+++
title = "View Metrics"
menuTitle = "4.2 View Metrics"
date = 2020-05-27T13:33:16-04:00
weight = 2
+++

Go to [CloudWatch Metrics page](https://console.aws.amazon.com/cloudwatch/home?#metricsV2:) and it should take you to a screen as shown below:

![Metrics home](/images/metrics/view-metrics-1.PNG?classes=shadow)

Now let's check out the metrics under the customer Namespace called [ContainerInsights](https://console.aws.amazon.com/cloudwatch/home?#metricsV2:graph=~();namespace=~'ContainerInsights). This Namespace contains all the metrics collected by CloudWatch Container Insights from the ECS clusters that have Container Insights enabled.

The following screen shows the different dimensions by which the metrics are being collected.
![Metrics2](/images/metrics/view-metrics-2.PNG?classes=shadow)

Select `ClusterName, ServiceName` dimension so we can take a look at the metrics there. To apply a filter, just click on the clustername on any of the rows and select `Add to search` which will apply a filter called `ClusterName="Services..."`. 

![Metrics3](/images/metrics/view-metrics-3.PNG?classes=shadow)

You can further filter on other columns as well by performing the same process as above on the other column. To add the data points to the chart, you can click on `Add to graph` in the context menu to graph a specific metric or `click` the checkbox.

![Metrics5](/images/metrics/view-metrics-5.PNG?classes=shadow)

You can change the metric statistic simply by clicking on the statistic type as shown below.

![Metrics6](/images/metrics/view-metrics-6.PNG?classes=shadow)

Container Insights captures metrics at 1 minute interval by default. In order to see granular data graphed, simply change the Period to `1 Minute` 

![Metrics7](/images/metrics/view-metrics-7.PNG?classes=shadow)


