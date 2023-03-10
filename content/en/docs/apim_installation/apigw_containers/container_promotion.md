---
 title :  Promote configuration between environments
 linkTitle :  Promote configuration
 weight: 95
 date :  2019-09-18 
 description : Develop and test APIs and policies in a development environment, and promote and deploy them in other environments (for example, pre-production and production).
---

In a typical API Gateway container deployment, you can create Docker images in your build or continuous integration (CI) environment from various artifacts checked out of your source control system. The resulting images are versioned and immutable. Then, you can start your Docker containers from the immutable images in a specific runtime environment (for example, development, pre-production, or production).

Alternatively, you can create images and start containers in a single CI/CD pipeline, where artifact updates trigger a build that results in the automatic creation or refresh of an execution environment.

## Test in development environment

A development environment works in a continuous cycle of iterative development, deployment, and testing. In this environment, it makes sense to keep all API Gateway configuration in a single deployment package (FED file), which you can deploy or export from Policy Studio.

You have three different options when testing in a development environment:

### Use an API Gateway that is running in classic mode

To test changes when using an API Gateway that is running in classic (non-containerized) mode, you can deploy configuration changes directly from Policy Studio.

### Use a deployment-enabled API Gateway container

To test changes when using a deployment-enabled API Gateway container, you can deploy directly from Policy Studio to the running container as follows:

1. When starting the API Gateway Docker container, specify `EMT_DEPLOYMENT_ENABLED=true`. For an example, see [Start a deployment-enabled API Gateway container](/docs/apim_installation/apigw_containers/docker_script_gwimage#start-a-deployment-enabled-api-gateway-container-in-a-development-environment).
2. Make the configuration changes to be tested in Policy Studio and click **Deploy** in the toolbar to deploy the updated configuration to the running API Gateway container.

### Use an API Gateway container that does not have deployment enabled

 To test changes when using an API Gateway container that does not have deployment enabled, you must export the configuration from Policy Studio and use it to generate a new API Gateway image and start a new container as follows:

1. Make the configuration changes to be tested in Policy Studio and select **File > Export > Deployment Package** to export the configuration as a deployment package (FED).
2. When creating the API Gateway Docker image using `build_gw_image.py`, specify the deployment package you exported from Policy Studio. For an example, see [Create an API Gateway image using existing fed and customized configuration](/docs/apim_installation/apigw_containers/docker_script_gwimage#create-an-api-gateway-image-using-existing-fed-and-customized-configuration).

## Promote to pre-production environment

After the configuration is deployed and tested in the development environment, you can promote it to the pre-production environment. This involves environmentalizing the configuration that will require environment-specific settings in upstream environments and exporting it as a policy package (POL file) from Policy Studio, and creating an environment package (`.env` file) that is specific to the pre-production environment in Configuration Studio.

To promote a configuration to a pre-production environment, follow these steps:

1. Environmentalize the configuration in Policy Studio and select **File > Export > Policy Package** to export the configuration as a policy package (POL).
2. Create the environment package for the pre-production environment in Configuration Studio, and select **File > Save > Environment Package** to export the environment package (`.env`).
3. When creating the API Gateway Docker image using `build_gw_image.py`, specify the policy package and pre-production environment package you exported from Policy Studio and Configuration Studio. For reference, see [Create an API Gateway image using existing fed and customized configuration](/docs/apim_installation/apigw_containers/docker_script_gwimage#create-an-api-gateway-image-using-existing-fed-and-customized-configuration), however, in this case you must specify a POL and `.env` files instead of a FED file.

For a detailed example of promoting configuration through environments, see [Example: Promote from development to testing environment](/docs/apigtw_devops/promotion_example/).

## Promote to production environment

After the configuration is deployed and tested in the pre-production environment, you can promote it to the production environment. This involves creating an environment package (`.env` file) that is specific to the production environment in Configuration Studio.

To promote a configuration to a production environment, follow these steps:

1. Create the environment package for the production environment in Configuration Studio, and select **File > Save > Environment Package** to export the environment package (`.env`).
2. When creating the API Gateway Docker image using `build_gw_image.py`, specify the policy package and production environment package you exported from Policy Studio and Configuration Studio. For reference, see [Create an API Gateway image using existing fed and customized configuration](/docs/apim_installation/apigw_containers/docker_script_gwimage#create-an-api-gateway-image-using-existing-fed-and-customized-configuration), however, in this case you must specify a POL and `env` files instead of a FED file.

For a detailed example of promoting configuration through environments, see [Example: Promote from development to testing environment](/docs/apigtw_devops/promotion_example/).

## Continuous policy deployment

You can continuously deploy policy updates in a container environment as part of your CI/CD process by scripting the steps detailed in [Create and start API Gateway Docker container](/docs/apim_installation/apigw_containers/docker_script_gwimage/) and specifying the latest policy package (POL) exported from the development environment and the environment package (`.env`) for the production environment to the `build_gw_image.py` script.

## Promote APIs registered in API Manager

You can promote APIs registered in API Manager using the approaches described in [Promote managed APIs between environments](/docs/apim_administration/apimgr_admin/api_mgmt_promote/).
