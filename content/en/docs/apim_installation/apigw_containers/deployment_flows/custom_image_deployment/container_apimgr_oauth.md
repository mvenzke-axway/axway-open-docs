---
 title :  Deploy API Manager or OAuth in Docker containers 
 linkTitle :  Deploy API Manager or OAuth 
 weight: 36
 date :  2019-09-18 
 description :  Deploy API Manager or OAuth services in your API Gateway containers.
---

The instructions on this page are optional, and only for users who wish to use API Manager or OAuth in a containerized environment.

## Deploy API Manager

Follow this section to deploy API Manager in a Docker container.

### Configure API Manager in Policy Studio

Follow these steps to configure API Manager in Policy Studio.

1. In Policy Studio, open or create a new project.
2. Select **File > Configure API Manager**.
3. If you do not have any Cassandra hosts configured, you must add a Cassandra host before you can continue:
    * Enter a name for the Cassandra server (for example, `container_cassandra`).
    * Enter the name of the Cassandra container as the host name (for example, `cassandra228`).
    * Enter the port of the Cassandra container (for example, `9042`).
4. If you are not running Cassandra in a container, you must set the cassandra keyspace name to a static value (for example, `x123_mygroup`).
5. Click **Next**.
6. Enter the appropriate API Manager settings. For more information, see [Enable API Manager](/docs/apim_administration/apimgr_admin/api_mgmt_config#enable-api-manager).

    {{< alert title="Note" color="primary" >}}The default API administrator user name and password set in Policy Studio are used only when creating the administrator account in Apache Cassandra. After the account has been created in Cassandra, you cannot change the credentials in Policy Studio. You must use API Manager to change the administrator credentials. You can also reset the administrator password by running the `setup-apimanager` script with the option `--resetPassword` inside the Admin Node Manager container. For more information, see [Reset the default API administrator password](/docs/apim_installation/apigw_containers/deployment_flows/custom_image_deployment/container_troubleshoot#reset-the-default-api-administrator-password).{{< /alert >}}

7. Click **Finish**.
8. Configure additional API Manager settings under **Server Settings > API Manager**. For example, you can specify custom policies that are called as traffic passes through API Manager.
9. Select **File > Export** and select a package to export the configuration as a package (`fed`, `pol`, or `env`).

### Deploy API Manager-enabled API Gateway container

Follow the steps in [Create and start API Gateway Docker container](/docs/apim_installation/apigw_containers/deployment_flows/custom_image_deployment/docker_script_gwimage/) to deploy your API Manager-enabled API Gateway container.

When creating the API Gateway Docker image using `build_gw_image.py`, specify the deployment package you exported from Policy Studio. For an example, see [Create an API Manager or OAuth enabled API Gateway image](/docs/apim_installation/apigw_containers/deployment_flows/custom_image_deployment/docker_script_gwimage#create-an-api-manager-or-oauth-enabled-api-gateway-image).

## Deploy OAuth services

Follow this section to deploy OAuth services in a Docker container.

### Configure OAuth in Policy Studio

Follow this section to configure OAuth in Policy Studio.

1. In Policy Studio, open or create a new project.
2. Select **File > Configure OAuth**.
3. If you do not have any Cassandra hosts configured, you must add a Cassandra host before you can continue:
    * Enter a name for the Cassandra server (for example, `container_cassandra`).
    * Enter the name of the Cassandra container as the host name (for example, `cassandra228`).
    * Enter the port of the Cassandra container (for example, `9042`).
4. Click **Next**.
5. Select the OAuth deployment type. For more information, see [Deploy OAuth configuration](/docs/apim_policydev/apigw_oauth/gw_server#deploy-oauth-services).
6. Click **Finish**.
7. Select **File > Export** and select a package to export the configuration as a package (`fed`, `pol`, or `env`).

### Deploy OAuth-enabled API Gateway container

Follow the steps in [Create and start API Gateway Docker container](/docs/apim_installation/apigw_containers/deployment_flows/custom_image_deployment/docker_script_gwimage/) to deploy your OAuth-enabled API Gateway container.

When creating the API Gateway Docker image using `build_gw_image.py`, specify the deployment package you exported from Policy Studio. For an example, see [Create an API Manager or OAuth enabled API Gateway image](/docs/apim_installation/apigw_containers/deployment_flows/custom_image_deployment/docker_script_gwimage#create-an-api-manager-or-oauth-enabled-api-gateway-image).
