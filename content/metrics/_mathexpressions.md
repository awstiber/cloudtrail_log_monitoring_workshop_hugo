+++
title = "Math expressions"
menuTitle = "4.3 Math expressions"
date = 2020-05-27T15:45:31-04:00
weight = 3
+++

Metric math enables you to query multiple CloudWatch metrics and use math expressions to create new time series based on these metrics. You can visualize the resulting time series on the CloudWatch console and add them to dashboards. Using AWS Lambda metrics as an example, you could divide the Errors metric by the Invocations metric to get an error rate. Then add the resulting time series to a graph on your CloudWatch dashboard.

{{%notice info%}}
Read all about Math expressions here - https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/using-metric-math.html
{{% /notice%}}


{{%notice info%}}
The below examples will use the ECS Container Metric data, but the same process can be applied to Metric Filters
{{% /notice%}}

Now [Click here](https://console.aws.amazon.com/cloudwatch/home?region=us-east-1#metricsV2:graph=~(view~'timeSeries~stacked~false~region~'us-east-1~stat~'Average~period~300);query=~'*7bECS*2fContainerInsights*2cClusterName*2cServiceName*7d) to see all the metrics under `ECS/Container Insights` namespace. Apply filters as shown below and graph all metrics.

![MathExpressions-1](/images/metrics/Math-Expressions-1.PNG?classes=shadow)

As seen above, the graph shows `CpuUtilized` metric from 4 different containers. Now, simply using a Metric Math expression, we can sum all the metrics and show them as a single line graph. 

See the picture below on how you can do this. 

![MathExpressions-2](/images/metrics/Math-Expressions-2.PNG?classes=shadow)

Doing this creates a new expression which uses the Metric Match function called `SUM()`. When you supply `METRICS()` as an argument to the `SUM()` expression, it aggregates all the metrics that are graphed except the ones that are math expressions.

You can also achieve the same result without using the `METRICS()` function as shown below, where m1,m2 and m3 being the metric Ids.

```
SUM([m1,m2,m3,m4])
```

![MathExpressions-4](/images/metrics/Math-Expressions-4.PNG?classes=shadow)

Notice that you can also create CloudWatch Alarms based on Metric math expressions.

There are numerous such expressions available for you to operate on Metric data. 

To see all expressions simply click on `Metric expression` and check out the various functions available.

![MathExpressions-5](/images/metrics/Math-Expressions-5.PNG?classes=shadow)









