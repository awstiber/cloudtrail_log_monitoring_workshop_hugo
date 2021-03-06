+++
title = "Deploy application"
date = 2020-08-29T14:10:29-04:00
weight = 10
pre = "<b>▶︎ </b>"
hidden = true
+++

{{%notice warning%}}
You only need to use the instructions on this page if you want to deploy the front end site on EKS also. If you don't have any interest in EKS, skip this page and [go here](/installation/_using_the_app.html)
{{% /notice%}}

{{% notice info%}}
Cloud9 normally manages IAM credentials dynamically. This isn't currently compatible with the aws-iam-authenticator plugin, so we will disable it and rely on the IAM role instead.
{{% /notice %}}
- Return to your workspace and click the sprocket, or launch a new tab to open the `Preferences` tab
- Select `AWS SETTINGS`
- Turn off `AWS managed temporary credentials`
- Close the `Preferences` tab
![c9disableiam](/images/c9disableiam.png)

- To ensure temporary credentials aren't already in place we will also remove
any existing credentials file:
```
rm -vf ${HOME}/.aws/credentials
```

- Now close the Cloud9 session by closing the browser tab.

### Reboot the EC2 instance for changes to take effect

- Open [EC2 console](https://console.aws.amazon.com/ec2/v2/home?#Instances:tag:Name=aws-cloud9*;sort=desc:launchTime) and select the Cloud9 instance you just created.

- Go to `Actions > Instance State > Reboot` 

- Now go back to Cloud9 and open the IDE once again.

### Deploy Petsite front-end web app on EKS

On the Cloud9 terminal, execute the following command

```
cdk deploy EKS_Petsite
```

It will take a few minutes for the stack to be deployed, so just go get a <span style=font-size:40px> ☕️ </span>

### Update kubeconfig

Execute the following commands. 

{{%notice info%}}
This will return a command which you need to execute on the Cloud9 console. Doing this will update the kubeconfig file in your Cloud9 environment so you can perform operations on your cluster using kubectl.
This is a critical step. Make sure you actually execute the command generated as part of the result. You will not able to successfully deploy the application on the EKS cluster if you do not follow the instructions properly.
{{% /notice%}}

```bash
STACK_NAME=$(aws ssm get-parameter --name '/petstore/stackname' --region $AWS_REGION | jq .Parameter.Value -r)

aws cloudformation describe-stacks  --stack-name $STACK_NAME | jq '.Stacks[0].Outputs[] | select(.OutputKey | contains("ConfigCommand")).OutputValue' -r                                               
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