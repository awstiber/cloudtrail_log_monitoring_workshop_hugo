+++
title = "Contributor Insights in action"
menuTitle=" 9.1 Contributor Insights in action"
date = 2020-05-18T16:45:38-04:00
weight = 1
+++

Open your browser and navigate to [CloudWatch Contributor Insights](https://console.aws.amazon.com/cloudwatch/home?region=us-east-1#contributorinsights). For quick reference on Contributor Insights rules, you can view all the configurable parameters in the Wizard tab. For this example, I will use the Syntax tab. Click Create rule and select Syntax. Provide a Rule name, and copy-paste the JSON Rule body from the examples below.

![ci-1](/images/contributorinsights/ci-1.PNG?classes-shadow)

Please make sure to change the LogGroupNames to what you named your CloudTrail Log group.

```json
{
    "Schema": {
        "Name": "CloudWatchLogRule",
        "Version": 1
    },
    "AggregateOn": "Count",
    "Contribution": {
        "Filters": [
            {
                "Match": "$.eventName",
                "NotIn": [
                    "AssumeRole"
                ]
            }
        ],
        "Keys": [
            "$.eventName"
        ]
    },
    "LogFormat": "JSON",
    "LogGroupNames": [
        "CloudTrail/DefaultLogGroup"
    ]
}
```

The preceding rule aggregates the results on Count – the number of occurrences of each unique contributor. You can also aggregate on SUM to add the values of the field that you specify from the log event. The Contribution section defines how the rule filters the log events. In the LogGroupNames section, you specify which CloudWatch Log Group this rule applies to.

![ci-2](/images/contributorinsights/ci-2.PNG?classes-shadow)

The rule creates a time series of how the API calls are distributed over a 1-hour period. It shows metrics about the unique contributors and their usage. The console has built-in features to add this as a widget to CloudWatch Dashboard, or to view the detailed logs in CloudWatch Logs Insights, or to export the result to a CSV file.

### Monitoring API throttling

The following rule monitors the API operations that were throttled by filtering on the errorCode field from the CloudTrail logs. This is useful to detect the frequency of unique API throttles and who is making those API calls. You can click on View logs to further analyze the API and take remedial action.

Please make sure to change the LogGroupNames to what you named your CloudTrail Log group.

```json
{
    "Schema": {
        "Name": "CloudWatchLogRule",
        "Version": 1
    },
    "AggregateOn": "Count",
    "Contribution": {
        "Filters": [
            {
                "In": [
                    "AccessDenied",
                    "UnauthorizedOperation"
                ],
                "Match": "$.errorCode"
            }
        ],
        "Keys": [
            "$.userIdentity.arn",
            "$.eventName"
        ]
    },
    "LogFormat": "JSON",
    "LogGroupNames": [
        "CloudTrail/DefaultLogGroup"
    ]
} 
```

The following is a sample visualization for the rule:

![ci-3](/images/contributorinsights/ci-3.PNG?classes-shadow)
