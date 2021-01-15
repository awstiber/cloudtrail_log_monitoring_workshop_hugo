+++
title = "PetAdoptions アプリのデプロイ"
menuTitle = "2.2 PetAdoptions アプリのデプロイ"
date = 2020-05-06T16:24:15-04:00
weight = 20
hidden = true
+++

### Synthesize CDK app

```
cdk synth
```
### Bootstrap CDK 

```
cdk bootstrap
```

### Deploy the stack

```
cdk deploy Services
```

スタックがデプロイされるまでに数分〜數十分かかります。その間に <span style=font-size:40px> ☕️ </span>

### kubeconfig の更新

{{%notice info%}}
先ほど `cdk.json` で `petsite_on_eks=true` と設定しなかった場合（EKS を使用しない場合）は、残りの手順をスキップして [次のセクション](/ja/installation/_install_traffic_gen.html) へ進んでください
{{% /notice%}}

次のコマンドを実行します。 

Cloud9コンソールで実行する必要があるコマンドが返されます。このコマンドにより、Cloud9 環境の kubeconfig ファイルが更新され、kubectl を使用してクラスター上で操作を実行できるようになります。

{{%notice info%}}
これは重要なステップです。コマンド実行結果として生成されたコマンドを実際に実行してください。正しく指示に従わないと、EKS クラスターにアプリケーションを正常にデプロイできません。
{{% /notice%}}

```bash
STACK_NAME=$(aws ssm get-parameter --name '/petstore/stackname' --region $AWS_REGION | jq .Parameter.Value -r)

aws cloudformation describe-stacks  --stack-name $STACK_NAME | jq '.Stacks[0].Outputs[] | select(.OutputKey | contains("ConfigCommand")).OutputValue' -r                                               
```

実行後、出力は以下のスクリーンショットのようになります
![Kubeconfig](/images/deployeks.png)


EC2 コンソールに移動し、デプロイの一部として作成された EC2 インスタンスを選択します。サービス/マイクロサービス VPC の下にあるものを探します。インスタンスロールを選択し、クリップボードにコピーします（Cmd+C/Ctrl+C）

### IAM ポリシーを EKS ノードインスタンスロールに追加する
[EC2 console](https://console.aws.amazon.com/ec2/v2/home) に移動し、デプロイの一部として作成された EC2 インスタンスを選択します。サービス/マイクロサービス VPC の下にあるものを探します。インスタンスロールを選択し、クリップボードにコピーします (Cmd+C / Ctrl+C)

![Instance Role](/images/containerinsights/eks11.png)

以下のプレースホルダテキスト(\<REPLACE_...NAME\>)を、前のステップでコピーしたインスタンスロール名に置き換えた後、次のコマンドを実行します。

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

{{% notice info%}}
前のセクションで `cdk.json` ファイルの `petsite_on_eks` 設定を true に設定していない場合は、この手順をスキップしてください。
{{% /notice %}}

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
ワークショップが完了したら、必ず [リソースをクリーンアップする](/_cleanup.ja.html) してください。これを行わないと、使用料金が発生します。
{{% /notice%}}
