+++
title = "View Metrics"
menuTitle = "10.1 View Metrics"
date = 2020-05-27T13:33:16-04:00
weight = 1
+++

Go to [CloudWatch Metrics page](https://console.aws.amazon.com/cloudwatch/home?#metricsV2:) and it should take you to a screen as shown below:

![Metrics home](/images/metrics/metrics1.png?classes=shadow)

Now let's check out the metrics under the customer Namespace called [ContainerInsights](https://console.aws.amazon.com/cloudwatch/home?#metricsV2:graph=~();namespace=~'ContainerInsights). This Namespace contains all the metrics collected by [CloudWatch Container Insights](/en/containerinsights.html) from the EKS clusters that have Container Insights enabled.

The following screen shows the different dimensions by which the metrics are being collected.
![Metrics2](/images/metrics/metrics2.png?classes=shadow)

Select `ClusterName, Namespace, PodName` dimension so we can take a look at the metrics there. If you had more than one EKS cluster in the account you will see all the cluster names listed there. To apply a filter and see only the `petsite` cluster, just click on the clustername on any of the rows and select `Add to search` which will apply a filter called `ClusterName="petsite"`. 

![Metrics3](/images/metrics/metrics3.png?classes=shadow)

You can further filter on other columns as well. For example, in the screenshot below we have applied a filter on `PodName` field as well.

![Metrics4](/images/metrics/metrics4.png?classes=shadow)

You can click on `Add to graph` in the context menu to graph a specific metric.

![Metrics5](/images/metrics/metrics5.png?classes=shadow)

You can change the metric statistic simply by clicking on the statistic type as shown below.

![Metrics6](/images/metrics/metrics6.png?classes=shadow)

Container Insights captures metrics at 1 minute interval by default. In order to see granular data graphed, simply change the Period to `1 Minute` 

![Metrics7](/images/metrics/metrics7.gif?classes=shadow)


