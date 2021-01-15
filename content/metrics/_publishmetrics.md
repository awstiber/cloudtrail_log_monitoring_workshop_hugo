+++
title = "Publish custom Metrics"
menuTitle = "10.4 Publish custom Metrics"
date = 2020-05-28T21:55:40-04:00
weight = 4
+++

You can publish your own custom metrics in a variety of ways

- [AWS CLI](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/publishingMetrics.html)
- [put-metric-data](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/put-metric-data.html) API call
- [Embedded Metric Format](/en/emf.html)

The example below uses the AWS CLI to publish a metric called `throttlecount` under the dimension `ServerName,ServerType` to the `observabilityworkshop` namespace.

```
aws cloudwatch put-metric-data --metric-name throttlecount --namespace observabilityworkshop --unit Count --value 156 --dimensions ServerName=DemoServer,ServerType=A1
```
Open a terminal window and paste the above command and press enter. This should publish the metric to CloudWatch Metrics.

Once completed, go to [CloudWatch Metrics](https://console.aws.amazon.com/cloudwatch/home?#metricsV2:graph=~();query=~'*7bobservabilityworkshop*2cServerName*2cServerType*7d) which will directly take you to the metric you just published.

![Metrics13](/images/metrics/metrics13.png?classes=shadow)
