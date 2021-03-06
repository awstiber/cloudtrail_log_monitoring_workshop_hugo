+++
title = "Setup Cloud9 environment"
menuTitle = "Setup Cloud9"
date = 2020-08-31T13:25:50-04:00
weight = 20
pre = "<b>▶︎ </b>"
+++

You can use a new Cloud9 instance to deploy the application. If you do not wish to you use Cloud9, skip this page and [go to the next step](/installation/_deploy_app.html)


{{%notice warning%}}
You can choose to install a part of the application on EKS in case you want to explore EKS monitoring. Before you proceed, make sure you are in a AWS Region that supports EKS. See [supported regions](https://aws.amazon.com/about-aws/global-infrastructure/regional-product-services/).
{{% /notice%}}


#### Execute the following commands to create the Cloud9 environment

#### Prerequisites
* [Go to AWS CloudShell](https://console.aws.amazon.com/cloudshell/home?region=us-east-1#)
* Ensure that you are on the N.Virginia Region (US-East-1)

![IMG-CloudShell](/images/installation/CloudShell.PNG)

Once the terminal loads, enter in the below commands

```
curl -O https://raw.githubusercontent.com/awstiber/cloudwatch-logs-code/main/cloud9-cfn.yaml

aws cloudformation create-stack --stack-name C9-CloudTrail-Log-Workshop --template-body file://cloud9-cfn.yaml --capabilities CAPABILITY_NAMED_IAM

```

Now please wait for the CloudFormation template to finish. You can view it by clicking [here](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks?filteringText=&filteringStatus=active&viewNested=true&hideStacks=false)
