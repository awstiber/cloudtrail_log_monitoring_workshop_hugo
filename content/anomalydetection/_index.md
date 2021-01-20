+++
title = "Anomaly Detection"
date = 2020-05-19T10:19:20-04:00
weight = 60
chapter = true
pre = "<b>6. </b>"
+++

When you enable anomaly detection for a metric, CloudWatch applies machine learning algorithms to the metric's past data to create a model of the metric's expected values. The model assesses both trends and hourly, daily, and weekly patterns of the metric. The algorithm trains on up to two weeks of metric data, but you can enable anomaly detection on a metric even if the metric does not have a full two weeks of data.

{{% button href="https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Anomaly_Detection.html" icon="fab fa-leanpub" icon="fab fa-leanpub" icon-position="right"  %}}Learn more about this topic...{{% /button %}}

