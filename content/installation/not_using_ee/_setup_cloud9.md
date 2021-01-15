+++
title = "Setup Cloud9 environment"
menuTitle = "Setup Cloud9"
date = 2020-08-31T13:25:50-04:00
weight = 20
pre = "<b>▶︎ </b>"
+++

You can use a new Cloud9 instance to deploy the application. If you do not wish to you use Cloud9, skip this page and [go to the next step](/en/installation/_deploy_app.html)


{{%notice warning%}}
You can choose to install a part of the application on EKS in case you want to explore EKS monitoring. Before you proceed, make sure you are in a AWS Region that supports EKS. See [supported regions](https://aws.amazon.com/about-aws/global-infrastructure/regional-product-services/).
{{% /notice%}}


#### Execute the following commands to create the Cloud9 environment

#### Prerequisites
* [Install and configure AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)
* Ensure the default profile has administrator access to the AWS account

```
curl -O https://raw.githubusercontent.com/aws-samples/one-observability-demo/main/cloud9-cfn.yaml

aws cloudformation create-stack --stack-name C9-Observability-Workshop --template-body file://cloud9-cfn.yaml --capabilities CAPABILITY_NAMED_IAM

```

