+++
title = "Deploy PetAdoptions app"
menuTitle = "2.2 Deploy PetAdoptions app"
date = 2020-05-06T16:24:15-04:00
weight = 20
hidden = true

+++

### Synthesize CDK app

```
cdk synth
```
### Bootstrap CDK 

```
cdk bootstrap
```

### Deploy the stack

```
cdk deploy Services
```

It will take a few minutes for the stack to be deployed, so just go get a <span style=font-size:40px> ☕️ </span>

### Update kubeconfig

{{%notice info%}}
If you did not choose to use EKS by setting `petsite_on_eks=true` in `cdk.json` earlier, skip rest of the steps and go to the [next section](/installation/_install_traffic_gen.html) 
{{% /notice%}}

Execute the following commands to update kubeconfig so you can interact with the EKS cluster

```bash
STACK_NAME=$(aws ssm get-parameter --name '/petstore/stackname' --region $AWS_REGION | jq .Parameter.Value -r)

$(aws cloudformation describe-stacks  --stack-name $STACK_NAME | jq '.Stacks[0].Outputs[] | select(.OutputKey | contains("ConfigCommand")).OutputValue' -r)                                               
```

After execution, your output should look like the below screenshot
![Kubeconfig](/images/deployeks.png)


### Add IAM policies to EKS Node instance role
Go to [EC2 console](https://console.aws.amazon.com/ec2/v2/home) and select the EC2 instance that was created as part of the deployment. Look for the ones that are under the Services/Microservices VPC. Select the instance role and copy to clipboard (Cmd+C / Ctrl+C)

![Instance Role](/images/containerinsights/eks11.png)

Execute the following command after replacing the placeholder text with the copied Instance role name from the previous step

```
ROLE=<REPLACE_THIS_WITH_THE_COPIED_INSTANCE_IAM_ROLE_NAME>
```

Execute the following commands to add IAM roles required for the workshop

```
aws iam attach-role-policy --role-name $ROLE --policy-arn arn:aws:iam::aws:policy/AWSXRayDaemonWriteAccess
aws iam attach-role-policy --role-name $ROLE --policy-arn arn:aws:iam::aws:policy/CloudWatchAgentServerPolicy
aws iam attach-role-policy --role-name $ROLE --policy-arn arn:aws:iam::aws:policy/AmazonSSMFullAccess
aws iam attach-role-policy --role-name $ROLE --policy-arn arn:aws:iam::aws:policy/AmazonSQSFullAccess
aws iam attach-role-policy --role-name $ROLE --policy-arn arn:aws:iam::aws:policy/AmazonSNSFullAccess
```

### Deploy PetSite EKS app

{{% notice info%}}
Skip this step if you did not  set the `petsite_on_eks` setting on `cdk.json` file to true in the previous section.
{{% /notice %}}

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
Remember to [cleanup the resources](/_cleanup.html) once you are done with the workshop. Failing to do so will lead to usage charges.
{{% /notice%}}
