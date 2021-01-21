+++
title = "Composite Alarms"
menuTitle = "6.3 Composite Alarms"
date = 2020-06-07T10:51:57-04:00
weight = 3
+++

A composite alarm includes a rule expression that takes into account the alarm states of other alarms that you have created. The composite alarm goes into ALARM state only if all conditions of the rule are met. The alarms specified in a composite alarm's rule expression can include metric alarms and other composite alarms.

Using composite alarms can reduce alarm noise. You can create multiple metric alarms, and also create a composite alarm and set up alerts only for the composite alarm. For example, a composite might go into ALARM state only when all of the underlying metric alarms are in ALARM state.

### Create a Composite Alarm

- Go to [CloudWatch Alarm](https://console.aws.amazon.com/cloudwatch/home?#alarmsV2:?) and select any 2 alarms. if you created a Metric alarm and a Anomaly Detection alarm, choose those.

- Click on `Create composite alarm`
![Alarms14](/images/alarms/alarms-14.PNG?classes=shadow)

- The following screen should look like the one below. Per the default condition the composite alarm will get triggered when either of the child alarms are in `ALARM` state.

```
ALARM("Metric_Alarm_Test") OR 
ALARM("AD-Alarm")
```
![Alarms15](/images/alarms/alarms-15.PNG?classes=shadow)

- You can customize the rule to the needs as well. 

    - Samples 

```
ALARM("Metric_Alarm_Test") AND
ALARM("AD-Alarm")
```

```
ALARM("Metric_Alarm_Test") OR 
(ALARM("AD-Alarm") AND ALARM("Another_Alarm"))
```

Select `Create a new topic` to create a new SNS topic to send the notification to and provide your email address.

![Alarms4](/images/alarms/alarms-4.PNG?classes=shadow)

- Click `Create topic` to create the SNS topic 
- Click `Next`, give the alarm a name and click `Next` again to review the configuration
- Click `Create` to create the alarm


### Navigating the Alarms screen

Once you have created the alarm, you will notice that the alarm is now in `Insufficient data` state which indicates that there is not enough data to validate the alarm. Waiting for 5 minutes will change the alarm state to `OK` in green.

Notice the child alarms part of the composite alarm being shown as well.

![Alarms16](/images/alarms/alarms-16.PNG?classes=shadow)
