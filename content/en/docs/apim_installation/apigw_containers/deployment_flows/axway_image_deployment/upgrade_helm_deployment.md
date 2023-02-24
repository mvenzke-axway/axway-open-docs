---
title: Upgrade your Helm deployment
linkTitle: Upgrade your Helm deployment
weight: 28
date: 2023-01-24
hide_readingtime: true
description: How to upgrade the helm deployment to use a later version of the gateway.
---

The use of Helm charts and Docker images greatly improves and simplifies the API Gateway upgrade process. With each new release of the API Gateway, a new version of the Helm chart will also be released. This new version will have the latest images at the time of the release set as the default, along with any additional features or configuration that might have been added to the chart. If other images are released in between Helm chart releases, such as for patch or security fixes, it is up to the user to update their Helm configuration to pull these new images. The chart is not backward compatible, and you cannot use a new version of the chart and continue using older versions of the images.

### Prerequisites

While the actual process of upgrade is straightforward, there are some prerequisite steps that you must perform to prepare for the upgrade.

Primarily, it is critical that the configurations files are upgraded. Whether using FED or YAML configuration, the config must be imported and upgraded using the corresponding version of policy studio. Any other changes to make use of new features should also be made. It must then be exported and copied in to a pod's merge directory, as any externalized config usually is. [Sample container configurations](/docs/apim_installation/apigw_containers/deployment_flows/axway_image_deployment/helm_deployment#the-structure-of-configuration-directories)

### Upgrade Helm Chart using your existing YAML file

Once this is in place it is then possible to apply the helm upgrade. This is done with the following command:

```
helm upgrade <your_release_name> -f myvalues.yaml -n mynamespace <repository.axway.com>
```

When the new pods come up, they will come up with the upgraded configuration.

After the upgrade is finished, check the deployment, for example:

```bash
kubectl get pods -n mynamespace
```

Result:

```none
NAME                                       READY     STATUS    RESTARTS   AGE
apim-gateway-aga-866946bb58-vsqlx          1/1       Running   0          72s
apim-gateway-anm-58b5644777-jbzfl          1/1       Running   0          72s
apim-gateway-apimgr-654fcd4744-xqxcw       1/1       Running   0          72s
apim-gateway-apitraffic-6fc46d6788-s4mk9   1/1       Running   0          72s
```
