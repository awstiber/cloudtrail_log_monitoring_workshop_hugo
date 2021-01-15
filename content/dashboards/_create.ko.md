+++
title = "대시보드 생성"
menuTitle = "11.1 대시보드 생성"
date = 2020-06-03T13:01:49-04:00
weight = 1
+++

### AWS 콘솔을 사용하여 대시 보드 생성
[CloudWatch Dashboard](https://console.aws.amazon.com/cloudwatch/home?#dashboards:)로 이동하여 `Create dashboard`를 클릭하고, 이름을 `observability-dashboard`로 지정한 다음 `Create dashboard`를 클릭합니다. 원하는 위젯 유형을 선택할 수있는 아래와 같은 화면이 나타납니다.

![AddWidget](/images/dashboard/dashboard1.png?classes=shadow)

`Line`그래프를 선택하고 `Metric`을 클릭합니다. 메트릭이 수집되는 모든 네임스페이스를 보여주는 화면이 나타납니다. `ContainerInsights` 네임스페이스를 선택합니다(EKS에 petsite 서비스를 배포 한 경우 표시됨). 그렇지 않은 경우`ECS/ContainerInsights` 네임스페이스를 선택합니다.

단순화를 위해 나머지 지침에서는 `ContainerInsights`를 선택했다고 가정하겠습니다. 

![Namespace](/images/dashboard/dashboard2.png?classes=shadow)

네임 스페이스 내부의 기준을 선택하고 아래와 같이 CPU 사용률 또는 메모리 사용률과 같은 메트릭을 선택합니다. 이 그래프를 대시보드에 새 위젯으로 추가하려면 `Create Widget`를 클릭합니다.

![AddWidget2](/images/dashboard/dashboard3.png?classes=shadow)

모서리의 오른쪽 하단을 드래그하여 위젯의 크기를 조정할 수 있습니다. 대시 보드를 저장하려면 `Save dashboard`을 클릭하세요. 저장하지 않으면 변경 사항이 저장되지 않습니다.

{{%notice tip%}}
위젯에 대한 서비스 할당량 제한 및 위젯/대시보드의 메트릭 제한에 대해 [알아보려면 이곳을 참고하세요.](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_limits.html).
{{% /notice%}}

또한 CloudWatch Logs Insights 결과를 위젯으로 추가 할 수도 있습니다.  `Add widget`를 클릭하고 `Log`를 선택하면 CloudWatch Logs Insights 화면으로 이동합니다.

![SelectLG](/images/dashboard/dashboard4.png?classes=shadow)

이 쿼리를 쿼리 텍스트 상자에 붙여넣고 `Run query`을 클릭합니다.

```
fields @timestamp, @message
| sort @timestamp desc
| filter @message like /PetListAdoptions/
| stats count(@message) by bin(5m)
| limit 20
```

아래와 같은 화면을 볼 수 있습니다. 쿼리는 로그 데이터에서 `PetListAdoptions`을 가지고있는 인스턴스 수를 보여줍니다. 이제 `Create widget`를 클릭합니다.
![SelectLGResults](/images/dashboard/dashboard5.png?classes=shadow)

이제 아래와 같이 대시 보드에 새 위젯이 추가됩니다.

![AddWidget3](/images/dashboard/dashboard6.png?classes=shadow)


### AWS CLI를 사용하여 CloudWatch 대시 보드에서 작업

AWS CLI를 통해서도 CloudWatch 대시 보드에서 다양한 작업을 수행 할 수 있습니다.
{{%notice tip%}}
여기에서 사용 가능한 명령을 살펴보십시오. - https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/index.html#cli-aws-cloudwatch
{{% /notice%}}

다음 명령은 json형식의 대시보드를 가져옵니다.

```
aws cloudwatch get-dashboard --dashboard-name observability-dashboard
```

#### 새 대시보드 만들기

아래의 json 문자열을 가져 와서 로컬 디렉터리에 파일을 만들고 이름을 `dashboard.json`으로 지정합니다. `<YOUR_AWS_REGION_HERE>`를 사용중인 AWS 리전으로 바꾸어야합니다.
```json
{"widgets":[{"type":"metric","x":0,"y":0,"width":12,"height":6,"properties":{"view":"timeSeries","stacked":false,"metrics":[["ContainerInsights","pod_cpu_utilization","PodName","petsite-deployment","ClusterName","petsite","Namespace","default"]],"region":"<YOUR_AWS_REGION_HERE>"}}]}
```

새 CloudWatch 대시 보드를 생성하는 다음 명령을 실행합니다.

```
aws cloudwatch put-dashboard --dashboard-name cli-dashboard --dashboard-body $(cat dashboard.json)
```

계정에있는 대시 보드 목록을 보려면 다음 명령을 실행하십시오.

```
aws cloudwatch list-dashboards
```

대시 보드 콘솔에서도 새로 생성 된 대시 보드를 확인할 수 있습니다.