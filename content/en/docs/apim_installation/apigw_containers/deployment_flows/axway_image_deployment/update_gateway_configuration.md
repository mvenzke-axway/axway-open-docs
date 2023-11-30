---
title: Update API Gateway configuration
linkTitle: Update API Gateway configuration
weight: 24
date: 2023-01-24
hide_readingtime: true
description: How to update the configuration of the API Gateway Helm deployment.
---

The Admin Node Manager, API Gateway, and Analytics images are built with a default configuration. This configuration can be updated at runtime by externalizing the customized configuration files.

### Update API Manager and API Traffic configuration

The API Gateway image used by the API Manager and API Traffic pods is built with YAML configuration by default, but it can be updated with FED configuration. Updates to the configuration are done by externalizing the deployment package (FED or YAML).

Fed files or YAML files can be copied to the persistent volumes using tools, such as `kubectl` and `scp`.

Configuration changes to the API Gateway image must be applied to both the API Manager deployment and the API Traffic deployment, but, because API Manager and API Traffic pods share the same persistent volume claim, you only need to copy the configuration file to one of these directories.

* If using a fed file, it must be copied to the `/merge` directory and have the name `fed`.
* If using a YAML `tar.gz` file, it must be copied to the `/merge` directory and have the name `yaml`.

The following procedure is an example of how to update the fed file of the API Manager and API Traffic pods.

1. Copy the fed file, for example, to the API Manager pod:

    ```bash
    kubectl cp apimgr.fed <apimgr pod id>:/merge/fed
    ```

2. Reload the API Manager deployment

    ```bash
    kubectl rollout restart deployment apigw-gateway-apimgr -n <namespace>
    ```

3. Reload the API Traffic deployment

   ```bash
   kubectl rollout restart deployment apigw-gateway-apitraffic -n <namespace>
   ```

Wait for some minutes while the rollout completes successfully.

### Update Other configuration in API Manager and API Traffic pods

Updating other configuration files might be done at the same time as updating the fed or YAML files, or at a later stage. These configuration files must be copied to the `/merge` directory in a directory structure as outlined in [The structure of configuration directories](/docs/apim_installation/apigw_containers/deployment_flows/axway_image_deployment/helm_deployment#the-structure-of-configuration-directories) section.

The following procedure is an example of how to update the configuration of the API Manager and API Traffic pods with a `jvm.xml` file.

1. Create a configuration directory `apigateway` with the appropriate subdirectories and configuration files:

    ```bash
    mkdir -f apigateway/system/conf
    cp jvm.xml apigateway/system/conf
    ```

2. Copy the `apigateway` directory to the API Manager pod:

    ```bash
    kubectl cp apigateway <apimgr pod id>:/merge/
    ```

3. Copy the same `apigateway` directory to any of the API Traffic pods:

    ```bash
    kubectl cp apigateway <apitraffic pod id>:/merge/
    ```

4. Reload the API Manager deployment:

    ```bash
    kubectl rollout restart deployment apigw-gateway-apimgr -n <namespace>
    ```

5. Reload the API Traffic deployment:

    ```bash
    kubectl rollout restart deployment apigw-gateway-apitraffic -n <namespace>
    ```

Wait for some minutes while the rollout completes successfully.

### Update Admin Node Manager configuration

You can update the Admin Node Manager deployment in the same way as for the API Manager and API Traffic deployments.

The following procedure is an example of how to update the configuration of the Admin Node Manager with an updated `adminUsers.json` file.

{{< alert title="Note" color="primary" >}}
When running in EMT mode, the addition of new admin users is disabled on the API Gateway User Interface. Currently, the only way to add extra admin users is to create them on an on-premise deployment, then save the generated `adminUsers.json` and upload it to the EMT Admin Node Manager pod and restart the deployment.
{{< /alert >}}

1. Create a configuration directory `apigateway` with the appropriate subdirectories and configuration files:

    ```bash
    mkdir -f apigateway/conf
    cp adminUsers.json apigateway/conf
    ```

2. Copy the `apigateway` directory to the Admin Node Manager:

    ```bash
    kubectl cp apigateway <anm pod id>:/merge/
    ```

3. Reload the Admin Node Manager deployment:

    ```bash
    kubectl rollout restart deployment apigw-gateway-anm -n <namespace>
    ```

For more information on mount configuration, see [Sample customized values file](/docs/apim_installation/apigw_containers/deployment_flows/axway_image_deployment/helm_deployment#sample-customized-values-file).

### Update Analytics configuration

You can update the Analytics deployment in the same way as for the other deployments.

The following procedure is an example of how to update the configuration of the Analytics deployment with a MySQL jar file.

1. Create a configuration directory `analytics` with the appropriate subdirectories and configuration files

    ```bash
    mkdir -f analytics/lib/ext
    cp mysql-connector-java-8.0.5.jar analytics/lib/ext
    ```

2. Copy the `analytics` directory to the Analytics pod merge directory

    ```bash
    kubectl cp analytics <aga pod id>:/merge/
    ```

3. Reload the Analytics deployment

    ```bash
    kubectl rollout restart deployment apigw-gateway-aga -n <namespace>
    ```

Wait for some minutes while the rollout completes successfully.

### Promoting configuration between environments

To simplify promotion between environments, it is recommended that you environmentalize as much of the configuration as possible. When done correctly, the same configuration files can be used between environments, with any changes between environments being set through the helm `values.yaml` file. For more information, see [Environment variables reference](/docs/apim_installation/apigw_containers/deployment_flows/axway_image_deployment/helm_runtime#environment-variables-reference).
