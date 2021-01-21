+++
title = "Deploy application"
menuTitle = "2.4 Deploy application"
date = 2020-08-31T14:02:35-04:00
weight = 30
+++

### Set up Cloud9 IAM permissions

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

### Install tools and clone code repo <span style="color: blue;">(skip this step if you're using Event Engine)</span>

This step will install all the necessary tools, utilities required and also will download the source code required for the workshop.

```
curl -sSL https://raw.githubusercontent.com/awstiber/cloudwatch-logs-code/main/PetAdoptions/envsetup.sh | bash -s stable
```

### We should configure our AWS CLI with our current AWS Region as default:

```bash
export ACCOUNT_ID=$(aws sts get-caller-identity --output text --query Account)
export AWS_REGION=$(curl -s 169.254.169.254/latest/dynamic/instance-identity/document | jq -r '.region')
echo "export ACCOUNT_ID=${ACCOUNT_ID}" | tee -a ~/.bash_profile
echo "export AWS_REGION=${AWS_REGION}" | tee -a ~/.bash_profile
aws configure set default.region ${AWS_REGION}
aws configure get default.region
```

### Validate environment settings

{{%notice info%}}
If you're not using Cloud9, ignore the IAM role validation result for Cloud9 and continue with the [Install CDK packages](#install-cdk-packages) step
{{%/notice%}} 

```bash
test -n "$AWS_REGION" && echo AWS_REGION is "$AWS_REGION" || echo AWS_REGION is not set

aws sts get-caller-identity --query Arn | grep observabilityworkshop-admin -q && echo "You're good. IAM role IS valid." || echo "IAM role NOT valid. DO NOT PROCEED."
```

When using Cloud9, if the IAM role is not valid, <span style="color: red;">**DO NOT PROCEED**</span>. Go back and confirm the steps on this page.

### Install CDK packages
Once cloned successfully, navigate to `pet_stack` folder using the following command

```
cd workshopfiles/one-observability-demo/PetAdoptions/cdk/pet_stack
```
Install all npm packages

```
npm install
```

### Bootstrap CDK 

{{%notice tip%}}
If you are not in the `pet_stack` folder go there by executing this - ```cd workshopfiles/one-observability-demo/PetAdoptions/cdk/pet_stack```
{{%/notice%}}

```
cdk bootstrap
```

### Deploy the stack


```
cdk deploy Services
```

It will take a few minutes for the stack to be deployed, so just go get a <span style=font-size:40px> ☕️ </span>

