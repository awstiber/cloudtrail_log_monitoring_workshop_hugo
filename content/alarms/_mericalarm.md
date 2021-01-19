+++
title = "Metric Alarms"
menuTitle = "14.1 Metric Alarms"
date = 2020-06-07T09:42:26-04:00
weight = 1
+++

A metric alarm watches a single CloudWatch metric or the result of a math expression based on CloudWatch metrics. The alarm performs one or more actions based on the value of the metric or expression relative to a threshold over a number of time periods. The action can be an Amazon EC2 action, an Amazon EC2 Auto Scaling action, or a notification sent to an Amazon SNS topic.

### Create a Metric Alarm

- Go to [CloudWatch Alarms](https://console.aws.amazon.com/cloudwatch/home?#alarmsV2:) and click on `Create alarm`
- Click `Select metric`
- This will take you to the Metrics home page, where you will see all the Namespaces available in the account. Select `ContainerInsights` or `ECS/ContainerInsights` namespace

![Alarms-1](/images/alarms/alarms-1.PNG?classes=shadow)

- Inside the Namespace, you will see all the dimensions under which the metrics are aggregated under
- Select `CloudTrail` dimension and select one of the metric filters you previously created

![Alarms2](/images/alarms/alarms-2.PNG?classes=shadow)

- Click `Select metric` which will take you to the screen as the one below

![Alarms3](/images/alarms/alarms-3.PNG?classes=shadow)

- Enter the threshold value (for example, 300 which means 300 count since this is a Metric Filter) that you want the metric data to be monitored.  Notice that `Static` option is selected by default, which means we will be setting a static value as the threshold to be monitored.

- Expand `Additional configuration` where you can indicate how many occurences of the breach qualifies for the alarm to be triggered. Set the values to 2 out of 5, which will make the alarm get triggered if there has been 2 breaches in 5 evaluation periods. Notice the message at the top of the graph describing the setting as `This alarm will trigger when the blue line goes above the red line for 2 datapoints within 25 minutes.`

- Click `Next` 
- In the `Configure actions` screen, you can set what action you want to take when the alarm changes to different states such as  
    - In alarm
    - OK
    - Insufficient data

The available options for actions include,
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

![Alarms5](/images/alarms/alarms-5.PNG?classes=shadow)

Click on the alarm to see the alarm details as shown below. Notice the alarm state now changed to 'OK' after evaluation was complete.

![Alarms6](/images/alarms/alarms-6.PNG?classes=shadow)

