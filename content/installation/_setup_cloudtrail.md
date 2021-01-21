+++
title = "Turn On CloudTrail"
menuTitle = "2.1 Turn on CloudTrail"
date = 2020-05-06T16:24:15-04:00
weight = 1
chapter = true
+++

Follow the below instructions to turn on CloudTrail for your account.

Click [here](https://console.aws.amazon.com/cloudtrail/home?region=us-east-1#/dashboard) to go to the CloudTrail Dashboard.

Ensure that the N. Virginia (US-East-1) is selected on the top right. Then click on the `Create trail` button.

![IMG-CloudTrail-1](/images/installation/CloudTrail-Step-1.PNG)

Now follow the below steps
1. Enter in your trail name
2. Enter in an AWS KMS Alias
2. Tick the `CloudWatch Logs` box
3. Enter in a value for your Log Group Name or keep the default
4. Create a new IAM Role
5. Name your new role
6. Click next

![IMG-CloudTrail-2](/images/installation/CloudTrail-Step-2.PNG)

7. Ensure that `Management Events` is ticked and click next

![IMG-CloudTrail-3](/images/installation/CloudTrail-Step-3.PNG)

8. Finally click on `Create trail` button

![IMG-CloudTrail-4](/images/installation/CloudTrail-Step-4.PNG)