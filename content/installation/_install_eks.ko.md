+++
title = "EKS에 Petsite 설치"
menuTitle = "2.4 EKS에 Petsite 설치"
date = 2020-09-02T11:58:20-04:00
weight = 35
+++

{{% notice info%}}
이 단계는 이전에서 EKS에 petsite Front-end를 설치하도록 선택 한 경우에만 진행합니다. 만약 EKS에 Front-end 구성을 하지 않는다면 [여기로 이동하세요](/ko/installation/_using_the_app.html)
{{% /notice%}}

### kubeconfig 업데이트

다음 명령을 실행하여 EKS 클러스터와 상호 작용할 수 있도록 kubeconfig를 업데이트합니다.

```bash
STACK_NAME=$(aws ssm get-parameter --name '/petstore/stackname' --region $AWS_REGION | jq .Parameter.Value -r)

$(aws cloudformation describe-stacks  --stack-name $STACK_NAME | jq '.Stacks[0].Outputs[] | select(.OutputKey | contains("ConfigCommand")).OutputValue' -r)                                               
```

실행 후 출력은 아래 스크린 샷과 같아야합니다.
![Kubeconfig](/images/eks_kubeconfig.png)

### Deploy PetSite EKS app

터미널의 `/cdk/pet_stack/`디렉토리에서 아래 명령을 입력하십시오.
```
chmod +x resources/eksdeployment.sh 
./resources/eksdeployment.sh    
```

결과는 다음과 유사합니다.
```
❯ ./eksdeployment.sh
-----------------------------------------------------------------
This script deploys petsite and the xray daemon to the EKS cluster
-----------------------------------------------------------------
deployment.apps/petsite-deployment created
service/service-petsite created
serviceaccount/xray-daemon created
clusterrolebinding.rbac.authorization.k8s.io/xray-daemon created
daemonset.apps/xray-daemon created
configmap/xray-config created
service/xray-service created
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 15552  100 15552    0     0  16627      0 --:--:-- --:--:-- --:--:-- 16615
namespace/amazon-cloudwatch created
serviceaccount/cloudwatch-agent created
clusterrole.rbac.authorization.k8s.io/cloudwatch-agent-role created
clusterrolebinding.rbac.authorization.k8s.io/cloudwatch-agent-role-binding created
configmap/cwagentconfig created
daemonset.apps/cloudwatch-agent created
configmap/cluster-info created
serviceaccount/fluentd created
clusterrole.rbac.authorization.k8s.io/fluentd-role created
clusterrolebinding.rbac.authorization.k8s.io/fluentd-role-binding created
configmap/fluentd-config created
daemonset.apps/fluentd-cloudwatch created
----- Creating SSM Parameter -----
----- ✅ DONE --------
```

{{%notice tip%}}
`jq : error (at <stdin>:35): Cannot iterate over null (null)`과 같은 오류가 생길수도 있습니다. ELB를 생성할때 레이스컨디션이 발생했기 때문입니다. 15초 정도 기다렸다가 `./eksdeployment.sh`명령을 다시 실행하십시오.
{{% /notice%}}

{{%notice warning%}}
워크샵을 마친 후에는 [리소스 정리](/ko/_cleanup.html)를 잊지마십시오. 그렇지 않으면 예상치못한 사용 요금이 발생합니다.
{{% /notice%}}