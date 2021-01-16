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
curl -sSL https://raw.githubusercontent.com/aws-samples/one-observability-demo/main/PetAdoptions/envsetup.sh | bash -s stable
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

### Edit config parameters
{{%notice info%}}
This is an optional step. Only change the values in the config file if needed. See comments on each line and make a call.
{{% /notice%}}

Open the `/pet_stack/cdk.json` file from the explorer tab on the left side. 

See instructions in the below on each line and make sure you follow them correctly.

{{%notice info%}}
If you want to deploy an EKS cluster, set the value of `petsite_on_eks` setting to `true`
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

### Reboot the EC2 instance for changes to take effect <span style="color: blue;">(skip this step if you're not deploying petsite on EKS)</span>	

Execute the following command on the Cloud9 terminal and wait a few seconds for the Cloud9 instance to reboot

```
sudo reboot
```

### Bootstrap CDK 

{{%notice tip%}}
If you are not in the `pet_stack` folder go there by executing this - ```cd workshopfiles/one-observability-demo/PetAdoptions/cdk/pet_stack```
{{%/notice%}}

```
cdk bootstrap
```

### Deploy the stack

{{%notice warning%}}
If you chose EKS, the deployment fails for the first time due to this [bug](https://github.com/aws/aws-cdk/issues/9027), but just re-execute the command again and it will succeed.
{{% /notice%}}

```
cdk deploy Services
```

It will take a few minutes for the stack to be deployed, so just go get a <span style=font-size:40px> ☕️ </span>

