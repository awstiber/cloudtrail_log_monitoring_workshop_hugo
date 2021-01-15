+++
title = "App을 사용해보기"
menuTitle = "2.5 App을 사용해보기"
date = 2020-05-06T16:24:15-04:00
weight = 40
+++

터미널에서 다음 명령을 실행하십시오.
```bash
aws ssm get-parameter --name '/petstore/petsiteurl'  | jq -r .Parameter.Value
```
결과로 반환 된 URL로 이동합니다. 아래와 같이 앱 홈 화면이 보일 것입니다.
#### 트러블슈팅

매우 드물지만 사이트에 입양가능한 동물들의 이미지가 표시되지 않는 동작이 발생할 수 있습니다. 다음 명령을 실행하여 문제를 해결하십시오.

```
seeder_lambda=$(aws lambda list-functions | jq -r '.Functions[].FunctionName | select(contains("ddbseeder"))')

aws lambda invoke --function-name $seeder_lambda --payload '{"mode":"create"}' --cli-binary-format raw-in-base64-out response.json

```

![App Home](/images/playaround/home.png)


아래와 같이 어플리케이션에서 여러 활동을 해보세요.

![App Home](/images/playaround/play.gif)
