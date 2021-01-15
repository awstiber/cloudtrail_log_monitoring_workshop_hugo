+++
title = "Create database in RDS"
menuTitle = "2.3 Create Database in RDS"
date = 2020-07-08T11:57:52-04:00
weight = 30
hidden = true
+++

As part of the CDK deployment, a SQL Server RDS database instance was created. In this exercise you will be creating a SQL database inside the RDS instance and make modifications to Security Groups to allow traffic from the microservices

#### Create a Windows EC2 instance to setup the database
Choose a  free tier eligible instance as shown below and make sure you create it on a public subnet in the same VPC we created as part of the CDK deployment
![Windows AMI](/images/rdssetup/windowsami.png)

Add the Security Group associated with the newly created Windows EC2 instance to the RDS instance's Security Group's Inbound rules to allow `MS SQL` traffic.

Open the browser and install SQL Server Management Studio using this link - https://aka.ms/ssmsfullsetup 

Once installed, launch it by typing `SSMS` in the `Start menu` as shown below

![SSMS](/images/rdssetup/ssms.png)

Login using credentials from the `/cdk/pet_stack/cdk.json` file. 

Get the server name by executing the following command

```
aws ssm get-parameter --name '/petstore/rdsconnectionstring' | jq  -r '.Parameter.Value | split(";")[0] | split("=")[1]'
```

![SQL Server Login](/images/rdssetup/dblogin.png)

Once logged in, click on `New Query` and copy the contents of `/cdk/pet_stack/resources/rds_sqlserver.sql` file and paste into the window. 

Select `Execute`

This will create the database for our application as shown below

![DB Created](/images/rdssetup/dbcreated.png)

{{%notice warning%}}

Once the database is created, remember to go back to EC2 console and terminate the Windows instance you just created. Not doing so will result in usage charges.

{{% /notice%}}