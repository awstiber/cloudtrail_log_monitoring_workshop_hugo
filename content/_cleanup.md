+++
title = "CLEAN UP RESOURCES"
date = 2020-07-12T12:46:53-04:00
weight = 100
+++

## Delete the stack

{{% notice info%}}
Skip step 1 if you did not setup petsite on EKS cluster
{{% /notice %}}

#### 1. Delete Kubernetes objects from the EKS cluster

{{% notice warning%}}
This will break the EKS cluster removing needed services like coreDNS.
{{% /notice %}}

```
kubectl delete all --all -A
```

#### 2. Delete the Traffic Generator Fargate task

If you followed [these steps](/installation/_install_traffic_gen.html) to create a traffic generator task, go back to the same cluster and simply stop the task which will kill the task immediately

#### 3. Delete all resources

From `/cdk/pet_stack/` directory execute the following commands.

```
chmod +x ./resources/destroy_stack.sh
./resources/destroy_stack.sh
```
