+++
title = "Crear un Panel"
menuTitle = "11.1 Crear un Panel"
date = 2020-06-03T13:01:49-04:00
weight = 1
+++

### Crear un panel utilizando la consola de AWS 
Diríjase a [CloudWatch Dashboard](https://console.aws.amazon.com/cloudwatch/home?#dashboards:) y seleccione `Create dashboard`, introduzca el nombre `observability-dashboard` y seleccione `Create dashboard`. Observará una pantalla como la siguiente donde podrá escoger el tipo de *widget* que desea.

![AddWidget](/images/dashboard/dashboard1.png?classes=shadow)

Seleccione el gráfico `Line` y luego seleccione `Configure`. Esta acción lo llevará a una pantalla en la cual se muestran todos los espacios de nombre bajo los cuales las métricas están siendo recopiladas. Si la opción del espacio de nombre `ContainerInsights` se encuentra disponible, seleccione esta opción (esta opción sólo estará disponible si servicio petsite fue desplegado en EKS durante el proceso de configuración). Si no está disponible, seleccione el espacio de nombre `ECS/ContainerInsights`.

Por simplicidad, vamos a asumir que usted seleccionó `ContainerInsights` en el resto de las instrucciones. Si usted seleccionó otro espacio de nombre, haga pequeños ajustes a las instrucciones usted mismo ya que es muy sencillo de seguir de igual forma..

![Namespace](/images/dashboard/dashboard2.png?classes=shadow)

Seleccione una dimensión dentro del espacio de nombre y seleccione una métrica como por ejemplo el nivel de uso de CPU o de memoria como se muestra a continuación. Seleccione `Create Widget` para agregar este gráfico como un nuevo *widget* al panel.

![AddWidget2](/images/dashboard/dashboard3.png?classes=shadow)

Puede ajustar el tamaño del *widget* simplemente arrastrando la esquina inferior derecha del mismo. Seleccione la opción `Save dashboard` para guardar los cambios en el panel, no hacer esto tampoco guardará ningún otro cambio hecho.

{{%notice tip%}}
Aprenda más sobre las cuotas de servicio relacionado a los *widgets* así como los límtes de métricas por *widget*/panel  [aquí](https://docs.aws.amazon.com/es_es/AmazonCloudWatch/latest/monitoring/cloudwatch_limits.html).
{{% /notice%}}

También es posible agregar resultados de CloudWatch Logs Insights como un *widget*. Seleccione `Add widget` y luego  `Query results`, que lo llevará a la pantalla de CloudWatch Logs Insights.

![SelectLG](/images/dashboard/dashboard4.png?classes=shadow)

Copie la consulta que sigue en el campo de texto para la consulta y seleccione `Run query`.

```
fields @timestamp, @message
| sort @timestamp desc
| filter @message like /PetListAdoptions/
| stats count(@message) by bin(5m)
| limit 20
```

Su pantalla deberá parecer similar a la que se muestra a continuación. La consulta muestra el número de ocurrencias donde el texto `PetListAdoptions` fue hallado en los registros de datos. Ahora seleccione `Create widget`.

![SelectLGResults](/images/dashboard/dashboard5.png?classes=shadow)

Esto agregará un nuevo *widget* al panel como se muestra a continuación.

![AddWidget3](/images/dashboard/dashboard6.png?classes=shadow)


### Trabajando con paneles de CloudWatch usando AWS CLI

Usted puede realizar una variedad de operaciones con los paneles de CloudWatch Dashboards mediante AWS CLI. 
{{%notice tip%}}
Eche un vistazo a los comandos disponibles aquí - https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/index.html#cli-aws-cloudwatch
{{% /notice%}}

El siguiente comando regresa el panel en formato JSON.

```
aws cloudwatch get-dashboard --dashboard-name observability-dashboard
```

#### Crear un nuevo panel

Tome el siguiente texto json y cree un archivo en el directorio local. Ponga como nombre a este archivo `dashboard.json`. Asegúrese de reemplazar el marcador `<YOUR_AWS_REGION_HERE>`con la región AWS apropiada.

```json
{"widgets":[{"type":"metric","x":0,"y":0,"width":12,"height":6,"properties":{"view":"timeSeries","stacked":false,"metrics":[["ContainerInsights","pod_cpu_utilization","PodName","petsite-deployment","ClusterName","petsite","Namespace","default"]],"region":"<YOUR_AWS_REGION_HERE>"}}]}
```

Ejecute el siguiente comando el cual creará un nuevo panel de CloudWatch:

```
aws cloudwatch put-dashboard --dashboard-name cli-dashboard --dashboard-body $(cat dashboard.json)
```

Para ver la lista de paneles que existen en una cuenta, ejecute el siguiente comando

```
aws cloudwatch list-dashboards
```

También puede verificar el panel recién creado en la consola de AWS.
