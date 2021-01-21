+++
title = "CLEAN UP RESOURCES"
date = 2020-07-12T12:46:53-04:00
weight = 100
+++

## Delete the stack

#### 1. Delete the Traffic Generator Fargate task

If you followed [these steps](/installation/_install_traffic_gen.html) to create a traffic generator task, go back to the same cluster and simply stop the task which will kill the task immediately

#### 2. Delete all resources

From `/cdk/pet_stack/` directory execute the following commands within the Cloud9 IDE.

```
chmod +x ./resources/destroy_stack.sh
./resources/destroy_stack.sh
```

#### 3. Delete CloudFormation

Go to [CloudFormation](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks?filteringText=&filteringStatus=active&viewNested=true&hideStacks=false) and delete all of the stacks that show up in there. 

#### 4. Delete CloudTrail

Go to [CloudTrail](https://console.aws.amazon.com/cloudtrail/home?region=us-east-1) and delete the trail that you created at the begining of this workshop.

#### 5. Remove S3 Buckets

Go to the [S3 Console](https://s3.console.aws.amazon.com/s3/home?region=us-east-1) and empty then delete the below buckets
- services-s3bucketpetadoptionc....
- The bucket you created for the subscription filter
- The CloudTrail logs bucket-