+++
title = "ワークショップ環境の準備"
menuTitle = "2.1 ワークショップ環境の準備"
date = 2020-05-06T16:24:15-04:00
weight = 1
hidden = true
+++

この章の手順に従って、PetAdoptions アプリケーションをデプロイするための CLI 環境を準備します。また、自分のラップトップを使用して、macOS、Linuxターミナルで、または [WSL on Windows](https://docs.microsoft.com/en-us/windows/wsl/install-win10) を使用してこれらのコマンドを実行することもできます。

#### Cloud9 環境のセットアップ

{{% notice info%}}
Cloud9 ドメインでは、広告ブロッカー、javascript disablers、トラッキングブロッカーを無効にする必要があります。無効にしないと、ワークスペースへの接続が影響を受ける可能性があります。
{{% /notice %}}

{{%notice warning%}}
このワークショップでは、オプションで Amazon EKS クラスターを作成します。このワークショップのすべての章を行う場合は、EKS クラスターの作成も行なってください。EKS を利用する場合は、EKS をサポートするリージョンにいることを確認してください。[サポートされているリージョン](https://aws.amazon.com/about-aws/global-infrastructure/regional-product-services/) を参照してください。
{{% /notice%}}

- [Cloud9 Environment](https://console.aws.amazon.com/cloud9/home?) を作成します
  - Create environment をクリックします
  
- Name を `observabilityworkshop` に、 `Cost-saving setting` を `After four hours` に、他の項目は全てデフォルトにします

- 起動後に、`Welcome tab` と画面下部のワークエリアを閉じ、メインワークエリアに `New Terminal` タブを開いて環境をカスタマイズします。

- すると、このような状態になっているはずです:
![cloud9](/images/cloud9.png)

{{% notice info%}}
Cloud9 は通常、IAM 認証情報を動的に管理します。これは現在 aws-iam-authenticator プラグインと互換性がないため、無効にし、代わりに IAM ロールを使用します。
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

### Cloud9 IAM 権限のセットアップ
1. [このリンクを使って、Administrator 権限の IAM ロールを作成します](https://console.aws.amazon.com/iam/home#/roles$new?step=review&commonUseCase=EC2%2BEC2&selectedUseCase=EC2&policies=arn:aws:iam::aws:policy%2FAdministratorAccess)
2. `AWS サービス` と `EC2` が選択されていることを確認し、`次のステップ` をクリックし、権限ポリシーを表示します。
3. `AdministratorAccess` が選択されていることを確認し、`次のステップ` をクリックします。そして、タグの追加画面でも `次のステップ` をクリックし、ロール作成の確認画面を表示します。
4. ロール名に `observabilityworkshop-admin` を入力し、 `ロールの作成` をクリックします。
![createrole](/images/createrole.png)

1. [このリンクをクリックしてください。](https://console.aws.amazon.com/ec2/v2/home?#Instances:tag:Name=observabilityworkshop*;sort=desc:launchTime) Cloud9 の EC2 instance を探します。
1. Cloud9 の EC2 instance を選択し、`アクション > インスタンスの設定 > IAM ロールの割り当て/置換` をクリックします
![c9instancerole](/images/c9instancerole.png)
1. `IAM ロール` ドロップダウンから `observabilityworkshop-admin` を選択し、`適用` をクリックします
![c9attachrole](/images/c9attachrole.png)

### Cloud9 instance ストレージボリュームサイズの追加

{{%notice warning%}}
重要:
スタック作成プロセスには、かなりのストレージスペースを必要とするいくつかの Docker Image を構築することが含まれます。そのため、スタックを適切にデプロイできるように、Cloud9 ボリュームサイズを増やす必要があります。
Cloud9 を使用していない場合は、この手順を無視して、次の手順に進んでください。
{{% /notice%}}

[EC2 console](https://console.aws.amazon.com/ec2/v2/home?#Instances:tag:Name=aws-cloud9-observabilityworkshop*;sort=desc:launchTime) を開き、名前が `aws-cloud9-observabilityworkshop` で始まるインスタンスを探し、以下にのように、そこから `インスタンス ID` をコピーします。

![](/images/c9instance.png)

次に [EC2 Volumes](https://console.aws.amazon.com/ec2/v2/home?#Volumes:) に移動し、前にコピーした `インスタンス ID` でアタッチされたボリュームを探します。それを選択し、
 `アクション > ボリュームの変更` をクリックします

`サイズ` テキストボックスに `32` と入力し、Cloud9 instance のボリュームサイズを32GBに増やします。

![](/images/c9volume.png)

`変更` をクリックし、数秒待ってから、ボリュームコンソールを更新して変更を確認します。次に、Cloud9 に戻り、手順を続行します。

### 変更の反映のための EC2 instance 再起動

[EC2 console](https://console.aws.amazon.com/ec2/v2/home?#Instances:tag:Name=aws-cloud9-observabilityworkshop*;sort=desc:launchTime) を開き、Cloud9 instance を選択します

`アクション > インスタンスの状態 > 停止` をクリックし、`インスタンスの状態` が `Stopped` になるまで待ちます。

次に、 `アクション > インスタンスの状態 > 開始` をクリックし、`インスタンスの状態` が `running` になるまで待ちます。

そして、 Cloud9 の画面に戻り、IDE を開きます。

### ツールのインストール と コードリポジトリからのクローン

この手順で、必要なツール、必要なユーティリティをすべてインストールし、ワークショップに必要なソースコードもダウンロードします。

```
curl -sSL https://raw.githubusercontent.com/aws-samples/one-observability-demo/main/PetAdoptions/envsetup.sh | bash -s stable
```

- 現在のリージョンをデフォルトとしてaws cliに設定する必要があります。

```bash
export ACCOUNT_ID=$(aws sts get-caller-identity --output text --query Account)
export AWS_REGION=$(curl -s 169.254.169.254/latest/dynamic/instance-identity/document | jq -r '.region')
```
AWS_REGION が目的のリージョンに設定されているかどうかを確認します

```bash
test -n "$AWS_REGION" && echo AWS_REGION is "$AWS_REGION" || echo AWS_REGION is not set
```
これらの設定をbash_profileに保存します

```bash
echo "export ACCOUNT_ID=${ACCOUNT_ID}" | tee -a ~/.bash_profile
echo "export AWS_REGION=${AWS_REGION}" | tee -a ~/.bash_profile
aws configure set default.region ${AWS_REGION}
aws configure get default.region
```

### IAM ロールを検証する

GetCallerIdentity CLI コマンドを使用して、Cloud9 IDE が正しい IAM ロールを使用していることを確認します。

```bash
aws sts get-caller-identity --query Arn | grep observabilityworkshop-admin -q && echo "You're good. IAM role IS valid." || echo "IAM role NOT valid. DO NOT PROCEED."
```

IAM ロールが正しくない場合(IAM role NOT valid)は、<span style="color: red;">**続行しないでください**</span>。ページの先頭に戻って、手順を再確認してください。

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
{{%notice warning%}}
設定ファイルは、アプリケーションスタックを正常に作成するために重要です。適切な値を入力してください。
{{% /notice%}}


左側のエクスプローラタブから `workshopfiles/one-observability-demo/PetAdoptions/cdk/pet_stack/cdk.json`ファイルを開きます。 

以下の手順を参照し、各行が正しい設定になっていることを確認してください。以下の設定をコピーして貼り付けずに、各行の説明を参考にし、既存ファイルを書き換えてください。(#以降のコメントがファイルにあるとエラーになります)

```bash
{
  "app": "npx ts-node app/pet_stack.ts",
  "context": {
    "vpc_cidr": "10.1.0.0/24", # この CIDR 範囲が既存の環境と競合する場合は、この値を変更します。
    "snstopic_email": "someone@example.com", # この値を、実際にメール通知を受信可能なメールアドレスに変更します。
    "rdsusername":"", # 任意の RDS ユーザー名を必ず入力してください。空のままにしておくと、スタックの作成プロセスが失敗します。
    "rdspassword":"", # 任意の RDS パスワードの入力が必要です。空のままにしておくと、スタックの作成プロセスが失敗します。パスワードの制約:表示可能な ASCII 文字 8 文字以上で設定してください。また、 /（スラッシュ）、""（二重引用符）、@（アットマーク）は使用できません。
    "petsite_on_eks":"false" # この値を「true」に変更すると、EKS クラスターがプロビジョニングされ、フロントエンドアプリがデプロイされます。EKS を使用しない場合は、false にすると、すべてのサービスを同じ ECS Fargate クラスタに展開します。
  }
}
```

{{%notice info%}}
EKS クラスターをデプロイする場合は、`petsite_on_eks` の値を `true` に設定します。
{{% /notice%}}