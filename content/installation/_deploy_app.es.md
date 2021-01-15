+++
title = "Desplegar la aplicación"
menuTitle = "2.3 Desplegar la aplicación"
date = 2020-08-31T14:02:35-04:00
weight = 30
+++

### Configure los permisos IAM de Cloud9

{{% notice info%}}
Cloud9 normalmente administra los permisos de IAM de forma dinámica. Actualmente, esto no es compatible con el complemento aws-iam-authenticator, por lo tanto precederemos a desactivar esta función a fin de usar un rol de IAM en su lugar.
{{% /notice %}}

- Regrese al espacio de trabajo y seleccione la opción del engranaje, o bien abra una nueva pestaña para ver las `Preferences`
- Seleccione `AWS SETTINGS`
- Desactive `AWS managed temporary credentials`
- Cierre la pestaña `Preferences`
![c9disableiam](/images/c9disableiam.png)

- Para asegurar que el ambiente no cuenta con credenciales temporales, precedemos a eliminar cualquier archivo de credenciales existente:

```
rm -vf ${HOME}/.aws/credentials
```

### Instale las herramientas y clone el repositorio de código <span style="color: blue;">(omita este paso si esta usando Event Engine)</span>

Este paso instala todas la herramientas y utilidades necesarias así como descarga el código fuente requerido para el taller.

```
curl -sSL https://raw.githubusercontent.com/aws-samples/one-observability-demo/main/PetAdoptions/envsetup.sh | bash -s stable
```

### Debemos configurar AWS CLI con la región de AWS a usar por defecto

```bash
export ACCOUNT_ID=$(aws sts get-caller-identity --output text --query Account)
export AWS_REGION=$(curl -s 169.254.169.254/latest/dynamic/instance-identity/document | jq -r '.region')
echo "export ACCOUNT_ID=${ACCOUNT_ID}" | tee -a ~/.bash_profile
echo "export AWS_REGION=${AWS_REGION}" | tee -a ~/.bash_profile
aws configure set default.region ${AWS_REGION}
aws configure get default.region
```

### Validar las configuraciones del ambiente

{{%notice info%}}
Si usted no está usando Cloud9, ignore el resultado de la validación del rol para el ambiente de Cloud9 y continue con la [Instalación del paquete CDK](#instalación-del-paquete-cdk)
{{%/notice%}} 

```bash
test -n "$AWS_REGION" && echo AWS_REGION is "$AWS_REGION" || echo AWS_REGION is not set

aws sts get-caller-identity --query Arn | grep observabilityworkshop-admin -q && echo "You're good. IAM role IS valid." || echo "IAM role NOT valid. DO NOT PROCEED."
```

Si usted está usando Cloud9, y la validación del rol de IAM falla, <span style="color: red;">**NO PROCEDA**</span>. Vuelva sobre sus pasos y confirme que los mismos se ejecutaron en la forma indicada..

### Instalación del paquete CDK

Una vez haya completado este proceso exitósamente, navegue a la carpeta `pet_stack` usando el siguiente comando

```
cd workshopfiles/one-observability-demo/PetAdoptions/cdk/pet_stack
```

Instale todos los paquetes de npm

```
npm install
```

### Edite los parámetros de configuración

{{%notice info%}}
Este paso es opcional. Sólo cambie los valores del archivo de configuración si lo necesita. Vea los comentarios en cada una de las líneas para ayudarlo a tomar su decisión.
{{% /notice%}}

Abra el archivo `/pet_stack/cdk.json` en la pestaña de exploración en el lado izquierdo del ambiente de trabajo.

Lea las instrucciones en los comentarios de cada línea y asegúrese de seguir las mismas correctamente.

{{%notice info%}}
Si desea desplegar el clúster de EKS, asigne al campo `petsite_on_eks` el valor `true`
{{% /notice%}}

```bash
{
  "app": "npx ts-node app/pet_stack.ts",
  "context": {
    "vpc_cidr": "11.0.0.0/16",# Cambie este valor si este rango CIDR presenta un conflicto con su ambiente pre-existente
        "snstopic_email": "someone@example.com", # Cambie este valor a una dirección de correo que pueda acceder si desea recibir las notificaciones. Recibirá miles de notificaciones de correo electrónico al incrementar el tráfico en la aplicación. ¡Tome esto como una advertencia!. Puede dejar el valor por defecto y no recibirá notificaciones de correo electrónico.
    "rdsusername":"petadmin",
    "petsite_on_eks":"false"# Si cambia este valor a "true" se creará un clúster EKS y se la aplicación web de front-end se desplegará en el. Deje este valor en "false" y todos los servicios se desplegarán en ECS
  }
}
```

### Reinicie la instancia EC2 para que los cambios surjan efecto <span style="color: blue;">(omita este paso si no desplegó petsite sobre EKS)</span>	

Ejecute el siguiente comando en el terminal de Cloud9 y espere algunos segundos para que la instancia se reinicie

```
sudo reboot
```

### Configuración inicial de CDK

{{%notice tip%}}
Si no se encuentra en la ruta `pet_stack` diríjase a la misma ejecutando el comando - ```cd workshopfiles/one-observability-demo/PetAdoptions/cdk/pet_stack```
{{%/notice%}}

```
cdk bootstrap
```

### Despliegue la pila

```
cdk deploy Services
```

Tomará algunos minutos para desplegar la pila, así que vaya a buscar un <span style=font-size:40px> ☕️ </span>
