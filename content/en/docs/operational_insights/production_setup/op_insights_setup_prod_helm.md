---
title: Configure a production setup for Helm
linkTitle: Production setup for Helm
weight: 10
date: 2022-07-20
description: Configure a production setup using Helm charts to run Operational Insights in a single instance.
---

This section covers the configuration specific for deploying Axway API Management for the Elastic solution on a Kubernetes or OpenShift cluster using Helm. The provided Helm chart is extremely flexible and configurable. You can decide which components to deploy, use your own labels, annotations, secrets, and volumes to customize the deployment to your needs.

{{< alert title="Note" color="primary">}}
To access sample files referred throughout this section for user customizations, follow the instructions on the [Docker Compose](/docs/operational_insights/op_insights_basic_setup/#basic-setup-with-docker-compose) section to download the release package. {{< /alert >}}

## Before you start

Ensure that you have all [prerequisites](/docs/operational_insights/op_insights_prerequisites/) in place.

## Create your configuration file

The first step to setup Operational Insights in your production environment using Helm, is to create your own `myvalues.yaml` based on the standard `values.yaml` chart and to configure the required parameters. All parameters are explained in detail in the `values.yaml` chart.

The following example represents the most simple `myvalues.yaml` configuration, assuming the API Management Platform and Filebeat are running externally to the Kubernetes cluster as indicated in the Helm architecture [diagram](/docs/operational_insights/op_insights_basic_setup/#architecture-example).

```bash
apibuilder4elastic:
  image: "docker.repository.axway.com/apigateway-docker-prod/<VERSION_TAG>/apibuilder4elastic:<VERSION_TAG>"
  anmUrl: "https://my-admin-node-manager:8090"
  secrets: 
    apimgrUsername: "apiadmin"
    apimgrPassword: "changeme"
# Enable if you would like to deploy a new Elasticsearch cluster for Operational Insights
elasticsearch:
  enabled: true
  volumeClaimTemplate:
    accessModes: [ "ReadWriteOnce" ]
    resources:
      requests:
        storage: 1Gi
# Enable if you would like to deploy Kibana for Operational Insights
kibana:
  enabled: true
```

{{< alert title="Note" color="primary">}}The `image` parameter specified in your `myvalues.yaml` file must be configured to point to a specific Docker release available from the [Axway repository](repository.axway.com). {{< /alert >}}

## Define Elasticsearch persistent volumes

Elasticsearch requires persistent volumes to function correctly. This can be configured by way of your own `myvalues.yaml`, but it will be dependent on the environment and platform you are deploying on.

You can use the [storage class](https://kubernetes.io/docs/concepts/storage/storage-classes/) provided by your chosen platform, or create your own, to deploy the volumes.

After a storage class is available, you must define it in the appropriate section of your `myvalues.ymal`. For example:

```bash
elasticsearch:
  enabled: true
  volumeClaimTemplate:
    accessModes: [ "ReadWriteOnce" ]
    resources:
      requests:
        storage: 1Gi
    storageClassName: example-nfs-share
```

## Download the Helm chart

Download the Helm package from the [Axway repository](repository.axway.com), and extract its contents as follows:

```bash
tar -xvzf APIGateway_AAOI_HelmPackage_<chart-version>_Utility_linux-x86-64_<build-number>.tgz
cd apim4elastic
```

You can also refer to the [Axway repository](repository.axway.com) for the most recent version of the Helm chart and the corresponding configuration files.

## Deploy the ELK Helm chart

After your Elasticsearch volumes are created and your `myvalues.yaml` file is configured, you can start the installation of the chart.

The Helm release name, `axway-elk`, is mandatory. For more information, see [FAQ - Why is the Helm release name axway-elk](/docs/operational_insights/op_insights_faq/#why-is-the-helm-release-name-axway-elk).

The following instructions to install an ELK Helm chart are applicable to the AAOI charts versions `5.1.0` to `5.6.0` inclusive. These charts use version `7` of the ELK images and charts.

Note the following about the chart versions:

* For instructions on installing AAOI charts higher than version `5.6.0`, see [Install ELK version 8](#install-elk-version-8).
* Downgrading to version `5.6.0` after moving to version `5.7.0`, or higher, is not possible because of the backward incompatibility between the v7 and v8 of the ELK Helm charts.

Run the following to install the ELK Helm chart:

```bash
helm install -n apim-elk -f myvalues.yaml axway-elk .
```

To check the status of the deployment, pods, services, and son on, run the following commands:

```bash
# Check the installed release
helm list -n apim-elk
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION   
axway-elk       apim-elk        1               2021-05-03 14:22:08.9325287 +0200 CEST  deployed        apim4elastic-4.2.0              4.2.0

# Check the pods with Elasticsearch and Kibana enabled
kubectl get pods -n apim-elk
NAME                                                         READY   STATUS    RESTARTS   AGE 
axway-elk-apim4elastic-apibuilder4elastic-65b5d56d77-5hv9z   1/1     Running   1          7h2m
axway-elk-apim4elastic-elasticsearch-0                       1/1     Running   0          7h2m
axway-elk-apim4elastic-elasticsearch-1                       1/1     Running   0          7h2m
axway-elk-apim4elastic-kibana-7c6d4b675f-dnxj7               1/1     Running   0          7h2m
axway-elk-apim4elastic-logstash-0                            1/1     Running   0          7h2m
axway-elk-apim4elastic-memcached-56b7447d9-25xwb             1/1     Running   0          7h2m

# Check deployed services
kubectl -n apim-elk get service
NAME                                                TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)             AGE
axway-elk-apim4elastic-apibuilder4elastic           ClusterIP   None            <none>        8443/TCP            7h7m
axway-elk-apim4elastic-elasticsearch                ClusterIP   10.100.85.132   <none>        9200/TCP,9300/TCP   7h7m
axway-elk-apim4elastic-elasticsearch-headless       ClusterIP   None            <none>        9200/TCP,9300/TCP   7h4m
axway-elk-apim4elastic-kibana                       ClusterIP   10.105.84.214   <none>        5601/TCP            7h7m
axway-elk-apim4elastic-logstash                     NodePort    10.103.53.111   <none>        5044:32001/TCP      7h7m
axway-elk-apim4elastic-logstash-headless            ClusterIP   None            <none>        9600/TCP            7h7m
axway-elk-apim4elastic-memcached                    ClusterIP   10.108.48.131   <none>        11211/TCP           7h7m

# Describe a certain POD
kubectl -n apim-elk describe pod axway-elk-apim4elastic-elasticsearch-0

# Get the logs for a POD
kubectl -n apim-elk logs axway-elk-apim4elastic-apibuilder4elastic-65b5d56d77-5hv9z
```

If all configuration has worked and your Ingress configuration is running, you can access the different components on the following host names:

* `https://kibana.apim4elastic.local`
* `https://apibuilder.apim4elastic.local`
* `https://elasticsearch.apim4elastic.local`

This example assumes that Ingress is configured and the DNS resolution for `apim4elastic.local` points to your cluster IP.

At this point, it is still assumed that the API Management platform is running externally. Therefore, the next step is to connect one or more Filebeats to Logstash running in Kubernetes.

## Configure Logstash and Filebeat

The communication between Filebeat and Logstash is a persistent TCP connection. This means that once the connection has been established, it will continue to be used for the best possible throughput. If you specify multiple Logstash instances in your Filebeat configuration, then Filebeat will establish multiple persistent connections and uses all of the them for load balancing and failover.

In the case of Kubernetes or OpenShift, multiple Logstash instances are running behind a Kubernetes service, which acts like a load balancer. However, because of the persistent connection, the load balancer and the service cannot really distribute the load. Therefore, for high volumes, the best option is to let Filebeat perform the load balancing.

## Configure NodePort service

By default, the Helm chart deploys a NodePort service for Logstash, then it becomes available on the configured port `32001` on all nodes of the cluster. You can setup the corresponding nodes as Logstash hosts in your Filebeat configuration with Load balancing enabled, and Filebeat will distribute the traffic across the available Logstashes by establishing multiple persistent connections.

The following diagram illustrates the approach:

![3 Worker Nodes Helm](/Images/op_insights/op_insights_filebeat_logstash_nodeport_3_worker_nodes.png)

The following is an example setup of the above configuration:

```bash
# The service exposing Logstash as a NodePort on 32001
kubectl -n apim-elk get services axway-elk-apim4elastic-logstash -o wide
NAME                              TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE   SELECTOR
axway-elk-apim4elastic-logstash   NodePort   10.110.89.215   <none>        5044:32001/TCP   85m   app=axway-elk-apim4elastic-logstash,chart=logstash,release=axway-elk

# The given NodePort (default 32001) is exposed on all Worker-Nodes:
kubectl get nodes -o wide
NAME                            STATUS   ROLES                  AGE   VERSION   INTERNAL-IP     EXTERNAL-IP   OS-IMAGE         KERNEL-VERSION                  CONTAINER-RUNTIME
ip-172-31-51-209.ec2.internal   Ready    <none>                 23h   v1.21.0   172.31.51.209   <none>        Amazon Linux 2   4.14.209-160.339.amzn2.x86_64   docker://19.3.13
ip-172-31-53-214.ec2.internal   Ready    <none>                 23h   v1.21.0   172.31.53.214   <none>        Amazon Linux 2   4.14.193-149.317.amzn2.x86_64   docker://19.3.6
ip-172-31-54-120.ec2.internal   Ready    <none>                 23h   v1.21.0   172.31.54.120   <none>        Amazon Linux 2   4.14.209-160.339.amzn2.x86_64   docker://19.3.13
ip-172-31-61-143.ec2.internal   Ready    control-plane,master   23h   v1.21.0   172.31.61.143   <none>        Amazon Linux 2   4.14.181-142.260.amzn2.x86_64   docker://19.3.6
```

The following is an example of the configuration for the Filebeat Logstash output:

```bash
output.logstash:
  # Based on our tests, the more WorkerNodes you add, the more likely the traffic 
  # is evenly distributed
  hosts: ["172.31.51.209:32001", "172.31.53.214:32001", "172.31.54.120:32001"]
  # Or as part of the .env:
  # LOGSTASH_HOSTS=172.31.51.209:32001,172.31.53.214:32001,172.31.54.120:32001
  worker: 2
  bulk_max_size: 3072
  loadbalance: true
  # Required for the NodePort service approach to give Filebeat a chance to recognize 
  # and use additional Logstash instances that have been provisioned. 
  # 5m was determined to be the best value for the highest possible throughput. 
  # Values lower than 5 minutes may cause an error in Filebeat described here:
  # https://www.elastic.co/guide/en/beats/filebeat/current/publishing-ls-fails-connection-reset-by-peer.html
  ttl: 2m
  # Required to make TTL working
  pipelining: 0
```

The NodePort service without any load balancer in between is the recommended approach for the best possible throughput. This has been tested with up to 1.000 TPS using four Logstash instances and a five-node-Elasticsearch cluster.

### Utilize a load balancer

You can also use a load balancer to have a single entry point. You can reconfigure the Logstash service from a NodePort to a LoadBalancer and use, for instance, your public cloud load balancer from AWS, GCP, and so on. In this scenario, you must ensure that Filebeat is set with an appropriate TTL to improve the load and make it better distributed between the available Logstash instances. However, it is still random to which Logstash instances connections are established. So, you might see situations where some Logstash instances are on idle status while others are under heavy load.

For example:

```bash
output.logstash:
  # Or as part of the .env:
  # LOGSTASH_HOSTS=172.31.51.209:32001,172.31.53.214:32001,172.31.54.120:32001
  hosts: ["logstash.on.load-balancer:5044"]
  worker: 2
  bulk_max_size: 3072
  # This parameter has no effect as there is only one Logstash host configured
  loadbalance: true
  # The following two parameters drop and re-establish the connection to Logstash every 5 minutes
  # With that, you give the Service/LoadBalancer from time to time the chance to distribute the traffic. 
  # But even with that, it might be the case that call traffic goes to one Logstash instance.
  # Do not set the TTL to less than 1 minute as it would increase the connection management overhead
  ttl: 2m
  # Required to make TTL working
  pipelining: 0
```

## Enable user authentication

To enable the user authentication for a newly created Elasticsearch cluster, you must generate passwords for the default Elasticsearch users and store them in your `myvalues.yaml` file or in your own secrets.

Run the following command to generate the passwords for the default users.

```bash
kubectl -n apim-elk exec axway-elk-apim4elastic-elasticsearch-0 -- bin/elasticsearch-setup-passwords auto --batch --url https://localhost:9200
```

The following example shows how to setup the Elasticsearch users in your `myvalues.yaml` and disable anonymous access.

```bash
apibuilder4elastic:
  secrets:
    elasticsearchUsername: "elastic"
    elasticsearchPassword: "XXXXXXXXXXXXXXXXXXXX"
logstash:
  logstashSecrets:
    # Used to send stack monitoring information
    logstashSystemUsername: "logstash_system"
    logstashSystemPassword: "XXXXXXXXXXXXXXXXXXXX"
    # Used to send events
    logstashUsername: "elastic"
    logstashPassword: "XXXXXXXXXXXXXXXXXXXX"
kibana:
  kibanaSecrets:
    username: "kibana_system"
    password: "XXXXXXXXXXXXXXXXXXXX"
filebeat:
  filebeatSecrets: 
    beatsSystemUsername: "beats_system"
    beatsSystemPassword: "XXXXXXXXXXXXXXXXXXXX"
  # Required for the internal stack monitoring to work with Filebeat
  elasticsearchClusterUUID: "YOUR-CLUSTER-UUID-ID"
# Required for the Elasticsearch readiness check after users have been generated
elasticsearch:
  elasticsearchSecrets: 
    elasticUsername: "elastic"
    elasticPassword: "XXXXXXXXXXXXXXXXXXXX"
  anonymous: 
    enabled: false
```

## Customize your Helm chart

You can use your own secrets, ConfigMaps, and son on to customize Operational Insights. We recommend that you create your own Helm chart that contains all the necessary resources, then link your custom resources in your `myvalues.yaml` to deploy Operational Insights.

You can customize your Helm chart as follows:

```bash
helm create axway-elk-setup
cd axway-elk-setup
```

## Use a secret for API Manager username and password

The following example shows how to create a secret to store API Manager username and password, and use it with API Builder. The same procedure applies for all confidential information. For more information, see the `values.yaml` file.

1. Create a secret:

    ```bash
    kubectl create secret generic api-builder-secrets --from-literal=API_MANAGER_PASSWORD=changeme --from-literal=API_MANAGER_USERNAME=apiadmin -n apim-elk
    ```

2. Reference the secret. Update your `myvalues.yaml` to reference the newly created secret. Additionally, you must declare the default `ConfigMap` if you do not wish to manage the `ConfigMap` yourself.

   ```bash
    apibuilder4elastic:
      envFrom:
        - configMapRef:
            name: axway-elk-apim4elastic-apibuilder4elastic-config
        - secretRef:
            name: api-builder-secrets
      # Optionally, disable the secrets driven by the values.yaml
      secrets:
        enabled: false
    ```

3. Install or upgrade APIM4Elastic:

    ```bash
    cd apim4elastic
    helm upgrade -n apim-elk -f myvalues.yaml axway-elk .
    ```

As a result of this section, you have set up your `myvalues.yaml` Helm chart to run Operational Insights in a single instance in a production environment.

## Manually create credential secrets

Each component uses secrets to store credentials, such as username and password. When deployed, the chart takes the credentials entered in your `values.yaml` file, and generate a secret from that value.

If you do not wish to store credentials in the `values` file, you can disable the secrets for that component, and create them manually. Then, when the Helm chart is deployed, the secrets will be picked up.

The following are examples of how to disable the automatically generation of secrets, and manually create a secret for the different components.

### Application Performance Monitoring server

Disable the secrets in you `values` file:

```bash
 apm-server:
   apmserverSecrets:
     secrets:
       enabled: false
```

Manually create a secret in APM server:

```bash
kubectl create secret generic axway-elk-apim4elastic-apmserver-secret --from-literal=APM_USERNAME=<username> --from-literal=APM_PASSWORD=<password> -n apim-elk
```

### Elasticsearch

Disable the secrets in you `values` file:

```bash
 elasticsearch:
   elasticsearchSecrets:
     secrets:
       enabled: false
```

Manually create a secret in Elasticsearch:

```bash
kubectl create secret generic axway-elk-apim4elastic-elasticsearch-secret --from-literal=ELASTIC_USERNAME=<username> --from-literal=ELASTIC_PASSWORD=<password> -n apim-elk
```

### Filebeat

Disable the secrets in you `values` file:

```bash
 filebeat:
   filebeatSecrets:
     secrets:
       enabled: false
```

Manually create a secret in Filebeat:

```bash
kubectl create secret generic axway-elk-apim4elastic-filebeat-secret --from-literal=BEATS_SYSTEM_USERNAME=<username> --from-literal=BEATS_SYSTEM_PASSWORD=<password> -n apim-elk
```

### Kibana

Disable the secrets in you `values` file:

```bash
 kibana:
   kibanaSecrets:
     secrets:
       enabled: false
```

Manually create a secret in Kibana:

```bash
kubectl create secret generic axway-elk-apim4elastic-kibana-secret --from-literal=KIBANA_SYSTEM_USERNAME=<username> --from-literal=KIBANA_SYSTEM_PASSWORD=<password> -n apim-elk
```

Note that in version 8 of the ELK Helm charts the credentials `elastic` and `password` are generated in the credentials secret as part of the deployment.

### Logstash

Disable the secrets in you `values` file:

```bash
 logstash:
   logstashSecrets:
     secrets:
       enabled: false
```

Manually create a secret in Logstash:

```bash
kubectl create secret generic axway-elk-apim4elastic-logstash-secret --from-literal=LOGSTASH_USERNAME=<username> --from-literal=LOGSTASH_PASSWORD=<password> --from-literal=xpack.monitoring.elasticsearch.username=<system_username> --from-literal=xpack.monitoring.elasticsearch.password=<system_password> -n apim-elk
```

## Install ELK version 8

With the release of the AAOI charts version `5.7.0`, the version of the ELK images and charts installed has been upgraded to version `8`. As a result, there are some differences in the method of deployment of the AAOI chart, particularly on the Helm `install` command. Most of the other instructions on this guide, for installing and configuring version `5.6.0`, and lower, of the AAOI charts are also relevant for version `5.7.0`.

The main change with ELK `v8` is that Kibana cannot be installed at the same time as Elasticsearch, so it must be disabled for the initial Helm `install` run.

For more information on the ELK Helm charts version `8.5.1`, see the [Elastic Helm charts](https://github.com/elastic/helm-charts) documentation.

### Install AAOI using version 5.7.0

Follow this section to install AAOI using Helm charts version `5.7.0`.

1. Download the release package `v5.7.0`. For more information, see [Docker Compose](/docs/operational_insights/op_insights_basic_setup/#basic-setup-with-docker-compose).
2. Create your values file as described in section [Create your configuration file](#create-your-configuration-file).
3. Install the Helm chart with kibana disabled.
4. Wait while Elasticsearch, Logstash, and Filebeat start up and stabilize.
5. When Elasticsearch has been successfully installed, run the upgrade command to install the Helm chart with kibana enabled.
6. Wait while kibana stabilizes. (This might take from 5 to 10 minutes).
7. kibana now has credentials for logging in to the UI, so you can extract the password from the credentials secret.
8. The Kibana username is always `elastic`. The password can be set in the `values` file, or else will be randomly generated.

The follow is an example of running the steps of this section.

```bash
cd apim4elastic
helm upgrade --install axway-elk -f myvalues.yaml . --set values.kibana.enabled=false
kubectl get pods
```

Wait approximately 5 minutes for the components to start up and stabilize, then continue:

```bash
curl -kv https://elasticsearchhost:92000 #change to the relevant name of elasticsearch host
helm upgrade --install axway-elk -f myvalues.yaml . --set values.kibana.enabled=true
```

Extract the password with the following instructions, but wait approximately 10 minutes before attempting to log on to the Kibana dashboard:

```bash
PASSWORD=$(kubectl get secret axway-elk-apim4elastic-elasticsearch-credentials -o=jsonpath='{.data.password}' | base64 --decode)
echo $PASSWORD
#
```

### Upgrade AAOI to version 5.7.0

When you upgrade the AAOI charts to version `5.7.0`, the upgrade automatically migrates the data in your Elasticsearch database to the new installation.

The steps to upgrade are identical to those for installing a new version, however, there is no need to uninstall the previous version of the charts.

The instructions to upgrade the ELK Helm chart to version `8` are very similar to a clean install. See the following command example:

```bash
cd apim4elastic
helm upgrade --install axway-elk -f myvalues.yaml . --set values.kibana.enabled=false
kubectl get pods
```

Wait approximately 15 minutes, or until both Elasticsearch pods have been upgraded, to using the version 8 image:

```bash
curl -kv https://elasticsearchhost:9200 #change to relevant name of elasticsearch host
helm upgrade --install axway-elk -f myvalues.yaml . --set values.kibana.enabled=true
```

Wait approximately 10 minutes before attempting to log on to the kibana dashboard.

Note the following about the previous code example:

* You need to wait longer after running the first Helm upgrade command before moving on.
* The Kibana interface now has a username and password.
* As this is an upgrade, it might take longer for the data to show on the Kibana dashboard.

#### Troubleshooting migration

The Elastic Helm charts version `8.5.1` do not allow Kibana to be installed at the same time as Elasticsearch.

If you get issues when uninstalling or upgrading the helm chart, you might have to clean up resources, upgrade with kibana disabled, and then upgrade with kibana enabled.

Sometimes, a few resources have to be cleaned up if Kibana was installed at the wrong time, and failed. The following are examples of cleanup operations:

```bash
kubectl delete rolebindings.rbac.authorization.k8s.io pre-install-axway-elk-apim4elastic-kibana
kubectl delete roles.rbac.authorization.k8s.io pre-install-axway-elk-apim4elastic-kibana
kubectl delete cm axway-elk-apim4elastic-kibana-helm-scripts
kubectl delete sa pre-install-axway-elk-apim4elastic-kibana
kubectl delete secret axway-elk-apim4elastic-kibana-es-token
kubectl delete roles.rbac.authorization.k8s.io post-delete-axway-elk-apim4elastic-kibana
kubectl delete job axway-elk
```

Sometimes, a pre-install job might have to be cleaned up. Run the following command to perform the clean up:

```bash
kubectl delete job pre-install-axway-elk-apim4elastic-kibana
```

Sometimes, a post-install job might have to be cleaned up. Run the following command to perform the clean up:

```bash
kubectl delete job post-install-axway-elk-apim4elastic-kibana
```
