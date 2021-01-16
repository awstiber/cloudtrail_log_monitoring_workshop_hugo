+++
title = "Metrics Filters"
menuTitle = "10.1 Metrics Filters"
date = 2020-05-27T15:45:31-04:00
weight = 1
+++

After the CloudWatch Logs agent begins publishing log data to Amazon CloudWatch, you can begin searching and filtering the log data by creating one or more metric filters. Metric filters define the terms and patterns to look for in log data as it is sent to CloudWatch Logs. CloudWatch Logs uses these metric filters to turn log data into numerical CloudWatch metrics that you can graph or set an alarm on. You can use any type of CloudWatch statistic, including percentile statistics, when viewing these metrics or setting alarms.

{{%notice info%}}
Read all about Metrics Filters here - https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/MonitoringLogData.html
{{% /notice%}}

To create a Metric Filter, follow the below steps

1. Go to your [Log Groups](https://console.aws.amazon.com/cloudwatch/home?region=us-east-1#logsV2:log-groups)
2. Click on the CloudTrail log group and go to the `Metric Filters` tab

![Metrics-Filter-1](/images/metrics/Metrics-Filter-1.PNG?classes=shadow)

3. Click on the `Create Metric Filter` button
4. At the top enter in the filter pattern you want. You can use the below one for this example

```
{ $.eventName= "CreateBucket" && $.errorCode = "AccessDenied" && $.sourceIPAddress != "123.225.145.36"}
```
5. Select the CloudTrail log data to test and select `Test pattern`

![Metrics-Filter-2](/images/metrics/Metrics-Filter-2.PNG?classes=shadow)

6. If it doesn't error out, please click next.
7. Enter in Filter Name
8. You can also enter in a Metric Namespace. [Click here](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_concepts.html#Namespace) to learn more.
9. Enter in the Metric Name
10. For Metric value, please enter in the amount you would like the Metric to be increased by with each occurance within the logs.
11. For default value, please enter in the amount you would like the Metric to be increase when there is **no** match
12. Click `Next` 

![Metrics-Filter-3](/images/metrics/Metrics-Filter-3.PNG?classes=shadow)

13. Click `Create metric filter`

![Metrics-Filter-4](/images/metrics/Metrics-Filter-4.PNG?classes=shadow)
