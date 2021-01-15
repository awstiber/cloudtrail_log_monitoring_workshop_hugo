+++
title = "애플리케이션 배포"
menuTitle = "2.3 애플리케이션 배포"
date = 2020-08-31T14:02:35-04:00
weight = 30
+++

### Cloud9 IAM 권한 설정

{{% notice info%}}
Cloud9는 일반적으로 IAM 자격 증명을 동적으로 관리합니다. 이는 현재 사용하는 aws-iam-authenticator플러그인과 호환되지않습니다. 그래서 우리는 이것을 비활성화하고 대신 IAM 역할을 사용할 것입니다.
{{% /notice %}}
- 작업 공간으로 돌아가서 톱니바퀴모양을 클릭하거나 새 탭을 실행하여 `Preferences`탭을 엽니다.
- `AWS SETTINGS`를 선택합니다.
- `AWS managed temporary credentials`을 해제합니다.
- `Preferences` 탭을 닫습니다.
![c9disableiam](/images/c9disableiam.png)

- 임시 자격 증명이 아직 없는지 확인하기 위해 기존 자격 증명 파일도 제거합니다.
```
rm -vf ${HOME}/.aws/credentials
```

### 도구 설치 및 코드 저장소 복제 <span style="color: blue;">(Event Engine을 사용하는 경우이 단계를 건너 뜁니다.)</span>

이 단계는 필요한 모든도구, 유틸리티를 설치하고 워크숍에 필요한 소스코드를 다운로드합니다.

```
curl -sSL https://raw.githubusercontent.com/aws-samples/one-observability-demo/main/PetAdoptions/envsetup.sh | bash -s stable
```

### AWS CLI를 구성할 때는 현재 리전을 기본값으로 사용해야합니다.

```bash
export ACCOUNT_ID=$(aws sts get-caller-identity --output text --query Account)
export AWS_REGION=$(curl -s 169.254.169.254/latest/dynamic/instance-identity/document | jq -r '.region')
echo "export ACCOUNT_ID=${ACCOUNT_ID}" | tee -a ~/.bash_profile
echo "export AWS_REGION=${AWS_REGION}" | tee -a ~/.bash_profile
aws configure set default.region ${AWS_REGION}
aws configure get default.region
```

### 환경 설정 확인

{{%notice info%}}
Cloud9를 사용하지 않는 경우 해당 단계를 건너뛰고 [Install CDK packages](#install-cdk-packages) 단계로 갑니다.
{{%/notice%}} 

```bash
test -n "$AWS_REGION" && echo AWS_REGION is "$AWS_REGION" || echo AWS_REGION is not set

aws sts get-caller-identity --query Arn | grep observabilityworkshop-admin -q && echo "You're good. IAM role IS valid." || echo "IAM role NOT valid. DO NOT PROCEED."
```

Cloud9를 사용할 때 IAM 역할이 유효하지 않으면 <span style="color: red;">**진행하지 마십시오!!**</span>. 다시 돌아가서 이 페이지의 각각단계를 자세히 확인하십시오.

### CDK 패키지 설치
복사가 완료되면 다음 명령을 사용하여 `pet_stack` 폴더로 이동합니다.

```
cd workshopfiles/one-observability-demo/PetAdoptions/cdk/pet_stack
```
모든 npm 패키지 설치

```
npm install
```

### config 매개변수 편집
{{%notice info%}}
이것은 옵션입니다. 필요한 경우에만 구성 파일의 값을 변경하십시오. 각 라인의 주석을 확인하고 진행하세요.
{{% /notice%}}

왼쪽의 탐색기 탭에서`/pet_stack/cdk.json` 파일을 엽니다.

아래의 각 줄에있는 지침을 참조하고 올바르게 따르십시오.

{{%notice info%}}
EKS 클러스터를 배포하려면 `petsite_on_eks` 설정 값을`true`로 설정합니다.
{{% /notice%}}

```bash
{
  "app": "npx ts-node app/pet_stack.ts",
  "context": {
    "vpc_cidr": "11.0.0.0/16",# Change this value if this CIDR range conflicts with your existing environment
        "snstopic_email": "someone@example.com", # Change this value to an email address you can access if you want to receive email notifications. There will be 1000s of emails with increased traffic. So be warned. You can also leave the default value as it is to avoid getting emails.
    "rdsusername":"petadmin",
    "petsite_on_eks":"false"# Changing this to "true" will provision an EKS cluster and deploy the front-end app on it. Leave it to "false" to deploy all services on ECS Fargate clusters.
  }
}
```

### 변경 사항을 적용하려면 EC2 인스턴스를 재부팅하십시오.<span style="color: blue;">(EKS에 petsite를 배포하지 않는 경우이 단계를 건너 뜁니다.)</span>	

Cloud9 터미널에서 다음 명령을 실행하고 Cloud9 인스턴스가 재부팅 될 때까지 몇 초 동안 기다립니다.
```
sudo reboot
```

### Bootstrap CDK 

{{%notice tip%}}
`pet_stack` 폴더에 있지 않다면 다음을 실행하여 이동하십시오-```cd workshopfiles/one-observability-demo/PetAdoptions/cdk/pet_stack```
{{%/notice%}}

```
cdk bootstrap
```

### 스택 배포

{{%notice warning%}}
EKS를 선택한 경우 이 [버그](https://github.com/aws/aws-cdk/issues/9027)로 인해 첫번재 배포는 실패하게됩니다. 그러나 명령을 다시 실행하면 성공합니다.
{{% /notice%}}

```
cdk deploy Services
```

스택을 배포하는데는 수 분이 걸리므로 잠시 <span style=font-size:40px> ☕️ </span>한잔 하고 오도록 하겠습니다.

