+++
title = "Metrics Explorer"
menuTitle = "10.5 Metrics Explorer"
date = 2020-12-15T21:20:12-05:00
weight = 5
post = " <b><sup>New</sup></b>"
+++

{{%notice info%}}
This module was developed by [Akshat Srivastava](https://www.linkedin.com/in/akshatsrivastava/)
{{% /notice%}}

Metrics Explorer is a tag-based tool that enables you to filter, aggregate, and visualize your metrics by tags and resource properties, to enhance observability for your services. Metrics explorer visualizations are dynamic, so if a matching resource is created after you create a metrics explorer widget and add it to a CloudWatch dashboard, the new resource automatically appears in the explorer widget.

{{%notice info%}}
Read all about Metrics Explorer here - https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Metrics-Explorer.html
{{% /notice%}}

Navigate to Metrics Explorer by [Clicking Here](https://console.aws.amazon.com/cloudwatch/home?#explorer:), it should take you to a screen as shown below (Empty Explorer)
![Metrics14](/images/metrics/metrics14.png?classes=shadow)

Metrics Explorer allows you to chose from Generic Templates (Lambda and EC2) or Service Based Templates where you can chose which metric you want to add to a dashboard, based on the service selected.
![Metrics15](/images/metrics/metrics15.png?classes=shadow)

Chose Generic templates< Lambda by runtime. You can see the graphs are pre-filled for you.
![Metrics16](/images/metrics/metrics16.png?classes=shadow)

{{%notice info%}}
**Metrics Explorer helps `bridge the gap between your resources and tags` to enhance observability for your services. It provides quick and easy mechanism to filter, aggregate, and visualize your metrics by tags and resource properties.
The visualizations you see here are dynamic, so if a matching resource is created after you create a metrics explorer widget and add it to a CloudWatch dashboard, the new resource automatically appears in the explorer widget**
{{% /notice%}}

Use the metrics explorer to now visualize past 1 hour metrics for all RDS DB instances that have a stack name = "Services" as their tag. Click on **Empty Explorer** again and select **RDS** (no need to save the previous graphs). Leave this default value and add a filter by chosing the tag `aws:cloudformation:stack-name:` and then click on `Services` Image below shows how your explorer should look like:
![Metrics17](/images/metrics/metrics17.png?classes=shadow)

Click on `Add to Dashboard` on the top right this time < Create new dashboard (name it RDS-Services-Stack) and click on the small tick next to it (this creates a new dashboard and saves it). Leave the Widget Title blank and click on `Add to dashboard` on the bottom right. Your dashboard is available under Dashboards (you would have to swtich to new interface to view this)
![Metrics18](/images/metrics/metrics18.png?classes=shadow)

You can also start at a dashboard view and add a Metrics Explorer widget. Go to Dashboard and click on "Create Dashboard", name it "Metrics-Explorer-Widget" < click create dashboard < chose "Explorer" widget < click next < select empty explorer. You can now add any resource and filter it by tag/resource property and directly save this dashboard.
![Metrics19](/images/metrics/metrics19.png?classes=shadow)


