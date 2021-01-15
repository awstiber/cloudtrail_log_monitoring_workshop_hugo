+++
title = "Prepare environment"
menuTitle = "2.1 Prepare environment"
date = 2020-05-06T16:24:15-04:00
weight = 10
hidden = true
+++

Follow the instructions in this section to prepare your CLI environment to deploy the PetAdoptions application. You can also use your own laptop and execute these commands on macOS, Linux Terminals or using [WSL on Windows](https://docs.microsoft.com/en-us/windows/wsl/install-win10).

#### Setup Cloud9 environment

{{% notice info%}}

Ad blockers, javascript disablers, and tracking blockers should be disabled for the Cloud9 domain, or connecting to the workspace might be impacted. Make sure to actually copy values from the Cloud9 terminal, as there is no copy-on-select!

{{% /notice %}}

{{%notice warning%}}
This workshop will optionally create an Amazon EKS cluster. If you are interested in using EKS, make sure you are in a region that supports EKS. See [supported regions](https://aws.amazon.com/about-aws/global-infrastructure/regional-product-services/).
{{% /notice%}}

- Create a [Cloud9 Environment](https://console.aws.amazon.com/cloud9/home?)
  - Select Create environment
  
- Name it `observabilityworkshop` and set the `Cost-saving setting` to `After four hours` then take all other defaults

- When it comes up, customize the environment by closing the `Welcome tab`
  and `Lower work area`, and opening a new `Terminal` tab in the main work area

- Your workspace should now look like this:
![cloud9](/images/cloud9.png)

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

### Set up Cloud9 IAM permissions
{{% notice info%}}
Some users have had issues with IAM role not getting assigned to Cloud9 when they leave an active session open. To avoid this issue, please close the browser tab if you still have Cloud9 open.
{{% /notice%}}

1. Follow [this deep link to create an IAM role with Administrator access.](https://console.aws.amazon.com/iam/home#/roles$new?step=review&commonUseCase=EC2%2BEC2&selectedUseCase=EC2&policies=arn:aws:iam::aws:policy%2FAdministratorAccess)
2. Confirm that `AWS service` and `EC2` are selected, then click `Next` to view permissions.
3. Confirm that `AdministratorAccess` is checked, then click `Next` to review and click `Next` again on the Tag page.
4. Enter `observabilityworkshop-admin` for the Name, and select `Create Role`
![createrole](/images/createrole.png)

1. Follow [this deep link to find your Cloud9 EC2 instance](https://console.aws.amazon.com/ec2/v2/home?#Instances:tag:Name=observabilityworkshop*;sort=desc:launchTime)
1. Select the instance, then choose `Actions > Instance Settings > Attach/Replace IAM Role`
![c9instancerole](/images/c9instancerole.png)
1. Choose `observabilityworkshop-admin` from the `IAM Role` drop down, and select `Apply`
![c9attachrole](/images/c9attachrole.png)

### Increase Cloud9 instance storage volume size

{{%notice warning%}}
IMPORTANT:
The stack creation process includes building several docker images on the local environment which requires considerable storage space. You need to increase the Cloud9 volume size so the stack can be deployed properly.
Ignore this step if you are not using Cloud9 and proceed to the next step.
{{% /notice%}}

Open [EC2 console](https://console.aws.amazon.com/ec2/v2/home?#Instances:tag:Name=aws-cloud9*;sort=desc:launchTime) and look for the instance whose Name begins with `aws-cloud9-` and copy the `Instance ID` from there as shown below:

![](/images/c9instance.png)

Now go to [EC2 Volumes](https://console.aws.amazon.com/ec2/v2/home?#Volumes:) and look for the volume attached with the Instance ID copied earlier. Select it and click on `Actions > Modify Volume`.

Enter `32` in the `Size` textbox, which will increase the Cloud9 instance volume size to 32 GB.

![](/images/c9volume.png)

Click `Modify` and wait a few seconds and refresh the Volume console to see the change. Now go back to Cloud9 and continue with the steps.

### Restart the EC2 instance for changes to take effect

Open [EC2 console](https://console.aws.amazon.com/ec2/v2/home?#Instances:tag:Name=aws-cloud9*;sort=desc:launchTime) and select the Cloud9 instance you just created.

Go to `Actions > Instance State > Stop` and wait for the `Instance state` to turn to `Stopped` state.

Again, `Actions > Instance State > Start` and wait for the `Instance state` to turn to `running` state.

Now go back to Cloud9 and open the IDE

### Install tools and clone code repo

This step will install all the necessary tools, utilities required and also will download the source code required for the workshop.

```
curl -sSL https://raw.githubusercontent.com/aws-samples/one-observability-demo/main/PetAdoptions/envsetup.sh | bash -s stable
```

We should configure our AWS CLI with our current AWS Region as default:

```bash
export ACCOUNT_ID=$(aws sts get-caller-identity --output text --query Account)
export AWS_REGION=$(curl -s 169.254.169.254/latest/dynamic/instance-identity/document | jq -r '.region')
```
Check if AWS_REGION is set to desired region

```bash
test -n "$AWS_REGION" && echo AWS_REGION is "$AWS_REGION" || echo AWS_REGION is not set
```

Let’s save these into bash_profile

```bash
echo "export ACCOUNT_ID=${ACCOUNT_ID}" | tee -a ~/.bash_profile
echo "export AWS_REGION=${AWS_REGION}" | tee -a ~/.bash_profile
aws configure set default.region ${AWS_REGION}
aws configure get default.region
```

### Validate the IAM role

Use the GetCallerIdentity CLI command to validate that the Cloud9 IDE is using the correct IAM role.

```bash
aws sts get-caller-identity --query Arn | grep observabilityworkshop-admin -q && echo "You're good. IAM role IS valid." || echo "IAM role NOT valid. DO NOT PROCEED."
```

If the IAM role is not valid, <span style="color: red;">**DO NOT PROCEED**</span>. Go back and confirm the steps on this page.

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
{{%notice warning%}}
The config file is critical for successful creation of the application stack. Please make sure you enter appropriate values.
{{% /notice%}}

Open the `/pet_stack/cdk.json` file from the explorer tab on the left side. 

See instructions in the below on each line and make sure you follow them correctly.

```bash
{
  "app": "npx ts-node app/pet_stack.ts",
  "context": {
    "vpc_cidr": "11.0.0.0/16",# Change this value if this CIDR range conflicts with your existing environment
        "snstopic_email": "someone@example.com", # Change this value to an email address you can access if you want to receive email notifications
    "rdsusername":"petadmin",
    "petsite_on_eks":"false"# Changing this to "true" will provision an EKS cluster and deploy the front-end app on it. Leave it to "false" to deploy all services on ECS Fargate clusters.
  }
}
```

{{%notice info%}}
If you want to deploy an EKS cluster, set the value of `petsite_on_eks` setting to `true`
{{% /notice%}}