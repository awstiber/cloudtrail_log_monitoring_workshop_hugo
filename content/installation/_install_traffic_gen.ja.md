+++
title = "トラフィックジェネレータのインストール"
menuTitle = "2.6 トラフィックジェネレータのインストール"

date = 2020-05-06T16:24:15-04:00
weight = 50
+++

このCDKアプリは、トラフィックジェネレータアプリのコンテナを含むECS Fargateタスク定義も作成します。トラフィックジェネレータアプリは自動的に多くのトラフィックを作成し、ワークショップの活動を支援します。

#### トラフィックジェネレータ インストールの手順

* [ECS Clusters](https://console.aws.amazon.com/ecs/home#/clusters) を開きます
* 以下のように、CDK によって作成された ECS クラスタのいずれかを選択します。
![Cluster](/images/trafficgen/traffic-cluster.png)
* **タスク** タブを選択し、**新しいタスクの実行** をクリックします
![Cluster](/images/trafficgen/traffic-newtask.png)
* **タスクの実行** 画面で、起動タイプ `Fargate` を選択します
* `Servicestrafficgenerator` で始まるタスク定義を選択します。必ず、 `Services/Microservices` という名前の VPC を選択してください
* サブネットリストからパブリックサブネットの 1 つを選択します。
* 新しいセキュリティグループを作成せずに、以下のようにリストから `default` セキュリティグループを選択してください
![Security Group](/images/trafficgen/traffic-sg.png)

* `パブリック IP の自動割り当て` を `ENABLED` にします
![Cluster](/images/trafficgen/traffic-runtask.png)
* **タスクの実行** をクリックし、30 秒ほど待ち、ステータスが <span style="color: green;"> **RUNNING** </span> になることを確認します
![Cluster](/images/trafficgen/traffic-running.png)
