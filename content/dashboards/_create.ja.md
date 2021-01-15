+++
title = "ダッシュボードの作成"
menuTitle = "11.1 ダッシュボードの作成"
date = 2020-06-03T13:01:49-04:00
weight = 1
+++

### AWS コンソールを使用してダッシュボードを作成する 

[CloudWatch ダッシュボード](https://console.aws.amazon.com/cloudwatch/home?#dashboards:) に移動し、 `ダッシュボードの作成` をクリックし、 `observability-dashboard` という名前を付け、 `ダッシュボードの作成` をクリックします。以下のような画面が表示され、追加するウィジェットの種類を選択することができます。

![AddWidget](/images/dashboard/dashboard1.png?classes=shadow)

`線` を選択し、`Next` をクリックしたのち、 `Metrics` を選択し、 `設定` をクリックします。これにより、メトリクスが収集されているすべての名前空間を示す画面が表示されます。`ContainerInsights` 名前空間が表示されている場合は、それを選択します。（これは、前章にて EKS に petsite service を展開した場合に表示されます）ない場合は、 `ECS/ContainerInsights` 名前空間を選択します。 

説明を簡単にするために、残りの手順では `ContainerInsights` を選択したと仮定します。 `ECS/ContainerInsights` を選択した場合は、手順を適宜読み替えてください。どちらの方法にでも手順はかなり簡単です。

![Namespace](/images/dashboard/dashboard2.png?classes=shadow)

名前空間内のディメンションを選択し、以下に示すように、CPU Utilization や Memory Utilization などのメトリクスを選択します。このグラフを新しいウィジェットとしてダッシュボードに追加するには、`ウィジェットの作成` をクリックします。

![AddWidget2](/images/dashboard/dashboard3.png?classes=shadow)

ウィジェットのコーナーの右下をドラッグして、サイズを調整することができます。ダッシュボードを保存するには、 `ダッシュボードの保存` をクリックします。保存しない場合は、変更内容は保存されません。

{{%notice tip%}}
ウィジェットのサービスクォータ制限と、ウィジェット/ダッシュボードごとのメトリクス制限については [こちら](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/cloudwatch_limits.html)
{{% /notice%}}

CloudWatch Logs Insights の結果をウィジェットとして追加することもできます。 `ウィジェットの追加` をクリックして、 `Logs table` を選択します。CloudWatch Logs Insights 画面に遷移します。

![SelectLG](/images/dashboard/dashboard4.png?classes=shadow)

`PetSite` のロググループを選択し、以下のクエリをクエリテキストボックスに貼り付けて、`クエリの実行` をクリックしてください。

```
fields @timestamp, @message
| sort @timestamp desc
| filter @message like /PetListAdoptions/
| stats count(@message) by bin(5m)
| limit 20
```

画面は以下のようになります。クエリは、ログデータから 文字列 `PetListAdoptions` でヒットしたインスタンスの数を返します。ここで、 `ウィジェットの作成` をクリックします。

![SelectLGResults](/images/dashboard/dashboard5.png?classes=shadow)

以下のように、ダッシュボードに新しいウィジェットが追加されます。

![AddWidget3](/images/dashboard/dashboard6.png?classes=shadow)


### AWS CLI を使用した CloudWatch ダッシュボードの操作

AWS CLI を使用して CloudWatch ダッシュボードでさまざまな操作をすることもできます。 

{{%notice tip%}}
利用可能なコマンドを見てみましょう - https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/index.html#cli-aws-cloudwatch
{{% /notice%}}

次のコマンドは、json形式のダッシュボード定義を取得します。

```
aws cloudwatch get-dashboard --dashboard-name observability-dashboard
```

#### 新しいダッシュボードを作成する

以下のjson文字列をコピーしファイルを作成し、 `dashboard.json` という名前を付けます。`<YOUR_AWS_REGION_HERE>` プレースホルダを適切な AWS リージョンに置き換えてください。

```json
{"widgets":[{"type":"metric","x":0,"y":0,"width":12,"height":6,"properties":{"view":"timeSeries","stacked":false,"metrics":[["ContainerInsights","pod_cpu_utilization","PodName","petsite-deployment","ClusterName","petsite","Namespace","default"]],"region":"<YOUR_AWS_REGION_HERE>"}}]}
```

次のコマンドを実行して、新しい CloudWatch ダッシュボードを作成します。 

```
aws cloudwatch put-dashboard --dashboard-name cli-dashboard --dashboard-body $(cat dashboard.json)
```

このAWSアカウントに存在するダッシュボードのリストを表示するには、次のコマンドを実行します。

```
aws cloudwatch list-dashboards
```

ダッシュボードコンソールで新しく作成したダッシュボードも確認することもできます。
