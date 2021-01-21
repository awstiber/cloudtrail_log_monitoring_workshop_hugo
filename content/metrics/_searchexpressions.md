+++
title = "Search expressions"
menuTitle = "4.4 Search expressions"
date = 2020-05-28T14:41:13-04:00
weight = 4
+++

Search expressions are a type of math expression that you can add to CloudWatch graphs. Search expressions enable you to quickly add multiple related metrics to a graph. They also enable you to create dynamic graphs that automatically add appropriate metrics to their display, even if those metrics don't exist when you first create the graph.

For example, you can create a search expression that displays the AWS/EC2 CPUUtilization metric for all instances in the Region. If you later launch a new instance, the CPUUtilization of the new instance is automatically added to the graph.

If you aren't already on the Metrics chart screen, please [Click here](https://console.aws.amazon.com/cloudwatch/home?region=us-east-1#metricsV2:graph=~(view~'timeSeries~stacked~false~region~'us-east-1~stat~'Average~period~300);query=~'*7bECS*2fContainerInsights*2cClusterName*2cServiceName*7d) to see all the metrics under `ECS/Container Insights` namespace.

{{%notice info%}}
Learn more about Search expression syntax here - https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/using-search-expressions.html
{{% /notice%}}

{{%notice info%}}
The below examples will use the ECS Container Metric data, but the same process can be applied to Metric Filters
{{% /notice%}}

Search expression syntax

```
SEARCH(' {Namespace, DimensionName1, DimensionName2, ...} SearchTerm', 'Statistic', Period)
```

Example search expression

```
SUM(SEARCH('{ECS/ContainerInsights,ClusterName} MetricName="TaskCount"', 'Average', 300))
```
### Fetching a specific metric under a dimension

Take a look at the following Search expression. This will select the metric called `pod_cpu_utilization` under `ClusterName, Namespace, PodName` dimension from the `ContainerInsights` namespace.

```
SEARCH('{ECS/ContainerInsights,ClusterName}  MetricName="CpuUtilized" ', 'Average', 300)
```

This is extremely useful in high cardinality scenarios like containers where you might be deploying new application pods whose name you may not be aware while creating a dashboard widget. When you use Search expressions like the one above, the metric gets automatically added to the graph as soon as the criteria is matched.

### Searching across metrics, dimensions and namespaces

You could also use an expression such as the one below. This will show the metric `pod_cpu_utlization` from all dimensions in the Namespace along with matching namespaces, dimensions that match the string.

```
SEARCH(' "CpuUtilized" ','Average',120)
```

![Metrics12](/images/metrics/Search-Expressions-1.PNG?classes=shadow)


### Using partial matches

The following search expression fetches values of MetricNames that contain the string `pod_cpu` under the dimension mentioned within `{}` curly braces.

```
SEARCH(' {ECS/ContainerInsights,ClusterName} MetricName=cpu ','Average',120)
```