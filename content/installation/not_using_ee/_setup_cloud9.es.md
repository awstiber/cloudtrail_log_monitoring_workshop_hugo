+++
title = "Configurar ambiente Cloud9"
menuTitle = "Configurar Cloud9"
date = 2020-08-31T13:25:50-04:00
weight = 20
pre = "<b>▶︎ </b>"
+++

Puede usar una nueva instancia de Cloud9 para desplegar la aplicación. Si no desea usar Cloud9, omita esta página y diríjase [al siguiente paso](/es/installation/_deploy_app.html)


{{%notice warning%}}
Usted puede escoger instalar parte de la aplicación en EKS en caso que desee explorar el monitoreo sobre esta plataforma. Antes de proceder, asegúrese que se encuentra en una región de AWS con soporte para EKS. Puede consultar aquí la lista de  [regiones soportadas](https://aws.amazon.com/es/about-aws/global-infrastructure/regional-product-services/?nc1=h_ls).
{{% /notice%}}


#### Ejecute los siguientes comandos para crear el ambiente de Cloud9

```
curl -O https://raw.githubusercontent.com/aws-samples/one-observability-demo/main/cloud9-cfn.yaml

aws cloudformation create-stack --stack-name C9-Observability-Workshop --template-body file://cloud9-cfn.yaml --capabilities CAPABILITY_NAMED_IAM

```

