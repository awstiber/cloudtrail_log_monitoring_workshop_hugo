+++
title = "アプリケーションのデプロイ"
menuTitle = "2.3 アプリケーションのデプロイ"
date = 2020-08-31T14:02:35-04:00
weight = 30
+++

### Cloud9 IAM 権限の設定

{{% notice info%}}
Cloud9 は通常、IAM 認証情報を動的に管理します。これは現在 aws-iam-authenticator プラグインと互換性がないため無効にし、代わりに IAM ロールを使用します。
{{% /notice %}}

- ワークスペースに戻り、歯車アイコンをクリックするか、新しいタブを起動して `Preferences` タブを開きます。

- `AWS SETTINGS` を選択

- `AWS managed temporary credentials` のチェックをはずす

- `Preferences` タブを閉じる
![c9disableiam](/images/c9disableiam.png)

- 一時的な認証情報がまだ配置されていないことを確認するために、既存の認証情報ファイルを削除します。
```
rm -vf ${HOME}/.aws/credentials
```

### ツールのインストール と コードリポジトリからのクローン <span style="color: blue;">(Event Engine を利用している場合はこの項目をスキップしてください)</span>

以下の手順で、必要なツール、必要なユーティリティをすべてインストールし、ワークショップに必要なソースコードもダウンロードします。

```
curl -sSL https://raw.githubusercontent.com/aws-samples/one-observability-demo/main/PetAdoptions/envsetup.sh | bash -s stable
```

### 現在の AWS リージョンをデフォルトとして AWS CLI に設定する必要があります：

```bash
export ACCOUNT_ID=$(aws sts get-caller-identity --output text --query Account)
export AWS_REGION=$(curl -s 169.254.169.254/latest/dynamic/instance-identity/document | jq -r '.region')
echo "export ACCOUNT_ID=${ACCOUNT_ID}" | tee -a ~/.bash_profile
echo "export AWS_REGION=${AWS_REGION}" | tee -a ~/.bash_profile
aws configure set default.region ${AWS_REGION}
aws configure get default.region
```

### IAM ロールを検証する

{{%notice info%}}
Cloud9 を使用していない場合は、Cloud9 の IAM ロール検証結果を無視して、 [CDK パッケージのインストール](#cdk-パッケージのインストール) ステップに進みます。
{{%/notice%}} 

```bash
test -n "$AWS_REGION" && echo AWS_REGION is "$AWS_REGION" || echo AWS_REGION is not set

aws sts get-caller-identity --query Arn | grep observabilityworkshop-admin -q && echo "You're good. IAM role IS valid." || echo "IAM role NOT valid. DO NOT PROCEED."
```

Cloud9 を利用している場合、IAM ロールが正しくない(IAM role NOT valid)ときは、<span style="color: red;">**続行しないでください**</span>。ページの先頭に戻って、手順を再確認してください。

### CDK パッケージのインストール
正常にクローンされたら、次のコマンドを使用して `pet_stack`フォルダに移動します

```
cd workshopfiles/one-observability-demo/PetAdoptions/cdk/pet_stack
```

全ての npm パッケージをインストールします

```
npm install
```

### config パラメータの編集

{{%notice info%}}
これはオプションの手順です。必要な場合のみ、設定ファイルの値を変更します。各行のコメントを参照してください。
{{% /notice%}}

左側のエクスプローラタブから `workshopfiles/one-observability-demo/PetAdoptions/cdk/pet_stack/cdk.json`ファイルを開きます。 

以下の手順を参照し、各行が正しい設定になっていることを確認してください。以下の設定をコピーして貼り付けずに、各行の説明を参考にし、既存ファイルを書き換えてください。(#以降のコメントがファイルにあるとエラーになります)

{{%notice info%}}
EKS クラスターをデプロイする場合は、`petsite_on_eks` の値を `true` に設定します。
{{% /notice%}}

```bash
{
  "app": "npx ts-node app/pet_stack.ts",
  "context": {
    "vpc_cidr": "10.11.0.0/16", # この CIDR 範囲が既存の環境と競合する場合は、この値を変更します。
    "snstopic_email": "someone@example.com",  # Eメール通知を受信する場合は、この値をアクセス可能なEメールアドレスに変更します。1000通Eメールトラフィックが発生しますので、これを受信できるEメールアドレスを設定してください。大量のEメールの受信を避けるために、デフォルト値のままにしておくことも可能です。
    "rdsusername":"petadmin", 
    "petsite_on_eks":"false" # この値を「true」に変更すると、EKS クラスターがプロビジョニングされ、フロントエンドアプリがデプロイされます。EKS を使用しない場合は、false にすると、すべてのサービスを同じ ECS Fargate クラスタに展開します。
  }
}
```

### 変更を反映するために、EC2 インスタンスを再起動します <span style="color: blue;">（EKS に petsite をデプロイしない場合は、この手順をスキップします）</span>

Cloud9ターミナルで次のコマンドを実行し、Cloud9インスタンスが再起動するまで数秒待ちます


```
sudo reboot
```

### Bootstrap CDK 

{{%notice tip%}}
`pet_stack` フォルダにいない場合は、
```cd workshopfiles/one-observability-demo/PetAdoptions/cdk/pet_stack```
このコマンドを実行します
{{%/notice%}}

```
cdk bootstrap
```

### スタックの Deploy

```
cdk deploy Services
```

スタックがデプロイされるまでに数分かかりますので、その間に<span style=font-size:40px> ☕️ </span>

