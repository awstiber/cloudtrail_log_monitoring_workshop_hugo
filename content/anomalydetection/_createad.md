+++
title = "Setup Anomaly Detection"
menuTitle = "5.1 Setup Anomaly Detection"
date = 2020-05-28T23:15:11-04:00
weight = 1
+++

When you enable anomaly detection for a metric, CloudWatch applies statistical and machine learning algorithms. These algorithms continuously analyze metrics of systems and applications, determine normal baselines, and surface anomalies with minimal user intervention.

The algorithms generate an anomaly detection model. The model generates a range of expected values that represent normal metric behavior.

### Setup Anomaly Detection on a CloudWatch Metric

Click this [link](https://console.aws.amazon.com/cloudwatch/home?region=us-east-1#metricsV2:graph=~(view~'timeSeries~stacked~false~region~'us-east-1~stat~'Average~period~300);query=~'), which will take you to the Metrics console page. Select the `CloudTrail` namespace that you previously created and drill down until you get to your metrics. It should look like something similar to the below picture depending on how many Metric Filters you created. 


![AD1](/images/ad/AD-1.PNG?classes=shadow)

Now go to the `Graphed metrics` tab and click on the Anomaly Detection icon as shown in the picture below. Make sure that the statistic is set to `Sum`

![AD2](/images/ad/AD-2.PNG?classes=shadow)

Anomaly Detection (AD) gets enabled immediately. A model is created based on the metric data points for a 2 week period. AD can also be enabled even if there is no data available for 2 week period. If you see an exclamation point next to the `(expected)` chart legend, that means the model does not have enough data points to create a band. You can come back to this at a later time to see the band. You can also use one of the Metrics within thr `ECS/ContainerInsights` namespace as that should have more data available. 

![AD3](/images/ad/AD-3.PNG?classes=shadow)

### Modifying the Anomaly Detection expression

Notice the expression below in the `Details` tab:

```
ANOMALY_DETECTION_BAND(m1, 2)
```

This indicates that AD has been enabled for the metric with Id `m1` with a standard deviation of 2 as default. You can also adjust the standard deviation to give more wiggle room for the metric datapoint if desired. Simply edit the expression as shown below.

![AD4](/images/ad/AD-4.PNG?classes=shadow)

### Creating a model programmatically

You can use `put-anomaly-detector` CLI command to create an Anomaly Detection model on a metric to create programmatically. 

If you deployed petsite on ECS, the following command creates an AD model on the metric `S3-Bucket-Access-Denied` in the `CloudTrail` namespace. 

```
aws cloudwatch put-anomaly-detector --namespace CloudTrail --metric-name S3-Bucket-Access-Denied --stat Sum --dimensions Name=CloudTrail,Value=S3
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
            "Namespace": "CloudTrail",
            "MetricName": "S3-Bucket-Access-Denied",
            "Dimensions": [
                {
                    "Name": "CloudTrail",
                    "Value": "S3"
                }
            ],
            "Stat": "Sum",
            "Configuration": {
                "ExcludedTimeRanges": []
            },
            "StateValue": "PENDING_TRAINING"
        },
        {
            "Namespace": "CloudTrail",
            "MetricName": "S3-Bucket-Access-Denied",
            "Dimensions": [],
            "Stat": "Sum",
            "Configuration": {
                "ExcludedTimeRanges": []
            },
            "StateValue": "TRAINED_INSUFFICIENT_DATA"
        },
        {
            "Namespace": "CloudTrail",
            "MetricName": "Access-Denied",
            "Dimensions": [],
            "Stat": "Sum",
            "Configuration": {
                "ExcludedTimeRanges": []
            },
            "StateValue": "TRAINED_INSUFFICIENT_DATA"
        },
        {
            "Namespace": "CloudTrail",
            "MetricName": "Access-Denied",
            "Dimensions": [],
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

![AD5](/images/ad/AD-5.PNG?classes=shadow)

To delete a model, simply click on `Delete model`