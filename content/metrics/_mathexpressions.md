+++
title = "Math expressions"
menuTitle = "10.2 Math expressions"
date = 2020-05-27T15:45:31-04:00
weight = 2
+++

Metric math enables you to query multiple CloudWatch metrics and use math expressions to create new time series based on these metrics. You can visualize the resulting time series on the CloudWatch console and add them to dashboards. Using AWS Lambda metrics as an example, you could divide the Errors metric by the Invocations metric to get an error rate. Then add the resulting time series to a graph on your CloudWatch dashboard.

{{%notice info%}}
Read all about Math expressions here - https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/using-metric-math.html
{{% /notice%}}

Now [Click here](https://console.aws.amazon.com/cloudwatch/home?#metricsV2:graph=~();query=~'*7bContainerInsights*2cClusterName*2cNamespace*2cPodName*7d) to see all the metrics under `ClusterName, Namespace, PodName` dimension under the `ContainerInsights` namespace on CloudWatch Metrics. Apply filters as shown below and graph all metrics.

![Metrics8](/images/metrics/metrics8.png?classes=shadow)

As seen above, the graph shows `pod_cpu_utilization` metric from 3 different pods that are all part of the Kubernetes data plane. Now, simply using a Metric Math expression, we can sum all the metrics and show them as a single line graph. 

See the GIF below on how you can do this. 

![Metrics9](/images/metrics/metrics9.gif?classes=shadow)

Doing this creates a new expression which uses the Metric Match function called `SUM()`. When you supply `METRICS()` as an argument to the `SUM()` expression, it aggregates all the metrics that are graphed except the ones that are math expressions.

You can also achieve the same result without using the `METRICS()` function as shown below, where m1,m2 and m3 being the metric Ids.

```
SUM([m1,m2,m3])
```

![Metrics10](/images/metrics/metrics10.png?classes=shadow)

Notice that you can also create CloudWatch Alarms based on Metric math expressions.

There are numerous such expressions available for you to operate on Metric data. 

To see all expressions simply click on `Metric expression` and check out the various functions available.

![Metrics11](/images/metrics/metrics11.png?classes=shadow)









