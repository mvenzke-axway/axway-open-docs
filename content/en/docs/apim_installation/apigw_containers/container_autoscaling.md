---
title: Enable autoscaling
linkTitle: Enable autoscaling
weight: 105
date: 2023-05-20
description: Learn what autoscaling is in the context of API Gateway Helm chart, and how to enable this feature in your cluster.
---

Autoscaling in the API Gateway Helm solution refers to Kubernetes Horizontal Pod Autoscaler (HPA). The intent behind HPA is that the number of pods in a deployment will increase during a period of high workload and decrease again when the workload decreases. For more information on this topic, see [Kubernetes](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) documentation.

The autoscaling feature is available for all deployments; however, it is mainly recommended to be used for the `apitraffic` deployment.

Autoscaling is disabled by default, and, when not enabled, the number of replicas required for each deployment is determined by a hardcoded value called, `replicaCount`.

## Enable autoscaling

To enable Horizontal Pod Autoscalling, you must update your `values` file where `autoscaling` and `resources` are defined.

### Define Resource values

In your custom `values` file, under the deployment for which you wish to enable autoscaling (`apitraffic`), ensure that you have completed the `resources` section. This section defines two properties:

* `limits`: Defines the maximum amount of CPU and memory that each pod must have.
* `requests`: Defines the minimum amount of CPU and memory that each pod must have. This allows kubernetes to allocate the necessary resources after a new replica is added.

The example in this section shows how to set up your resources section. Note the following:

* The units used for both memory and CPU can be expressed in different ways. There are many units that can be used for memory.
* The example uses `M` for megabytes (1,000,000 bytes) and `Mi` for mebibytes (1,048,576 bytes).
* 1 CPU means 1 virtual CPU, or core.
* When `m` is placed after the number, it means millicpu. 1000m is equivalent to 1 CPU.

The following is an example on how to set up the `resources` section:

```yaml
  resources:
     limits:
       cpu: 1000m
       memory: 512M
     requests:
       cpu: 500m
      memory: 256M
```

For more information on setting memory and CPU limits, and the requests available, see [Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/) documentation.

### Define Autoscaling values

Create (or, go to) the `autoscaling` section of each deployment for which autoscaling is required and ensure the following parameters are defined and set appropriately:

* `enabled`: Determines whether autoscaling is enabled or not.
* `minReplicas`: The minimum number of replicas that will be available in the cluster.
* `maxReplicas`: the maximum number of replicas that will be available in the cluster.
* `targetCPUUtilizationPercentage`: The percentage of CPU that will be used as a reference to scale up or down, based on the requested CPU value defined in the resources. When this percentage is reached (based on the average CPU used by the pods), a new replica will be created.
* `targetMemoryUtilizationPercentage`: The percentage of memory that will be used as a reference to scale up or down, based on the requested memory value defined in the resources. When this percentage is reached (based on the average memory used by the pods), a new replica will be created.

Note that it is not required to use both `targetMemoryUtilizationPercentage` and `targetCPUUtilizationPercentage`. One of these is sufficient.

The following is an example on how to set up the `properties` section:

```yaml
   autoscaling:
     enabled: true
     minReplicas: 1
     maxReplicas: 6
     targetCPUUtilizationPercentage: 80
     targetMemoryUtilizationPercentage: 80
```

{{< alert title="Note" color="primary" >}}Ensure that the nodes are large enough to accommodate the autoscaling requests. In other words, make sure that the sum total of the amount of CPU and memory resources requested by the maximum number of pods is available in the cluster.{{< /alert >}}