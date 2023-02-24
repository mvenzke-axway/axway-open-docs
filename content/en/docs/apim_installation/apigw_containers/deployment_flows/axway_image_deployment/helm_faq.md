---
title: Frequently Asked Questions
linkTitle: FAQ
weight: 28
date: 2022-01-23
description: Get answers to frequently asked questions (FAQ) about deploying API Gateway using Axway's Helm chart and images.
---

## How do you deploy changes in configuration from Policy Studio?

In the provided `values.yaml` file, the environment variable `EMT_DEPLOYMENT_ENABLED` is set to `true` by default. This enables you to deploy changes directly from Policy Studio to the API Manager and API Gateway containers. This method of updating the configuration will not persist when the container restarts, and so it is only recommended for development environments. Also, it is not possible to update the Admin Node Manager configuration in this way.

In a production environment we recommend you to follow these steps:

1. Change the setting of `EMT_DEPLOYMENT_ENABLED` to `false` in your local `values` file to disable this option.
2. Export any changes you make via Policy Studio to a FED file (for a FED based configuration) or `.tar.gz` (for a YAML based configuration) file.
3. Use the FED or tar.gz file to update the configuration by copying the file to the `/merge/fed` or `/merge/yaml` directory in the pod.
4. Run a `kubectl` rollout restart of the deployment.

### Example using a FED file

For example, to update the ANM FED file with one that has been changed in Policy Studio, you first get the ANM FED configuration from the pod:

```bash
kubectl cp apim-gateway-anm-d99989f5d-xt65x:/opt/Axway/apigateway/conf/fed my-anm-fed
```

Next, use the Policy Studio option to create a project from an existing configuration to import the `my-anm-fed` directory. Make changes and export the changes as, for example,`my-changed-anm.fed`. Then, copy this file back to the merge directory of the pod and restart the ANM deployment. For example:

```bash
kubectl cp my-changed-anm.fed apim-gateway-anm-d99989f5d-xt65x:/merge/fed
kubectl rollout restart deployment apim-gateway-anm
```

### Example using a YAML file

Similarly, if using YAML, you must export the changes you made via Policy Studio to a `.tar.gz` file, copy this file as `/merge/yaml` in the pod, and run a `kubectl` rollout restart of the deployment. For example, to change the the gateway YAML file, first get the file from the pod:

```bash
kubectl cp apim-gateway-apitraffic-66fd47f896-466dw:/opt/Axway/apigateway/groups/emt-group/conf my-yaml
```

Next, use the Policy Studio option to create a project from existing configuration to import the `my-yaml` directory. Make changes and export the changes as, for example, `my-changed-yaml.tar.gz`. Then, copy this file back to the merge directory of the `apitraffic` deployment. For example:

```bash
kubectl cp my-changed-yaml.tar.gz apim-gateway-apitraffic-66fd47f896-466dw:/merge/yaml
kubectl rollout restart deployment apim-gateway-apitraffic
```

{{< alert title="Note" color="primary" >}}In general, when you make configuration changes to the `apitraffic` deployment you should also apply the same changes to the `apimgr` deployment. You can do this by copying the same changed FED or YAML file to the `apimgr` pod merge directory and run `kubectl rollout restart deployment` on the `apimgr` deployment. {{< /alert >}}

## How do you promote configuration through environments?

The promotion flow in a helm deployment is very similar to a classic deployment. However, configuration updates in helm currently require either a `.fed` or `.tar.gz` file, therefore you must export the policy package (`.pol` file) and environment package (.`env` file) from Policy Studio into a single FED or YAML file.

The exported configuration from one environment can then be copied to the merge directory of the pod in the target environment.

