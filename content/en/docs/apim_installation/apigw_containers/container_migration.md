---
 title :  Migrate to a container deployment 
 linkTitle :  Migrate to a container deployment 
 weight: 100
 date :  2019-09-18 
 description : Migrate an API Gateway or API Manager classic deployment to a Helm-based container deployment. 
---

## Prerequisites

* Your 7.7 classic deployment must be configured and working as expected. The 7.7 deployment can be a system you upgraded from an earlier version or it can be a new installation.
* Apache Cassandra and any relational databases your system uses must be accessible to your Kubernetes cluster.
* You must have an appropriate license to run API Gateway or API Manager in a container.

## Sample topology

The following is a sample topology used to demonstrate the steps you must perform to migrate from a classic deployment to a container deployment. Your own topology might be significantly different and you must adapt the steps appropriately.

In this example, the API Gateway 7.7 classic deployment being migrated has the following topology:

* One Admin Node Manager.
* API Gateway `Group1` contains one or more API Gateways with the same configuration.
    * API Manager is enabled.
    * Apache Cassandra is configured.
    * Metrics processing is enabled and a relational database is configured.
    * Gateways are registered with the Admin Node Manager.

## Migration steps

Follow these steps to migrate your deployment.

### Step 1 – Domain certificates

The Axway images are shipped with a default domain certificate. Customized domain certificates, such as if you want to reuse your existing system domain certificate and key, can be loaded to the environment by way of the persistent volume mount point for `apigateway` configuration files. For more information, see [Add customized domain certificate configuration](/docs/apim_installation/apigw_containers/deployment_flows/axway_image_deployment/helm_deployment#add-a-customized-domain-certificate-configuration).

{{< alert title="Note" color="primary" >}}You must not use the same domain certificates for classic and container deployments that are running in parallel. Reusing the same certificates, in this case ,can cause problems as the API Gateway containers might try to register with both the elastic container deployment and classic deployment Node Managers.{{< /alert >}}

### Step 2 – Export Admin Node Manager configuration

To export the Admin Node Manager configuration, follow these steps:

1. In Policy Studio, create a project from an existing configuration. Select the Node Manager configuration from your classic deployment (for example, `/INSTALL_DIR/apigateway/conf/fed`).
2. Export the configuration to a FED file. Select **File > Export > Deployment Package** and enter a file name (for example, `ANM.fed`). Use the same passphrase as you used in the classic deployment.

### Step 3 – Export API Gateway configuration

To export the API Gateway configuration, follow these steps:

1. In Policy Studio, create a project from a running API Gateway. Select a gateway from Group1 from your classic deployment. API Manager is already enabled. Leave the password blank for the API administrator user as this is already stored in Cassandra.
2. Set the following environment variables within your configuration to correctly synchronize with the Axway Helm chart and allow these values to be configured through the chart.

    {{< alert title="Note" color="primary" >}}Be careful entering these variables. Should they be incorrect, particularly any password values, it can be very difficult to uncover the issue, as any errors will show up as a standard connection issue.{{< /alert >}}

    * Server Settings >
        * Cassandra >
            * Authentication >
                1. Username: `${environment.CASS_USERNAME}`
                2. Password: `${environment.CASS_PASSWORD}`
            * Hosts >
                1. Name: cassandra
                2. Host: `${environment.CASS_HOST}`
                3. Port: `${environment.CASS_PORT}`
            * Keyspace >
                1. Keyspace name: `${environment.CASS_KEYSPACE}`
            * Throttling >
                1. Keyspace name: `${environment.CASS_TKEYSPACE}`
    * Environment Configuration >
        * Database Connections >
            * Default Database Connection >
                1. URL: `${environment.METRICS_DB_URL}`
                2. User Name: `${environment.METRICS_DB_USERNAME}`
                3. Password:
                    * Enter Password: `${environment.METRICS_DB_PASS}`

3. Export your configuration. Select **File > Export > Deployment Package** and enter a file name (for example, `Group1.fed` or `Group1.tar.gz`). Use the same passphrase as you used for Group1 in the classic deployment.

### Step 4 – Export API Manager and KPS data

You can set up your container deployment to either use the existing Apache Cassandra keyspace from your classic deployment or you can create a new keyspace.

Having configured the environment variables in the previous step, to point your API Gateway container to an existing Cassandra keyspace used by your classic deployment, set the following values to what is required for the existing cassandra hosts and keyspace in your helm `values` file:

* global.cassandra.hosts
* global.cassandra.username
* global.cassandra.password
* global.cassandra.keyspace
* global.cassandra.tkeyspace

Alternatively, to create a new keyspace for the API Gateway container, follow these steps:

1. Use `kpsadmin backup` to export the KPS data.
2. Copy the backup directory to the API Gateway container and run `kpsadmin restore` from the Admin Node Manager container.

For more information, see the `kpsadmin` section in the [API Gateway Key Property Store User Guide](/docs/apim_policydev/apigw_kps/).

{{< alert title="Note" color="primary" >}}You can also export your API Manager APIs as an API collection if you require a backup.{{< /alert >}}

### Step 5 – Deploy helm chart

To deploy your custom configurations, the Helm chart must first be deployed using the default configurations. After the containers starts, your custom configurations can be copied into the containers before restarting them. For more information, see [Install API Gateway using your customized YAML file](/docs/apim_installation/apigw_containers/deployment_flows/axway_image_deployment/helm_deployment#install-api-gateway-using-your-customized-yaml-file).