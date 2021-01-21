+++
title = "Install Petsite on EKS"
menuTitle = "2.5 Install Petsite on EKS"
date = 2020-09-02T11:58:20-04:00
weight = 35
hidden=true
+++

{{% notice info%}}
This section only applies if you chose to install the petsite front end on EKS in the previous step. If you did not do that, skip this page and [go here](/installation/_using_the_app.html)
{{% /notice%}}

### Update kubeconfig

Execute the following commands to update kubeconfig so you can interact with the EKS cluster

```bash
STACK_NAME=$(aws ssm get-parameter --name '/petstore/stackname' --region $AWS_REGION | jq .Parameter.Value -r)

$(aws cloudformation describe-stacks  --stack-name $STACK_NAME | jq '.Stacks[0].Outputs[] | select(.OutputKey | contains("ConfigCommand")).OutputValue' -r)                                               
```

After execution, your output should look like the below screenshot
![Kubeconfig](/images/eks_kubeconfig.png)


### Deploy PetSite EKS app

On the Terminal from the `/cdk/pet_stack/` directory enter the following command

```
chmod +x resources/eksdeployment.sh 
./resources/eksdeployment.sh    
```

Your result will be similar to the one below
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
If you receive error such as this, `jq: error (at <stdin>:35): Cannot iterate over null (null)`, it is because due to a race condition where creating the ELB took a bit longer than normal. Wait for 15 seconds and execute the `./eksdeployment.sh ` command again.
{{% /notice%}}

{{%notice warning%}}
Remember to [cleanup the resources](/_cleanup.html) once you are done with the workshop. Failing to do so will lead to unexpected usage charges.
{{% /notice%}}