The steps are identical to those described at [How do you deploy changes in configuration from Policy Studio](/docs/apim_installation/apigw_containers/deployment_flows/custom_image_deployment/container_faq/#how-do-you-deploy-changes-in-configuration-from-policy-studio) for production environments, except in this case, the exported FED or YAML file is coming from a development or pre-production environment.

## How do you promote APIs through environments?

In a Helm deployment, you can promote APIs registered in API Manager in the same way as in a classic environment. For more information, see [Promote managed APIs between environments](/docs/apim_administration/apimgr_admin/api_mgmt_promote/).

## What API Gateway Manager functionality is not available?

In a Helm deployment, you cannot perform the following in API Gateway Manager:

* You cannot create or delete API Gateway groups and instances.
* You cannot start or stop API Gateways.
* You cannot deploy configuration packages to a group of API Gateway instances.

For more information, see [Operate and monitor API Gateway containers](/docs/apim_installation/apigw_containers/container_operations).

## How do you persist logs?

Logs can be persisted by writing them to a directory that is mounted to a persistent volume. An example of adding the volume mount and volume claim to the local `values` file can be found at [Modify other areas of the deployment](/docs/apim_installation/apigw_containers/deployment_flows/axway_image_deployment/helm_runtime/#modify-other-areas-of-the-deployment).

For more information on using volumes to persist data, see the [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/) Kubernetes documentation

## How do you customize audit logs?

You can use the following environment variables to customize the audit log directory and filename :

* **APIGW_AUDIT_LOG_DIR**: The directory into which audit logs are placed. Defaults to `/opt/Axway/apigateway/groups/emt-group/emt-service/logs` for the API Gateway and API Manager, and to `/opt/Axway/apigateway/logs` for the Admin Node Manager. You can modify the value of this environment variable by adding the environment variable to the `values` file and upgrading the Helm chart, or by editing the deployment directly using the `kubectl` command, for example: `kubectl edit deployment apim-apigateway-anm`.
* **APIGW_AUDIT_LOG_NAME**: The name of the audit log file defaults to `audit.log`. You can update the value of this environment variable in the same way as for the audit log directory above.

If there is a requirement to persist the audit logs to a directory on your host machine, a mounted volume can be employed.

An alternative method of customizing the audit log directory and filename is by using a jvm.xml file, as follows:

1. Copy the JVM file from the pod. The file is stored `/opt/Axway/apigateway/system/conf/jvm.xml`, so for example, you can run:

   ```
   kubectl cp <pod name>:/opt/Axway/apigateway/system/conf/jvm.xml myjvm.xml
   ```
2. Make the modifications you wish. For example you can add the following lines:

   ```
   <ConfigurationFragment>
       <Environment name="APIGW_AUDIT_LOG_DIR" value="$VINSTDIR/logs/$GROUPNAME"/>
       <Environment name="APIGW_AUDIT_LOG_NAME" value="$INSTANCENAME-audit.log"/>
   </ConfigurationFragment>
   ```

3. Copy the modified file to /merge/apigateway/system/conf/jvm.xml in the pod.
4. Run the following command to apply the changes

   ```
   kubectl restart rollout deployment <deployment name>
   ```

## How do you customize logs in a multi-node environment?

You can customize the logs produced by API Gateway in Policy Studio by way of the following environment variables:

* HOSTNAME: `${environment.HOSTNAME}`
* GROUPNAME:`${environment.GROUPNAME}`
* INSTANCENAME:`${environment.INSTANCENAME}`

These variables allow you to make the configuration generic, so that you can create unique log directories and filenames across a multi-node container environment.

To customize logging for a multi-node environment, perform the following steps:

1. In Policy Studio, create a project [from a FED file](/docs/apim_policydev/apigw_poldev/gs_project#new-project-from-a-fed-file), pointing it to an existing API Gateway FED. Alternatively, create a project [from an existing configuration](/docs/apim_policydev/apigw_poldev/gs_project#new-project-from-existing-configuration) pointing to an existing directory that contains an XML or YAML configuration.
2. Click **Server Settings > Logging**.
3. Click **Transaction Audit Log** and configure where you wish API Gateway to log transaction audit information:

   To log to a text file, select the **Text File** tab and configure the following:
   * Check **Enable logging to file**.
   * Set **File Name** to `transactionLog-${environment.HOSTNAME}`.
   * Set **Directory** to `logs/transaction-${environment.HOSTNAME}`.

   To log to an XML file, select the **XML File** tab and configure the following:
   * Check **Enable logging to XML file**.
   * Set **File Name** to `transactionLog-${environment.HOSTNAME}`.
   * Set **Directory** to `logs/transaction-${environment.HOSTNAME}`.
4. Click **Transaction Access Log** and configure where you wish the API Gateway to log transaction access information:
    * Check **Writing to Transaction Access Log**.
    * Set **File Name** to `access-${environment.HOSTNAME}`.
    * Set **Directory** to `logs/access-${environment.HOSTNAME}`.
5. Click **Transaction Event Log** and configure where you wish API Gateway to log transaction event information:
    * Check **Writing to Transaction Event Log**.
    * Set **Write transaction event logs to directory** to `logs/events-${environment.HOSTNAME}`.
6. Click **Traffic Monitor** and configure where you wish API Gateway to log traffic information:
    * Set **Transaction Directory** to `conf/opsdb.d/opsdb-${environment.HOSTNAME}`.

See section [How do you deploy changes in configuration from Policy Studio](#how-do-you-deploy-changes-in-configuration-from-policy-studio) for details on how to upgrade from Policy Studio.

## What license do you need to run?

You must have an appropriate license to run API Gateway or API Manager in a container. For more information, see [Include a license configuration](/docs/apim_installation/apigw_containers/deployment_flows/axway_image_deployment/helm_deployment#include-a-license-configuration)

## How do you upgrade from API Gateway 7.5.3 to 7.7?

To upgrade from API Gateway 7.5.3 (classic deployment) to API Gateway 7.7 (Helm deployment), you must first upgrade to a 7.7 classic deployment, and then migrate to a 7.7 Helm deployment.

For information on upgrading to API Gateway 7.7, see [API Gateway Upgrade Guide](/docs/apim_installation/apigw_upgrade/), and for more details on migrating to a Helm deployment, see [Migrate to container deployment](/docs/apim_installation/apigw_containers/container_migration).

## How do you manage API Gateway topology?

In a classic deployment, topology is managed internally through an Admin Node Manager communicating with Node Managers in API Gateway nodes.

In a Helm deployment, the topology is externally managed by a cluster manager (Openshift or Kubernetes), and it manages the topology and communicates directly with each API Gateway. In this case, you cannot use the `managedomain` script or the API Gateway Manager web UI to manage your topology. Instead, you must use an orchestration tool. For more information on Openshift, see [Openshift Platform](https://docs.openshift.com/container-platform/4.9/welcome/index.html), or for more information on Kubernetes, see [Kubernetes](https://kubernetes.io/).

API Gateway running in containers is fully supported on Openshift Container Platform (4.9) using an Axway provided Helm chart and Red Hat Universal Base Image 7 (UBI 7) based images built using the latest version of the containerization scripts.

## How do you run API Gateway administration tools or scripts?

In a Helm deployment, you must connect to the running Admin Node Manager Docker container to run API Gateway administration tools, such as `kpsadmin`. For an example, see [Manage KPS with kpsadmin](/docs/apim_installation/apigw_containers/container_troubleshoot#manage-kps-with-kpsadmin).

## How do you set up API Manager metrics?

To set up API Manager metrics you must add a JDBC driver JAR file for the metrics database to the `apigateway/ext/lib` directory of the merge directory of the Admin Node Manager pod image, and then run `kubectl rollout restart <ANM deployment>`.

When starting the containers, you must also specify the connection details for the metrics database using environment variables in your override local `values` file.
