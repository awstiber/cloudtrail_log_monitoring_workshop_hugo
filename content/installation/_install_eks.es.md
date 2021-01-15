+++
title = "Instalar Petsite en EKS"
menuTitle = "2.4 Instalar Petsite en EKS"
date = 2020-09-02T11:58:20-04:00
weight = 35
+++

{{% notice info%}}
Esta sección sólo aplica si usted decidió instalar el portal web de la aplicación petsite sobre EKS en el paso anterior. Si no lo hizo, omita esta página y vaya a la [siguiente](/es/installation/_using_the_app.html)
{{% /notice%}}

### Actualizar kubeconfig

Ejecute los siguientes comandos para actualizar kubeconfig de manera que pueda interactuar con el clúster EKS

```bash
STACK_NAME=$(aws ssm get-parameter --name '/petstore/stackname' --region $AWS_REGION | jq .Parameter.Value -r)

$(aws cloudformation describe-stacks  --stack-name $STACK_NAME | jq '.Stacks[0].Outputs[] | select(.OutputKey | contains("ConfigCommand")).OutputValue' -r)
```

Luego de ejecutado este comando, debe obtener un resultado como el que se muestra en la siguiente captura de pantalla
![Kubeconfig](/images/eks_kubeconfig.png)


### Desplegar la aplicación PetSite en EKS

En el terminal, desde la ruta `/cdk/pet_stack/` ejecute los siguientes comandos

```
chmod +x resources/eksdeployment.sh
./resources/eksdeployment.sh
```

Debe obtener un resultado similar al que se muestra a continuación
```
❯ ./eksdeployment.sh
-----------------------------------------------------------------
This script deploys petsite and the xray daemon to the EKS cluster
-----------------------------------------------------------------
deployment.apps/petsite-deployment created
service/service-petsite created
serviceaccount/xray-daemon created
clusterrolebinding.rbac.authorization.k8s.io/xray-daemon created
daemonset.apps/xray-daemon created
configmap/xray-config created
service/xray-service created
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 15552  100 15552    0     0  16627      0 --:--:-- --:--:-- --:--:-- 16615
namespace/amazon-cloudwatch created
serviceaccount/cloudwatch-agent created
clusterrole.rbac.authorization.k8s.io/cloudwatch-agent-role created
clusterrolebinding.rbac.authorization.k8s.io/cloudwatch-agent-role-binding created
configmap/cwagentconfig created
daemonset.apps/cloudwatch-agent created
configmap/cluster-info created
serviceaccount/fluentd created
clusterrole.rbac.authorization.k8s.io/fluentd-role created
clusterrolebinding.rbac.authorization.k8s.io/fluentd-role-binding created
configmap/fluentd-config created
daemonset.apps/fluentd-cloudwatch created
----- Creating SSM Parameter -----
----- ✅ DONE --------
```

{{%notice tip%}}
Si recibe un error similar a este: `jq: error (at <stdin>:35): Cannot iterate over null (null)`, es debido a que el balanceador de carga (ELB) tardó más de los normal en crearse y falló el intento de obtener su dirección. Espere unos 15 segundos para asegurarse que el balanceador de carga haya sido creado y ejecute el comando `./eksdeployment.sh ` nuevamente.
{{% /notice%}}

{{%notice warning%}}
Recuerde [liberar los recursos](/es/_cleanup.html) una vez concluya con este taller. Si no lo hace puede conllevar cargos inesperados por uso en su cuenta.
{{% /notice%}}