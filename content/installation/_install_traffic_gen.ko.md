+++
title = "Traffic Generator 설치"
menuTitle = "2.6 Traffic Generator 설치"

date = 2020-05-06T16:24:15-04:00
weight = 50
+++

CDK 앱은 Traffic Generator 컨테이너가 포함 된 ECS Fargate 작업 정의도 생성합니다. Traffic Generator 앱은 다른 워크샵을 위해 자동으로 많은 트래픽을 생성합니다.

#### 트래픽 생성기를 설치하는 단계

* [ECS Clusters](https://console.aws.amazon.com/ecs/home#/clusters)로 이동합니다.
* 아래와 같이 CDK 배포로 생성 된 ECS 클러스터 중 하나를 선택합니다.
![Cluster](/images/trafficgen/traffic-cluster.png)
* **Tasks** 탭으로 이동하여 **Run new task**을 클릭합니다.
![Cluster](/images/trafficgen/traffic-newtask.png)
* **Run Task** 화면에서 시작 유형을 `Fargate` 로 선택합니다.
* `Servicestrafficgenerator`로 시작하는 Task definition를 선택합니다. `Services/Microservices`라는 VPC를 선택해야합니다.
* 서브넷 목록에서 퍼블릭 서브넷 중 하나를 선택합니다.
* 새 보안그룹을 생성하지 말고 아래와 같이 목록에서`default` 보안그룹을 선택하십시오.
![Security Group](/images/trafficgen/traffic-sg.png)

* `Auto-Assign public IP`를`ENABLED`로 설정합니다.
![Cluster](/images/trafficgen/traffic-runtask.png)
* **Run Task**을 클릭하고 작업상태가 다음과 같이 <span style="color: green;"> **RUNNING** </span>으로 표시 될 때까지 30초 동안 기다립니다.
![Cluster](/images/trafficgen/traffic-running.png)