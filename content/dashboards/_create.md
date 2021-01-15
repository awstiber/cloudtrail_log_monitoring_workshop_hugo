+++
title = "Create a Dashboard"
menuTitle = "11.1 Create a Dashboard"
date = 2020-06-03T13:01:49-04:00
weight = 1
+++

### Create a dashboard using AWS console 
Go to [CloudWatch Dashboard](https://console.aws.amazon.com/cloudwatch/home?#dashboards:) and click on `Create dashboard`, name it `observability-dashboard` and click `Create dashboard`. You will see a screen such as below where you can choose the type of widget you want. 

![AddWidget](/images/dashboard/dashboard1.png?classes=shadow)

Select `Line` graph and click `Configure`. This will take you a screen showing all Namespaces under which Metrics are being collected. If you see `ContainerInsights` namespace, select that (this will show up if you deployed the petsite service on EKS during setup). If not, select `ECS/ContainerInsights` namespace. 

For the sake of simplicity, I'm going to assume you chose `ContainerInsights` for the rest of the instructions. If you did not, just make small adjustments to the instructions yourself as it is pretty easy to follow either way.

![Namespace](/images/dashboard/dashboard2.png?classes=shadow)

Select a dimension inside the namespace and select a metric such as CPU Utilization or Memory Utilization as shown below. Click on `Create Widget` to have this graph added as a new widget to the dashboard.

![AddWidget2](/images/dashboard/dashboard3.png?classes=shadow)

You will be able to adjust the size of the widget simply by dragging the bottom right of the corner. Click on 'Save dashboard` to save the dashboard, not doing so will not save the changes made.

{{%notice tip%}}
Learn about Service Quota limits on widgets and metric limits per widget/dashboard [here](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_limits.html).
{{% /notice%}}

You can also add CloudWatch Logs Insights results as a widget as well. Click on `Add widget` select `Query results`, which will take you to CloudWatch Logs Insights screen.

![SelectLG](/images/dashboard/dashboard4.png?classes=shadow)

Paste this query below in the query textbox and click on `Run query`.

```
fields @timestamp, @message
| sort @timestamp desc
| filter @message like /PetListAdoptions/
| stats count(@message) by bin(5m)
| limit 20
```

Your screen should look like the one below. The query shows the number of instances the string `PetListAdoptions` was found in the log data. Now click on `Create widget`.

![SelectLGResults](/images/dashboard/dashboard5.png?classes=shadow)

This will add a new widget to the dashboard as shown below.

![AddWidget3](/images/dashboard/dashboard6.png?classes=shadow)


### Working on CloudWatch Dashboards using AWS CLI

You can perform a variety of operations on CloudWatch Dashboards through AWS CLI as well. 
{{%notice tip%}}
Take a look at the available commands here - https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/index.html#cli-aws-cloudwatch
{{% /notice%}}

The following command gets the dashboard in json format back to you.

```
aws cloudwatch get-dashboard --dashboard-name observability-dashboard
```

#### Create a new dashboard

Take the following json string below and create a file on the local directory and name it `dashboard.json`. Make sure you replace `<YOUR_AWS_REGION_HERE>` placeholder with the appropriate AWS Region.

```json
{"widgets":[{"type":"metric","x":0,"y":0,"width":12,"height":6,"properties":{"view":"timeSeries","stacked":false,"metrics":[["ContainerInsights","pod_cpu_utilization","PodName","petsite-deployment","ClusterName","petsite","Namespace","default"]],"region":"<YOUR_AWS_REGION_HERE>"}}]}
```

Execute the following command which will create a new CloudWatch Dashboard 

```
aws cloudwatch put-dashboard --dashboard-name cli-dashboard --dashboard-body $(cat dashboard.json)
```

To see the list of dashboards that exist on the account, execute the following command

```
aws cloudwatch list-dashboards
```
You can also check your newly created dashboard on the dashboard console as well.