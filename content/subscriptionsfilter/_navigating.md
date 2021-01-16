+++
title = "Creating a Subscription Filter"
menuTitle = "8.1 Creating a Subscription Filter"
date = 2020-06-04T15:07:47-04:00
weight = 1
+++

#### Creating a Subscription Filter Using AWS SAM

To create the subscription filter, we are going to be using AWS SAM. The AWS Serverless Application Model (SAM) is an open-source framework for building serverless applications. It provides shorthand syntax to express functions, APIs, databases, and event source mappings. With just a few lines per resource, you can define the application you want and model it using YAML. During deployment, SAM transforms and expands the SAM syntax into AWS CloudFormation syntax, enabling you to build serverless applications faster.


{{% button href="https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-getting-started.html" icon="fab fa-leanpub" icon="fab fa-leanpub" icon-position="right"  %}}Learn more about this AWS SAM...{{% /button %}}

To create a subscription filter, follow the below steps.

1. Go [here](https://console.aws.amazon.com/cloud9/home?region=us-east-1#) to access your Cloud9 Environment.
2. Click the `Open IDE` button
3. Click on the `Folder` icon on the top left
4. Right click on the top folder then click `New Folder`
5. Name is `SubscriptionFilter`

![Subscription-Filter-1](/images/subscription/Subscription-Filter-1.PNG?classes=shadow)

3. Once loaded click the `aws` logo on the top left
4. Click the `scroll` to the right of the aws logo
5. Click the `Create new SAM Application` menu item

![Subscription-Filter-2](/images/subscription/Subscription-Filter-2.PNG?classes=shadow)

6. Select `python3.8`
7. Select `AWS SAM Hello World` as the template
8. Select the `Subscription-Filter` for the workspace folder
9. Name the application `SubscriptionFilter`
10. Click the close button on the informational prompt

![Subscription-Filter-3](/images/subscription/Subscription-Filter-3.PNG?classes=shadow)

11. Click on the gear icon on the top right
12. Click on `AWS Settings`
13. If disabled, re-enable the `AWS managed temporary credentials`

![Subscription-Filter-4](/images/subscription/Subscription-Filter-4.PNG?classes=shadow)

14. Click [here](https://s3.console.aws.amazon.com/s3/home?region=us-east-1#) to go to the S3 Console
15. Create a bucket using a unique name
16. Click `Create bucket`

![Subscription-Filter-5](/images/subscription/Subscription-Filter-5.PNG?classes=shadow)

17. Go to the `app.py` file and copy and paste the below code into it replacing everything that is already present. **Make sure to enter your Account ID in the code below.** Save the file afterwards.

```python
import json
import gzip
import base64
import boto3

# import requests


def lambda_handler(event, context):
    client = boto3.client('sns')
    
    encoded_zipped_data = event['awslogs']['data']
    zipped_data = base64.b64decode(encoded_zipped_data)
    data = gzip.decompress(zipped_data)
    
    payload = json.loads(data)
    log_events = payload['logEvents']
    
    for log_event in log_events:
        message = log_event['message']
        
        print(message)
        
        messageJson = json.loads(message)
        
        if "errorCode" not in messageJson:
            if messageJson["eventName"] == "CreateBucket":
                response = client.publish(
                    TopicArn='arn:aws:sns:us-east-1:<Enter Account ID>:S3-Creation',
                    Message=message,
                    Subject='S3 Bucket Created',
                    MessageStructure='string'
                )
            elif messageJson["eventName"] == "DeleteBucket":
                response = client.publish(
                    TopicArn='arn:aws:sns:us-east-1:<Enter Account ID>:S3-Deletion',
                    Message=message,
                    Subject='S3 Bucket Deleted',
                    MessageStructure='string'
                )    
    return {
        "statusCode": 200,
        "body": json.dumps({
            "message": "hello worlds",
            # "location": ip.text.replace("\n", "")
        }),
    }
```

18. Go to the `template.yaml` file and replace the entire contents with the code below. **Make sure to enter your email and Log Group Name.** Finally, save the file. 

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Transform: AWS::Serverless-2016-10-31
Description: >
  SubscriptionFilter

  Sample SAM Template for SubscriptionFilter

# More info about Globals: https://github.com/awslabs/serverless-application-model/blob/master/docs/globals.rst
Globals:
  Function:
    Timeout: 60

Resources:
  HelloWorldFunction:
    Type: AWS::Serverless::Function # More info about Function Resource: https://github.com/awslabs/serverless-application-model/blob/master/versions/2016-10-31.md#awsserverlessfunction
    Properties:
      CodeUri: hello_world/
      Handler: app.lambda_handler
      Runtime: python3.8
      Policies:
        - SNSPublishMessagePolicy:
            TopicName: "S3-Creation"
        - SNSPublishMessagePolicy:
            TopicName: "S3-Deletion"
      Events:
        CWLog:
          Type: CloudWatchLogs # More info about API Event Source: https://github.com/awslabs/serverless-application-model/blob/master/versions/2016-10-31.md#api
          Properties:
            LogGroupName: '<Enter Log Grop Name>'
            FilterPattern: '{ $.eventName= "CreateBucket" || $.eventName= "DeleteBucket" }'
  S3CreateBucketSNS:  
    Type: AWS::SNS::Topic
    Properties: 
      Subscription: 
        - Protocol: email
          Endpoint: "<Enter Your Email>"
      TopicName: "S3-Creation"
  S3DeleteBucketSNS:  
    Type: AWS::SNS::Topic
    Properties: 
      Subscription: 
        - Protocol: email
          Endpoint: "<Enter Your Email>"
      TopicName: "S3-Deletion"

Outputs:
  # ServerlessRestApi is an implicit API created out of Events key under Serverless::Function
  # Find out more about other implicit resources you can reference within SAM
  # https://github.com/awslabs/serverless-application-model/blob/master/docs/internals/generated_resources.rst#api
  HelloWorldFunction:
    Description: "Hello World Lambda Function ARN"
    Value: !GetAtt HelloWorldFunction.Arn
  HelloWorldFunctionIamRole:
    Description: "Implicit IAM Role created for Hello World function"
    Value: !GetAtt HelloWorldFunctionRole.Arn
```

19. Click on the `aws` logo on the left hand side
20. Click the `scroll` icon to the right of the aws logo
21. Click on the `Deploy SAM Application` menu item

![Subscription-Filter-6](/images/subscription/Subscription-Filter-6.PNG?classes=shadow)

22. Select the `SubscriptionFilter` template

![Subscription-Filter-7](/images/subscription/Subscription-Filter-7.PNG?classes=shadow)

23. Select `US East (N. Virginia)`

![Subscription-Filter-8](/images/subscription/Subscription-Filter-8.PNG?classes=shadow)

24. Select the S3 Bucket that you created in steps 14-16

![Subscription-Filter-9](/images/subscription/Subscription-Filter-9.PNG?classes=shadow)

25. Type in a stack name and press the enter key

![Subscription-Filter-10](/images/subscription/Subscription-Filter-10.PNG?classes=shadow)

26. You should automatically receive an email asking you to confirm your subscription for both SNS topics. Please go ahead and confirm them. 