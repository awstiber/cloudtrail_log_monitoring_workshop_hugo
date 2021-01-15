+++
title = "Conociendo la aplicación"
menuTitle = "2.5 Conociendo la aplicación"
date = 2020-05-06T16:24:15-04:00
weight = 40
+++

Ejecute el siguiente comando en el terminal.

```bash
aws ssm get-parameter --name '/petstore/petsiteurl'  | jq -r .Parameter.Value
```
Diríjase a la dirección URL que obtuvo como resultado de este comando. Usted deberá ver la pantalla de inicio de la aplicación como se muestra a continuación

![App Home](/images/playaround/home.png)

#### Solución de problemas

En algunos casos aislados, se puede presentar un comportamiento en el cual el portal no muestra la imágen de ninguna mascota. Ejecute los siguientes comandos para resolver este problema.

```
seeder_lambda=$(aws lambda list-functions | jq -r '.Functions[].FunctionName | select(contains("ddbseeder"))')

aws lambda invoke --function-name $seeder_lambda --payload '{"mode":"create"}' --cli-binary-format raw-in-base64-out response.json

```

Debería poder navegar en la aplicación como se muestra en la imágen que sigue a continuación.

![App Home](/images/playaround/play.gif)
