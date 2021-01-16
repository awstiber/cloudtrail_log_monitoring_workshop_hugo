+++
title = "Play around with the app"
menuTitle = "2.6 Play around with the app"
date = 2020-05-06T16:24:15-04:00
weight = 40
+++

Execute the following command on the terminal. 

```bash
aws ssm get-parameter --name '/petstore/petsiteurl'  | jq -r .Parameter.Value
```
Navigate to the URL that was returned as a result. You should be able to see the app home screen as shown below 

#### Troubleshooting

In very rare cases, you might encounter a behavior where the site does not show any pet images. Execute the following commands to fix the problem.

```
seeder_lambda=$(aws lambda list-functions | jq -r '.Functions[].FunctionName | select(contains("ddbseeder"))')

aws lambda invoke --function-name $seeder_lambda --payload '{"mode":"create"}' --cli-binary-format raw-in-base64-out response.json

```

![App Home](/images/playaround/home.png)


You can navigate through the application as shown below.

![App Home](/images/playaround/play.gif)
