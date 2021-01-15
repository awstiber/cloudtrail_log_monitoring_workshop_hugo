+++
title = "Querying Logs"
menuTitle = "8.2 Querying Logs"
date = 2020-06-03T17:09:15-04:00
weight = 2
+++

### Using Log Insights Queries

Go to [CloudWatch Logs Insights](https://console.aws.amazon.com/cloudwatch/home?#logsV2:logs-insights) console and select the `aws-cloudtrail-logs` log group. Remember, you can select more than one log group if needed. As of May 2020, you can select up to 20 log groups at a time.

![IMG001](/images/logsinsights/Cloud-Watch-Insights-Query.PNG?classes=border)

As you can see a sample query is automatically placed in the query field. Now simply click on `Run query` button to execute the query results. As expected, you will see results from the query.

The sample query fetches the `@timestamp` and `@message` fields from the log data, orders by the timestamp field in descending order and displays the first 20 records.

![IMG002](/images/logsinsights/Cloud-Watch-Insights-Query-Results.PNG?classes=border)


You can also expand the returned result by clicking the arrow on the left hand side

![IMG003](/images/logsinsights/Cloud-Watch-Insights-Query-Results-Expanded.PNG?classes=border)
{{% notice tip %}}
Learn more about Logs Insights syntax and queries [here](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CWL_QuerySyntax.html)
{{% /notice %}}

#### Simple list with filter and sort

Now paste this query into the log field. The following query applies a filter on the messages and fetches only the records that contain the eventName `AssumeRole` in the log event and displays the result ordered by the timestamp field in descending order

```plaintext
fields @timestamp, @message
| sort @timestamp desc
| limit 20
| filter eventName = 'AssumeRole'
```
![IMG004](/images/logsinsights/Cloud-Watch-Insights-Assume-Role-Query.PNG?classes=border)

#### List with aggregation, sort, and timeseries

Now paste this query into the log field. The following shows a result that contains the number of messages captured by 5 minute interval

![IMG005](/images/logsinsights/Cloud-Watch-Insights-Assume-Role-Query-Stat.PNG?classes=border)

You can also visualize the results by clicking on the `Visualization` tab in the results area as shown below.

![IMG006](/images/logsinsights/Cloud-Watch-Insights-Assume-Role-Query-Visualize.PNG?classes=border)

Notice that you can also add the visualization to a CloudWatch Dashboard, export to csv and so on.

![IMG007](/images/logsinsights/Cloud-Watch-Insights-Assume-Role-Query-Visualize-Export.PNG?classes=border)

#### Querying using AWS CLI

You can query the log groups using AWS CLI as well. The query below queries top 10 log records from a log group for a specific time period.

Make sure you replace the log group to the appropriate one you have on your account and change the start and end time parameter values to the right epoch time values. You can calculate epoch time values from this public website - [https://www.epochconverter.com/](https://www.epochconverter.com/)

```plaintext
aws logs start-query --log-group-name aws-cloudtrail-logs-285874658916-40b96c89 --start-time '1610659331' --end-time '1610745731' --query-string 'fields @message | limit 10'
```

The above query will return a queryId. Copy that query Id and replace the `<QUERY_ID>` string. in the below command and execute it to see log data results.


```plaintext
aws logs get-query-results --query-id <QUERY_ID>
```