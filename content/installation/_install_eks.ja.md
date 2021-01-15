+++
title = "Petsite アプリケーションの EKS へのインストール"
menuTitle = "2.4 Petsite アプリケーションの EKS へのインストール"
date = 2020-09-02T11:58:20-04:00
weight = 35
+++

{{% notice info%}}
このセクションは、前の手順で Petsite のフロントエンドを EKS にインストールすることを選択した場合にのみ行います。もし EKS を選んでない場合は、このセクションを飛ばして [ここへ移動](/ja/installation/_using_the_app.html) してください。
{{% /notice%}}


### kubeconfig の更新

次のコマンドを実行して kubeconfig を更新し、EKS クラスタと対話できるようにします。

```bash
STACK_NAME=$(aws ssm get-parameter --name '/petstore/stackname' --region $AWS_REGION | jq .Parameter.Value -r)

$(aws cloudformation describe-stacks  --stack-name $STACK_NAME | jq '.Stacks[0].Outputs[] | select(.OutputKey | contains("ConfigCommand")).OutputValue' -r)                                               
```

実行後、出力は以下のスクリーンショットのようになります
![Kubeconfig](/images/eks_kubeconfig.png)

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
ワークショップが完了したら、必ず [ワークショップ環境の削除](/ja/_cleanup.html) してください。これを行わないと、期待しない使用料金が発生します。
{{% /notice%}}
