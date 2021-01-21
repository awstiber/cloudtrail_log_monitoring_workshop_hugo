+++
title = "Alarms on Anomaly Detection band"
menuTitle = "6.2 Alarms on Anomaly Detection band"
date = 2020-06-07T10:55:04-04:00
weight = 2
+++

You can create an alarm based on CloudWatch anomaly detection, which mines past metric data and creates a model of expected values. The expected values take into account the typical hourly, daily, and weekly patterns in the metric.

You set a value for the anomaly detection threshold, and CloudWatch uses this threshold with the model to determine the "normal" range of values for the metric. A higher value for the threshold produces a thicker band of "normal" values.

You can choose whether the alarm is triggered when the metric value is above the band of expected values, below the band, or either above or below the band.

{{%notice tip%}}
Learn more about Anomaly Detection [here](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Create_Anomaly_Detection_Alarm.html).
{{% /notice%}}

### Create an Alarm based on Anomaly Detection band

Go to [CloudWatch Metrics](https://console.aws.amazon.com/cloudwatch/home?region=us-east-1#metricsV2:graph=~()). This will take you to the Metrics home page, where you will see all the Namespaces available in the account. Select `CloudTrail` namespace

![Alarms7](/images/alarms/alarms-7.PNG?classes=shadow)

- Inside the Namespace, you will see all the dimensions under which the metrics are aggregated under
- Click on `Metrics with no dimensions`
- Select one of the Metric Filters which you previously created.

![Alarms8](/images/alarms/alarms-8.PNG?classes=shadow)

- Go to `Graphed Metrics` tab and select the `Anomaly Detection` button as shown below

![Alarms9](/images/alarms/alarms-9.PNG?classes=shadow)

- Your screen should look like similar to the one below with the Anomaly Detection band immediately created 
![Alarms10](/images/alarms/alarms-10.PNG?classes=shadow)

- Now click on the create alarm button (🔔) which will take you to the screen similar to the one below

![Alarms11](/images/alarms/alarms-11.PNG?classes=shadow)

- Notice that the `Anomaly detection` option is selected. Under `Whenever S3-Bucket-Access-Denied is...` you can select any of the 3 options presented there. Select `Outside of the band`

- You can also select the standard deviation value under `Anomaly detection threshold` section. Change the value to see that the band around the metric timeline changing dynamically

- Expand `Additional configuration` where you can indicate how many occurences of the breach qualifies for the alarm to be triggered. Set the values to 2 out of 5, which will make the alarm get triggered if there has been 2 breaches in 5 evaluation periods. Notice the message at the top of the graph describing the setting as `This alarm will trigger when the blue line goes above the red line for 2 datapoints within 25 minutes.`

- Click `Next` 
- In the `Configure actions` screen, you can set what action you want to take when the alarm changes to different states such as 
    - In alarm
    - OK
    - Insufficient data

- The available options for actions include,
    - Send a notification to an SNS topic
    - Take an Auto scaling action
    - EC2 action if the metric is from an EC2 instance

Select `Create a new topic` to create a new SNS topic to send the notification to and provide your email address. 

![Alarms4](/images/alarms/alarms-4.PNG?classes=shadow)

- Click `Create topic` to create the SNS topic 
- Click `Next`, give the alarm a name and click `Next` again to review the configuration
- Click `Create` to create the alarm

### Navigating the Alarms screen

Once you have created the alarm, you will notice that the alarm is now in `Insufficient data` state which indicates that there is not enough data to validate the alarm. Waiting for 5 minutes will change the alarm state to `OK` in green.

![Alarms12](/images/alarms/alarms-12.PNG?classes=shadow)

Click on the alarm to see the alarm details as shown below. Notice the alarm state now changed to 'OK' after evaluation was complete.

![Alarms13](/images/alarms/alarms-13.PNG?classes=shadow)