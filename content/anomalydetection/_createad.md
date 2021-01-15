+++
title = "Setup Anomaly Detection"
menuTitle = "12.1 Setup Anomaly Detection"
date = 2020-05-28T23:15:11-04:00
weight = 1
+++

When you enable anomaly detection for a metric, CloudWatch applies statistical and machine learning algorithms. These algorithms continuously analyze metrics of systems and applications, determine normal baselines, and surface anomalies with minimal user intervention.

The algorithms generate an anomaly detection model. The model generates a range of expected values that represent normal metric behavior.

### Setup Anomaly Detection on a CloudWatch Metric

If you deployed petsite on EKS, go to [CloudWatch Metrics](https://console.aws.amazon.com/cloudwatch/home?#metricsV2:graph=~();query=~'`7bContainerInsights`2cClusterName`2cNamespace`2cPodName`7d`20MetricName`3d`22pod_cpu_utilization`22) and select graph the `pod_cpu_utilization` metric from the from PodName `petsite-deployment`. Your screen should look like the one below:

If you deployed the petsite on ECS, click this [link](https://console.aws.amazon.com/cloudwatch/home?#metricsV2:graph=~();query=~'*7bECS*2fContainerInsights*2cClusterName*7d), which will take you to the `ECS/ContainerInsights` namespace and selected the `CPUUtilized` metric under `ClusterName` namespace


![AD1](/images/ad/ad1.png?classes=shadow)

Now go to the `Graphed metrics` tab and click on the Anomaly Detection icon as shown in the picture below

![AD2](/images/ad/ad2.png?classes=shadow)

Anomaly Detection (AD) gets enabled immediately. A model is created based on the metric data points for a 2 week period. AD can also be enabled even if there is no data available for 2 week period.

![AD3](/images/ad/ad3.png?classes=shadow)

### Modifying the Anomaly Detection expression

Notice the expression below in the `Details` tab:

```
ANOMALY_DETECTION_BAND(m1, 2)
```

This indicates that AD has been enabled for the metric with Id `m1` with a standard deviation of 2 as default. You can also adjust the standard deviation to give more wiggle room for the metric datapoint if desired. Simply edit the expression as shown below.

![AD4](/images/ad/ad4.gif?classes=shadow)

### Creating a model programmatically

You can use `put-anomaly-detector` CLI command to create an Anomaly Detection model on a metric to create programmatically. 

If you deployed petsite on EKS, the following command creates an AD model on the metric `pod_cpu_utilization` in the `ContainerInsights` namespace under `ClusterName` dimension. If your clustername is different, replace `petsite` to the appropriate name.

```
 aws cloudwatch put-anomaly-detector --namespace ContainerInsights --metric-name pod_cpu_utilization --stat Average --dimensions Name=ClusterName,Value=petsite
```

If you deployed petsite on ECS, the following command creates an AD model on the metric `CpuUtilized` in the `ECS/ContainerInsights` namespace under `ClusterName` dimension. Make sure to enter one of the ECS clusters name in the `Value` parameter.

```
 aws cloudwatch put-anomaly-detector --namespace ECS/ContainerInsights --metric-name CpuUtilized --stat Average --dimensions Name=ClusterName,Value=<REPLACE_THE_ECS_CLUSTER_NAME_HERE>
```

{{%notice tip%}}
Learn about the syntax of this CLI command here - https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/put-anomaly-detector.html
{{% /notice%}}

### List all AD models in your account
Use the following command to list all AD models in your account. This will show details about the ADs in your account along with their training information
```
aws cloudwatch describe-anomaly-detectors
```

Executing this command should produce a result similar to the one below

```json
{
    "AnomalyDetectors": [
       {
            "Namespace": "ContainerInsights",
            "MetricName": "pod_cpu_utilization",
            "Dimensions": [
                {
                    "Name": "ClusterName",
                    "Value": "petsite"
                }
            ],
            "Stat": "Average",
            "Configuration": {
                "ExcludedTimeRanges": []
            },
            "StateValue": "TRAINED_INSUFFICIENT_DATA"
        }
    ]
}

```

### Editing and Deleting an existing model

To edit the model, click on `Edit model`, which will take you to the edit screen. Here, you can edit the model to exclude a specific time period from the model calculation. 
For example, if you have a deployment coming up and you expect the metrics during that time to affect the AD model, just add that duration to the excluded period, which will make AD ignore metrics during that timeframe.

![AD5](/images/ad/ad5.png?classes=shadow)

To delete a model, simply click on `Delete model`