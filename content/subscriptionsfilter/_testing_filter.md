+++
title = "Testing The Subscription Filter"
menuTitle = "8.2 Testing The Subscription Filter"
date = 2020-06-03T17:09:15-04:00
weight = 2
+++

### Testing out your subscription filter

To test out your subscription filter, click [here](https://s3.console.aws.amazon.com/s3/home?region=us-east-1) to go to the S3 Bucket console.

Create a bucket and immediately delete it afterwards. Within a couple of minutes you should see emails from both SNS topics in your inbox. 

![Subscription-Filter-11](/images/subscription/Subscription-Filter-11.PNG?classes=shadow)

If the emails do not arrive, try the below items: 
- Make sure that you confirmed both subcription emails
- Try creating and deleting the bucket again. Sometimes the creation/deletion notification may have just missed the completion of the subscription filter. 