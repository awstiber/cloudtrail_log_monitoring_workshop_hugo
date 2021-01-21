+++
title = "Publish custom Metrics"
menuTitle = "4.5 Publish custom Metrics"
date = 2020-05-28T21:55:40-04:00
weight = 5
+++

You can publish your own custom metrics in a variety of ways

- [AWS CLI](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/publishingMetrics.html)
- [AWS CloudShell](https://console.aws.amazon.com/cloudshell/home?region=us-east-1)
- [put-metric-data](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/put-metric-data.html) API call
- [Embedded Metric Format](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Embedded_Metric_Format.html)

The example below uses the AWS CLI/CloudShell to publish a metric called `throttlecount` under the dimension `ServerName,ServerType` to the `observabilityworkshop` namespace.

```
aws cloudwatch put-metric-data --metric-name throttlecount --namespace CloudTrail --unit Count --value 156 --dimensions ServerName=DemoServer,ServerType=A1
```
Open a terminal window and paste the above command and press enter. This should publish the metric to CloudWatch Metrics.

Once completed, go to [CloudWatch Metrics](https://console.aws.amazon.com/cloudwatch/home?region=us-east-1#metricsV2:graph=~();query=~'*7bCloudTrail*2cServerName*2cServerType*7d) which will directly take you to the metric you just published.

![Metrics13](/images/metrics/metrics13.png?classes=shadow)

{{%notice tip%}}
This can be used in conjunction with CloudWatch Log Subscription Filters to create more complicated metrics.
{{% /notice%}}