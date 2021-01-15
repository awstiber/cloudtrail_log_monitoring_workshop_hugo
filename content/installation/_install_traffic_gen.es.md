+++
title = "Instalar el generador de Tráfico"
menuTitle = "2.6 Instalar el generador de Tráfico"

date = 2020-05-06T16:24:15-04:00
weight = 50
+++

La aplicación de CDK, también creará la definición de una tarea para ECS Fargate que contiene un generador de tráfico para la aplicación. La aplicación de generación de tráfico creará, de forma automática, un alto volúmen de tráfico para ayudarlo en las actividades del taller.

#### Pasos para instalar el generador de Tráfico

* Diríjase a la consola [ECS Clusters](https://console.aws.amazon.com/ecs/home#/clusters)
* Seleccione cualquiera de los clúster ECS creados por CDK como se muestra a continuación
![Cluster](/images/trafficgen/traffic-cluster.png)
* Diríjase a la pestaña de **Tasks** y seleccione **Run new task**
![Cluster](/images/trafficgen/traffic-newtask.png)
* En la pantalla **Run Task** seleccione el tipo `Fargate` 
* Seleccione la definición de tarea que comienza por `Servicestrafficgenerator`. Asegúrese de seleccionar la VPC llamada `Services/Microservices`
* Seleccione uno de los sub-segmentos de red pública de la lista
* No cree un nuevo grupo de seguridad, y seleccione el grupo `default` de la lista como se muestra a continuación
![Security Group](/images/trafficgen/traffic-sg.png)
* Seleccione en el campo `Auto-Assign public IP` valor `ENABLED`
![Cluster](/images/trafficgen/traffic-runtask.png)
* Seleccione **Run Task** y espere unos 30 segundos hasta que el estado de la tarea se muestre como <span style="color: green;"> **RUNNING** </span>
![Cluster](/images/trafficgen/traffic-running.png)