+++
title = "Install Traffic Generator"
menuTitle = "2.7 Install Traffic Generator"

date = 2020-05-06T16:24:15-04:00
weight = 50
+++

The CDK app also creates an ECS Fargate task definition with a traffic generator container in it. The traffic generator app will automatically create a lot of traffic to help with other workshop activities.

#### Steps to install traffic generator

* Go to [ECS Clusters](https://console.aws.amazon.com/ecs/home#/clusters)
* Select any one of ECS clusters created by the CDK deployment as shown below
![Cluster](/images/trafficgen/traffic-cluster.png)
* Go to **Tasks** tab  and click on **Run new task**
![Cluster](/images/trafficgen/traffic-newtask.png)
* In the **Run Task** screen select launch type as `Fargate` 
* Choose the Task definition that starts with `Servicestrafficgenerator`. Ensure you select the VPC named `Services/Microservices`
* Select one of the public subnets from the subnet list
* Do not create a new security group and select the `default` security group from the list as shown below
![Security Group](/images/trafficgen/traffic-sg.png)

* Set `Auto-Assign public IP` to `ENABLED`
![Cluster](/images/trafficgen/traffic-runtask.png)
* Click **Run Task** and wait for 30 seconds until the task status is shown as <span style="color: green;"> **RUNNING** </span>
![Cluster](/images/trafficgen/traffic-running.png)