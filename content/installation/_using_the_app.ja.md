+++
title = "アプリを操作してみる"
menuTitle = "2.5 アプリを操作してみる"
date = 2020-05-06T16:24:15-04:00
weight = 40
+++

ターミナルで次のコマンドを実行します。

```bash
aws ssm get-parameter --name '/petstore/petsiteurl'  | jq -r .Parameter.Value
```
コマンドの結果として返された URL にアクセスします。以下のような、アプリのホーム画面を見ることができるかと思います。

#### トラブルシューティング

まれに、サイトにペットの画像が表示されないような現象が発生することがあります。この問題を解決するには、次のコマンドを実行します。

```
seeder_lambda=$(aws lambda list-functions | jq -r '.Functions[].FunctionName | select(contains("ddbseeder"))')
aws lambda invoke --function-name $seeder_lambda --payload '{"mode":"create"}' --cli-binary-format raw-in-base64-out response.json
```

![App Home](/images/playaround/home.png)

以下のように、アプリケーションを操作することができます。

![App Home](/images/playaround/play.gif)
