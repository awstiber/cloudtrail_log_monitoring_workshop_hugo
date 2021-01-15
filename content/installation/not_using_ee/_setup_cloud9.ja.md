+++
title = "Cloud9 環境のセットアップ"
menuTitle = "Cloud9 のセットアップ"
date = 2020-08-31T13:25:50-04:00
weight = 20
pre = "<b>▶︎ </b>"
+++

Cloud9 を利用してアプリケーションをデプロイできます。Cloud9 の利用を希望しない場合は、このページをスキップして [次の手順](/ja/installation/_deploy_app.html) に進んでください。

{{%notice warning%}}
EKS のモニタリングを利用したい場合に備えて、アプリケーションの一部を EKS にインストールすることもできます。その場合は、先に進む前に EKS をサポートする AWS リージョンに入っていることを確認してください。[リージョン表](https://aws.amazon.com/jp/about-aws/global-infrastructure/regional-product-services/) を参照してください。
{{% /notice%}}

#### 以下のコマンドを実行して Cloud9 環境を作成します

```
curl -O https://raw.githubusercontent.com/aws-samples/one-observability-demo/main/cloud9-cfn.yaml

aws cloudformation create-stack --stack-name C9-Observability-Workshop --template-body file://cloud9-cfn.yaml --capabilities CAPABILITY_NAMED_IAM

```
