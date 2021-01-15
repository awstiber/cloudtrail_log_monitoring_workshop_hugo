+++
title = "Cloud9 환경 설정"
menuTitle = "Cloud9 설정"
date = 2020-08-31T13:25:50-04:00
weight = 20
pre = "<b>▶︎ </b>"
+++

새 Cloud9 인스턴스를 사용하여 애플리케이션을 배포 할 수 있습니다. 만약 Cloud9을 사용하지 않으려면 이 단계를 건너뛰고 [다음 단계로 이동하세요](/ko/installation/_deploy_app.html)


{{%notice warning%}}
EKS 모니터링을 탐색하려는 경우 EKS에 애플리케이션의 일부를 설치하도록 선택할 수 있습니다. 계속하기 전에 EKS를 지원하는 AWS 리전에 있는지 확인하십시오. [여기에서 확인 할 수 있습니다.](https://aws.amazon.com/about-aws/global-infrastructure/regional-product-services/).
{{% /notice%}}


#### 다음 명령을 실행하여 Cloud9 환경을 만듭니다.

```
curl -O https://raw.githubusercontent.com/aws-samples/one-observability-demo/main/cloud9-cfn.yaml

aws cloudformation create-stack --stack-name C9-Observability-Workshop --template-body file://cloud9-cfn.yaml --capabilities CAPABILITY_NAMED_IAM

```

