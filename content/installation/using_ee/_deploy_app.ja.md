+++
title = "アプリケーションのデプロイ"
date = 2020-08-29T14:10:29-04:00
weight = 10
pre = "<b>▶︎ </b>"
hidden = true
+++

{{%notice warning%}}
EKS にフロントエンドを展開する場合は、このページの手順を使用する必要があります。EKS に興味がない場合は、このページをスキップして [ここへ移動](/ja/installation/_using_the_app.html) してください。
{{% /notice%}}

- ワークスペースに戻り、歯車アイコンをクリックするか、新しいタブを起動して `Preferences` タブを開きます。

- `AWS SETTINGS` を選択

- `AWS managed temporary credentials` のチェックをはずす

- `Preferences` タブを閉じる
![c9disableiam](/images/c9disableiam.png)

- 一時的な認証情報がまだ配置されていないことを確認するために、既存の認証情報ファイルを削除します。
```
rm -vf ${HOME}/.aws/credentials
```

- ブラウザのタブを閉じて、 Cloud9 のセッションを閉じます。

### 変更を反映するために、EC2 インスタンスを再起動します

- [EC2 console](https://console.aws.amazon.com/ec2/v2/home?#Instances:tag:Name=aws-cloud9*;sort=desc:launchTime) を開いて、Cloud9 の EC2 インスタンスを選択します。

- `アクション > インスタンスの状態 > 再起動` をクリックします。

- Cloud9 のページに戻り、 IDE を再度開きます。

### EKS へ Petsite フロントエンド Web アプリをデプロイする

Cloud9ターミナルで、以下のコマンドを実行します。

```
cdk deploy EKS_Petsite
```

スタックがデプロイされるまでに数分かかりますので、その間に<span style=font-size:40px> ☕️ </span>

### kubeconfig の更新

次のコマンドを実行します。

{{%notice info%}}
これにより、Cloud9 コンソールで実行する必要があるコマンドが返されます。そのコマンドにより、Cloud9 環境の kubeconfig ファイルが更新され、kubectl を使用してクラスター上の操作を実行できるようになります。
これは重要なステップです。結果の一部として生成されたコマンドを実際に実行してください。正しく指示に従わないと、EKS クラスターにアプリケーションを正常にデプロイできません。
{{% /notice%}}

```bash
STACK_NAME=$(aws ssm get-parameter --name '/petstore/stackname' --region $AWS_REGION | jq .Parameter.Value -r)

aws cloudformation describe-stacks  --stack-name $STACK_NAME | jq '.Stacks[0].Outputs[] | select(.OutputKey | contains("ConfigCommand")).OutputValue' -r                                               
```

実行後、出力は以下のスクリーンショットのようになります
![Kubeconfig](/images/deployeks.png)


### IAM ポリシーを EKS ノードインスタンスロールに追加する

[EC2 console](https://console.aws.amazon.com/ec2/v2/home) に移動し、デプロイの一部として作成された EC2 インスタンスを選択します。サービス/マイクロサービス VPC の下にあるものを探します。インスタンスロールを選択し、クリップボードにコピーします (Cmd+C / Ctrl+C)

![Instance Role](/images/containerinsights/eks11.png)

プレースホルダテキストを、前のステップでコピーしたインスタンスロール名に置き換えた後、次のコマンドを実行します。

```
ROLE=<REPLACE_THIS_WITH_THE_COPIED_INSTANCE_IAM_ROLE_NAME>
```

以下のコマンドを実行して、ワークショップに必要な IAM ロールを追加します。

```
aws iam attach-role-policy --role-name $ROLE --policy-arn arn:aws:iam::aws:policy/AWSXRayDaemonWriteAccess
aws iam attach-role-policy --role-name $ROLE --policy-arn arn:aws:iam::aws:policy/CloudWatchAgentServerPolicy
aws iam attach-role-policy --role-name $ROLE --policy-arn arn:aws:iam::aws:policy/AmazonSSMFullAccess
aws iam attach-role-policy --role-name $ROLE --policy-arn arn:aws:iam::aws:policy/AmazonSQSFullAccess
aws iam attach-role-policy --role-name $ROLE --policy-arn arn:aws:iam::aws:policy/AmazonSNSFullAccess
```

### PetSite EKS アプリのデプロイ

`/cdk/pet_stack/` ディレクトリからターミナルで次のコマンドを入力します。

```
chmod +x resources/eksdeployment.sh 
./resources/eksdeployment.sh    
```

コマンド実行結果は、以下のものと同様になります

```
❯ ./eksdeployment.sh
-----------------------------------------------------------------
This script deploys petsite and the xray daemon to the EKS cluster
-----------------------------------------------------------------
deployment.apps/petsite-deployment created
service/service-petsite created
serviceaccount/xray-daemon created
clusterrolebinding.rbac.authorization.k8s.io/xray-daemon created
daemonset.apps/xray-daemon created
configmap/xray-config created
service/xray-service created
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 15552  100 15552    0     0  16627      0 --:--:-- --:--:-- --:--:-- 16615
namespace/amazon-cloudwatch created
serviceaccount/cloudwatch-agent created
clusterrole.rbac.authorization.k8s.io/cloudwatch-agent-role created
clusterrolebinding.rbac.authorization.k8s.io/cloudwatch-agent-role-binding created
configmap/cwagentconfig created
daemonset.apps/cloudwatch-agent created
configmap/cluster-info created
serviceaccount/fluentd created
clusterrole.rbac.authorization.k8s.io/fluentd-role created
clusterrolebinding.rbac.authorization.k8s.io/fluentd-role-binding created
configmap/fluentd-config created
daemonset.apps/fluentd-cloudwatch created
----- Creating SSM Parameter -----
----- ✅ DONE --------
```

{{%notice tip%}}
`jq: error (at <stdin>:35): Cannot iterate over null (null)` このようなエラーが表示された場合は、競合状態によりELBの作成に通常よりも少し時間がかかったことが原因です。その場合は、15 秒待ってから `./eksdeployment.sh ` を再実行してください。
{{% /notice%}}

{{%notice warning%}}
ワークショップが完了したら、必ず [ワークショップ環境の削除](/ja/_cleanup.html) してください。これを行わないと、使用料金が発生します。
{{% /notice%}}
