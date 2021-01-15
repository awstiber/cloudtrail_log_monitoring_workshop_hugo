+++
title = "Event Engine 접속"
date = 2020-05-06T14:10:29-04:00
weight = 10
chapter = true
pre = "<b>1. </b>"
+++

| <span style="color: #FF9900;font-weight:bold;font-size:20px">AWS 직원을 위한 참고사항</span> |
| :------------- | 
|  워크샵용 계정을 프로비저닝하는 데 사용해야하는 사용자지정 템플릿을 만들었습니다. [템플릿이 필요하면 연락하세요.](https://amazon.enterprise.slack.com/user/@W017PE1V36G). 고객과 워크숍이나 이벤트를 진행할 때마다 [ijaganna@](https://amazon.enterprise.slack.com/user/@W017PE1V36G)에게  세부정보(고객 이름, 날짜, 위치 및 참가자 수)를 보내주면 사용현황을 추적 할 수 있습니다.


{{%notice warning%}}
현재 AWS가 진행하는 워크숍 이벤트에 참여하고 있지 않은 경우 이 섹션은 적용되지 않습니다. 이 페이지를 건너뛰고 [여기](/ko/installation/not_using_ee.html)로 가십시오.
{{% /notice%}}

AWS Event Engine은 AWS 필드팀이 워크샵, GameDays, Bootcamps, Immersion Days 및 AWS 계정에 대한 실제 액세스가 필요한 기타 이벤트를 실행하는 데 도움이되도록 만들어졌습니다.

브라우저를 열고 이벤트 엔진 로그인 페이지로 이동하십시오. [https://dashboard.eventengine.run](https://dashboard.eventengine.run). 약관 및 로그인 페이지가 표시됩니다. 이벤트 엔진 계정 사용에 적용되는 약관을 읽고 이해하십시오.

![EventEngine](/images/ee/ee.png)

AWS 이벤트담당 직원이 제공하는 12자리 해쉬코드가 필요합니다. 

![EventEngine](/images/ee/ee1.png)

12 자리 해시코드를 사용하여 로그인합니다.

![EventEngine](/images/ee/ee2.png)

원하는 경우 팀 이름을 설정하십시오. 참가자가 각자 자신의 계정을 가질 수도 있고, 팀을 구성 할 수 있습니다.

![EventEngine](/images/ee/ee3.png)

오늘 이벤트에서 사용할 AWS 계정에 로그인하려면 AWS 콘솔 링크를 클릭하십시오.

![EventEngine](/images/ee/ee4.png)

이벤트가 사용되는 리전을 확인하십시오. 이 리전의 작업만 허용됩니다.

![EventEngine](/images/ee/ee5.png)

'Open AWS Console'버튼을 클릭하여 AWS 콘솔을 엽니다. 여기에 CLI 자격증명에 대한 내용이 있지만 오늘 워크샵에서는 Cloud9 콘솔과 IDE를 사용하므로 필요하지 않습니다.

어려움이 있으시면 AWS 이벤트담당 직원에게 알려주십시오.