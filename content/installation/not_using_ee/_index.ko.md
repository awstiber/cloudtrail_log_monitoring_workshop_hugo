+++
title = "자신의 AWS 계정 사용시"
menuTitle = "2.1 자신의 AWS 계정 사용시"
date = 2020-08-30T15:01:17-04:00
weight = 10
chapter = true
+++

{{%notice info%}}
AWS에서 진행하는 이벤트일 경우 담당자가 Event Engine을 통해 워크샵에 필요한 리소스를 미리 프로비저닝하고 권한을 부여했을 수 있습니다. 이 경우 [이곳](/ko/installation/using_ee.html)으로 이동하여 다음 진행사항을 확인하세요. 
{{% /notice%}}

{{% notice warning%}}
AWS 직원이 대상인 이벤트일 경우 Isengard는 일부 워크샵 단계에서 문제가 생길 수 있습니다. 따라서 Isengard 대신 IAM 사용자 계정을 사용하십시오.
{{% /notice%}}

이 단계에서는 애플리케이션을 배포하는 데 사용 할 Cloud9 인스턴스를 프로비저닝하는 방법을 안내합니다.
[Cloud9를 설정하려면 여기로 이동](/ko/installation/not_using_ee/_setup_cloud9.html)