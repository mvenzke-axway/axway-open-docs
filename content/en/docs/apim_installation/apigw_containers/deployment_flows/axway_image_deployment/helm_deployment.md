---
title: Helm deployment
linkTitle: Helm deployment
weight: 22
date: 2023-01-23
description: Install API Gateway using Axway's public helm chart and public images.
---

From API Gateway [November 2022](/docs/apim_relnotes/20221130_apimgr_relnotes/) update onwards, Axway public images and a public helm chart are available from [Axway repository](https://repository.axway.com/). This allows you to deploy the Admin Node Manager, API Gateway, API Manager, and if required, Axway Analytics, using Helm.

## Prerequisites

To run the Helm chart, you must have access to a Kubernetes cluster, for example in AWS, Azure, or Openshift. For details on possible cloud based architectures, see [API management reference architectures](/docs/apim-reference-architectures/).

You must also have a persistent storage with access mode of `RWO` and `RWX`, and which is exposed as a Kubernetes storage class, for example, OpenShift Container Storage, Azure Disks/Files, AWS EBS/EFS, or nfs-client.

{{< alert title="Note" color="primary" >}}In the sample commands on this page, when not specifically mentioned, we assume the cluster is a kubernetes cluster. If using an Openshift cluster, the openshift CLI command `oc` can be used instead of `kubectl`. The `helm` command is the same on all clusters. {{< /alert >}}

## Deploy a Cassandra cluster

Before running the `helm install` command of the Axway Helm chart, you must deploy a Cassandra cluster.

Deploying a Cassandra cluster in containers is only recommended for development environments. In a production environment, you must configure Cassandra for high availability (HA) as detailed in [Configure a Cassandra HA cluster](/docs/cass_admin/cassandra_config/).

### Deploy Cassandra in a development environment

In a development environment, Cassandra can be run in containers, and the cluster can be easily deployed using a public Helm chart. Follow this section to deploy Cassandra in a development environment.

1. In OpenShift, create a new project for the cassandra deployment:

   ```bash
   kubectl create namespace cassandra
   ```

2. Add the `bitnami` Helm repository to the project:

   ```bash
   helm repo add bitnami https://charts.bitnami.com/bitnami
   ```

3. Using the [`bitnami/cassandra`](https://github.com/bitnami/charts/blob/main/bitnami/cassandra) chart, install Cassandra by running a customized version of this sample command:

   ```bash
   helm install cassandra bitnami/cassandra \
        --set podSecurityContext.enabled="false" \
        --set containerSecurityContext.enabled="false" \
        --set dbUser.user=cassandra \
        --set dbUser.password=REDACTED \
        --set resources.requests.memory=8Gi \
        --set replicaCount=3 \
        --set image.tag="3.11.13" \
        --set jvm.maxHeapSize=1024M \
        --set jvm.newHeapSize=1024M \
        -n cassandra
   ```

## Deploy a MySQL database

If you are using metrics for analytics, follow this section to deploy a MySQL database for your metrics.

1. Create a project for the MySQL instance:

   ```bash
   kubectl create namespace metrics
   ```

2. Prepare a `configmap` with the SQL file for the schema init:

   ```bash
   echo 'CREATE DATABASE metrics; USE metrics;' | cat - mysql-analytics.sql > mysql-analytics-cm.sql
   kubectl create configmap mysql-metrics --from-file=mysql-analytics-cm.sql -n metrics
   ```

   The `mysql-analytics.sql` file is found in the API Gateway Docker scripts, in the `/quickstart` directory. For more information on getting the API Gateway Docker scripts see [Set up API Gateway Docker scripts](/docs/apim_installation/apigw_containers/deployment_flows/custom_image_deployment/docker_scripts_prereqs#setup-api-gateway-docker-scripts).

3. Install the Helm chart using [bitnami/mysql](https://github.com/bitnami/charts/tree/main/bitnami/mysql):

   ```bash
   helm install mysql bitnami/mysql \
        --set primary.podSecurityContext.enabled="false" \
        --set primary.containerSecurityContext.enabled="false" \
        --set secondary.podSecurityContext.enabled="false" \
        --set secondary.containerSecurityContext.enabled="false" \
        --set auth.rootPassword=password \
        --set initdbScriptsConfigMap=mysql-metrics \
        --set image.tag=8.0 \
        --set primary.persistence.storageClass="nfs-client" \
        -n metrics
   ```

## Deploy API Gateway

You must have a Service Account with Axway in order to access the Helm chart and public images.

### Access the Helm chart

To access the API Gateway Helm chart, go to [Axway repository](https://repository.axway.com/), and search for "API Gateway and Manager", then check the `helm chart` box. The chart can be used directly from the repository, but you must fetch the chart first to see the `values.yaml` file.

### Add the Axway Helm repository

Add the helm repository and perform a `helm pull` to get the latest version of the repository:

```bash
helm repo add axway https://helm.repository.axway.com --username=<client_id> --password=<client_secret>
helm pull axway/apigateway-helm-prod-apigateway
```

### Fetch the helm chart to examine the values file

To be able to view the helm `values.yaml` file you can run a `helm fetch` command on the added repository:

```bash
helm fetch axway/apigateway-helm-prod-apigateway --untar
```

This command creates a directory `apigateway` containing the complete chart, including the `values.yaml` file.

### Create a Kubernetes secret for your Axway credentials

To access the Axway images that are referenced in the Helm chart from your kubernetes cluster, create a kubernetes secret with your Axway Service Account credentials and reference it in your customized values.yaml file.

```bash
kubectl create secret docker-registry regcred -n <namespace> --docker-server="https://repository.axway.com" --docker-username="<client_id>" --docker-password="<client_secret>"
```

### Create a customized values.yaml file

Create a customized `values` file, for example, `myvalues.yaml`, and make your customizations. This file should contain only the sections of the `values.yaml` file that you wish to override. Any values not present in the customized file will be picked up from the original `values.yaml` file.

### Include a license configuration

Product license configuration can be added to each component individually by way of a Helm ConfigMap. The `license.lic` license file must be configured within the `License` ConfigMap. For more information, see section [Sample customized values file](#sample-customized-values-file).

### Include a group ID configuration

The group ID can be set on the API Manager or API Traffic component. For example:

```bash
apimgr:
  groupId: 
    "APIMgrGroup"

apitraffic:
  groupId: 
    "APITrafficGroup"
```

### Add a customized domain certificate configuration

Axway images are shipped with a default domain certificate. Customized domain certificates can be loaded to the environment by way of the persistent volume mount point for `apigateway` configuration files. For more information, see [Mount component configuration](/docs/apim_installation/apigw_containers/deployment_flows/axway_image_deployment/helm_deployment#mount-a-component-configuration) section.

If the domain certificates were generated with a passphrase, that passphrase must be configured within the global `domainkeypassphrase`. For example:

```bash
global:
  domainkeypassphrase:
    passphrase: "redacted"
```

### Configure Entity Store passphrase

Any component which uses an Entity Store with a passphrase can configure the passphrase in the `values` override file for that specific component. For example:

```bash
anm:
  espassphrase: "redacted"
```

### Sample customized values file

The following is an example of a customized `myvalues.yaml` file including an example of how to insert the contents of a valid license file.

```bash
nameOverride: gateway
global:
  domainName: example.com
  defaultRegistry: docker.repository.axway.com/apigateway-docker-prod/7.7
  imagePullPolicy: Always
  imagePullSecrets:
  - name: regcred
  storage:
    provisioningType: "dynamic"
    storageClassName: "nfs-client"
    volumes:
      - name: events
        enabled: true
        usedBy:
          - anm
          - apimgr
          - traffic
        accessModes:
          - ReadWriteMany
        capacity: 1Mi
      - name: anm-external-config
        enabled: true
        usedBy:
          - anm
        accessModes:
          - ReadWriteMany
        capacity: 2Mi
  initContainers:
    securityContext:
      runAsNonRoot: false
  database:
    host: mysql.metrics.svc.cluster.local
    metrics:
      enabled: true
      username: "root"
      password: "password"
  cassandra:
    enabled: true
    hosts:
      - variable: CASS_HOST
        hostname: cassandra.cassandra.svc.cluster.local
    username: cassandra
    password: cassandra
    keyspace: ks
    tkeyspace: tks

anm:
  image:
    repository: "admin-nodemanager"
    tag: "7.7.0.20221130-3-BN0009-ubi7"
  resources:
    limits:
      memory: "2048Mi"
      cpu: "1000m"
    requests:
      memory: "1Gi"
      cpu: "250m"
  securityContext:
    runAsNonRoot: false
  route:
    enabled: false
  ingress:
    enabled: true
    className: nginx
    annotations:
      nginx.ingress.kubernetes.io/ssl-passthrough: "true"
      nginx.ingress.kubernetes.io/ssl-redirect: "true"
    hosts:
      - host: anm.myexample.com
        paths:
          - path: /
            pathType: ImplementationSpecific
    tls:
      - hosts:
          - anm.myexample.com
  service:
    port: 8091
  extraVolumeMounts:
    - name: events
      mountPath: /opt/Axway/apigateway/events
  extraVolumes:
    - persistentVolumeClaim:
        claimName: events
      name: events
  license:
    license.lic: |
      FIPS=1
      Mock Connector=1
      SalesForce Connector=1
      ServiceNow Connector=1
      analytics=1
      api_visual_mapper=1
      apiportal=1
      expires=Sun, 07 Dec 2022 11:36:34 GMT
      mcafee=1
      mobile integration=1
      unrestricted=1
      # SIGNATURE: a512a9b3cc28996b7d60232f58ed08bab18945b2b7db4e9d0c442e61cead5519
      # SIGNATURE: 391c9d48b254fe6ec0f93ec23237dc3a35c491ab53a369d375a9b94413b8ea9a
      # SIGNATURE: f9b1776bc91e20868308da83386303d930fe035650e9d3c6106447f9b8fb9cdc
      # SIGNATURE: 90673c5076a69150c2ea8d7a5d3c406d2b7c3a52ffdee876b66793172c7292c3
      # SIGNATURE: 494aa7924ac869d33592f9d075b1cf8c91b70d0c18a8cfbf8c1f0e3356a40b54
      # SIGNATURE: b8bc011ec122bc8c314460cffb2eefc987bc0431f0a9b400574432dbda4ee5f4
      # SIGNATURE: e2ba8b1fb2ce138b3176ebc015b2bb2172bd059ff646d59309e10d93af372174
      # SIGNATURE: 0af7fee3a95535d8ff5f26481499a0add7a481d0a1c44c0075282ea18b33b479


apimgr:
  groupId:
    "MyEmtGroup"
  image:
    repository: "gateway"
    tag: "7.7.0.20221130-3-BN0009-ubi7"
  resources:
    limits:
      memory: "2Gi"
      cpu: 2
    requests:
      memory: "0.5Gi"
      cpu: 0.5
  securityContext:
    runAsNonRoot: false
  route:
    enabled: false
  ingress:
    enabled: true
    className: nginx
    annotations:
      nginx.ingress.kubernetes.io/ssl-passthrough: "true"
      nginx.ingress.kubernetes.io/ssl-redirect: "true"
    hosts:
      - host: apimgr.myexample.com
        paths:
          - path: /
            pathType: ImplementationSpecific
    tls:
      - hosts:
          - apimgr.myexample.com
  service:
    port: 8075
  extraVolumeMounts:
    - name: events
      mountPath: /opt/Axway/apigateway/events
  extraVolumes:
    - persistentVolumeClaim:
        claimName: events
      name: events
  license:
    license.lic: |
      FIPS=1
      Mock Connector=1
      SalesForce Connector=1
      ServiceNow Connector=1
      analytics=1
      api_visual_mapper=1
      apiportal=1
      expires=Sun, 07 Dec 2022 11:36:34 GMT
      mcafee=1
      mobile integration=1
      unrestricted=1
      # SIGNATURE: a512a9b3cc28996b7d60232f58ed08bab18945b2b7db4e9d0c442e61cead5519
      # SIGNATURE: 391c9d48b254fe6ec0f93ec23237dc3a35c491ab53a369d375a9b94413b8ea9a
      # SIGNATURE: f9b1776bc91e20868308da83386303d930fe035650e9d3c6106447f9b8fb9cdc
      # SIGNATURE: 90673c5076a69150c2ea8d7a5d3c406d2b7c3a52ffdee876b66793172c7292c3
      # SIGNATURE: 494aa7924ac869d33592f9d075b1cf8c91b70d0c18a8cfbf8c1f0e3356a40b54
      # SIGNATURE: b8bc011ec122bc8c314460cffb2eefc987bc0431f0a9b400574432dbda4ee5f4
      # SIGNATURE: e2ba8b1fb2ce138b3176ebc015b2bb2172bd059ff646d59309e10d93af372174
      # SIGNATURE: 0af7fee3a95535d8ff5f26481499a0add7a481d0a1c44c0075282ea18b33b479
  extraEnvVars:
    - name: EMT_HEALTHCHECK_PORT
      value: "8065"
    - name: EMT_HEALTHCHECK_PATH
      value: /healthcheck
    - name: GW_DIR
      value: /opt/Axway/apigateway
    - name: GW_TRACE_DIR
      value: /opt/Axway/apigateway/groups/topologylinks/emt-group-emt-service/trace
    - name: EMT_TOPOLOGY_TTL
      value: "10"
    - name: EMT_HEAP_SIZE_MB
      value: "1512"
    - name: EMT_TRACE_LEVEL
      value: INFO
    - name: APIGW_LOG_TRACE_TO_FILE
      value: "true"
    - name: APIGW_LOG_TRACE_JSON_TO_STDOUT
      value: "true"
    - name: APIGW_LOG_OPENTRAFFIC_OUTPUT
      value: "file"
    - name: EMT_DEPLOYMENT_ENABLED
      value: "false"
    - name: ACCEPT_GENERAL_CONDITIONS
      value: "yes"

apitraffic:
  image:
    repository: "gateway"
    tag: "7.7.0.20221130-3-BN0009-ubi7"
  resources:
    limits:
      memory: "2Gi"
      cpu: 2
    requests:
      memory: "0.5Gi"
      cpu: 0.5
  securityContext:
    runAsNonRoot: false
  oauth:
    route:
      enabled: false
  route:
    enabled: false
  ingress:
    enabled: true
    className: nginx
    annotations:
      nginx.ingress.kubernetes.io/ssl-passthrough: "true"
      nginx.ingress.kubernetes.io/ssl-redirect: "true"
    hosts:
      - host: apitraffic.myexample.com
        paths:
          - path: /
            pathType: ImplementationSpecific
    tls:
      - hosts:
          - apitraffic.myexample.com
  service:
    port: 8065
  extraVolumeMounts:
    - name: events
      mountPath: /opt/Axway/apigateway/events
  extraVolumes:
    - persistentVolumeClaim:
        claimName: events
      name: events
  license:
    license.lic: |
      FIPS=1
      Mock Connector=1
      SalesForce Connector=1
      ServiceNow Connector=1
      analytics=1
      api_visual_mapper=1
      apiportal=1
      expires=Sun, 07 Dec 2022 11:36:34 GMT
      mcafee=1
      mobile integration=1
      unrestricted=1
      # SIGNATURE: a512a9b3cc28996b7d60232f58ed08bab18945b2b7db4e9d0c442e61cead5519
      # SIGNATURE: 391c9d48b254fe6ec0f93ec23237dc3a35c491ab53a369d375a9b94413b8ea9a
      # SIGNATURE: f9b1776bc91e20868308da83386303d930fe035650e9d3c6106447f9b8fb9cdc
      # SIGNATURE: 90673c5076a69150c2ea8d7a5d3c406d2b7c3a52ffdee876b66793172c7292c3
      # SIGNATURE: 494aa7924ac869d33592f9d075b1cf8c91b70d0c18a8cfbf8c1f0e3356a40b54
      # SIGNATURE: b8bc011ec122bc8c314460cffb2eefc987bc0431f0a9b400574432dbda4ee5f4
      # SIGNATURE: e2ba8b1fb2ce138b3176ebc015b2bb2172bd059ff646d59309e10d93af372174
      # SIGNATURE: 0af7fee3a95535d8ff5f26481499a0add7a481d0a1c44c0075282ea18b33b479
  extraEnvVars:
    - name: EMT_HEALTHCHECK_PORT
      value: "8065"
    - name: EMT_HEALTHCHECK_PATH
      value: /healthcheck
    - name: GW_DIR
      value: /opt/Axway/apigateway
    - name: GW_TRACE_DIR
      value: /opt/Axway/apigateway/groups/topologylinks/emt-group-emt-service/trace
    - name: EMT_TOPOLOGY_TTL
      value: "10"
    - name: EMT_HEAP_SIZE_MB
      value: "1512"
    - name: EMT_TRACE_LEVEL
      value: INFO
    - name: APIGW_LOG_TRACE_TO_FILE
      value: "true"
    - name: APIGW_LOG_TRACE_JSON_TO_STDOUT
      value: "true"
    - name: APIGW_LOG_OPENTRAFFIC_OUTPUT
      value: "file"
    - name: EMT_DEPLOYMENT_ENABLED
      value: "false"
    - name: ACCEPT_GENERAL_CONDITIONS
      value: "yes"

aga:
  enabled: false
```

### Install API Gateway using your customized YAML file

Follow these steps to install API Gateway using your customized `myvalues.yaml` file.

1. Create a namespace for your API Gateway Helm chart, for example:

    ```bash
    kubectl create namespace mynamespace
    ```
2. Use your customized file to install the chart. The following command will pick up all values from the original `values.yaml` and override them with any specific values from `myvalues.yaml`.

    ```bash
    helm install apim -f myvalues.yaml -n mynamespace <repository.axway.com>
    ```

    If just using a modified `values.yaml` file, then run:

    ```bash
    helm install -f values.yaml apim -n mynamespace axway/apigateway-helm-prod-apigateway
    ```

3. After the installation is finished, check the deployment, for example:

    ```bash
    kubectl get pods -n mynamespace
    ```

    Result:

    ```none
    NAME                                       READY     STATUS    RESTARTS   AGE
    apim-gateway-aga-866946bb58-vsqlx          1/1       Running   0          72s
    apim-gateway-anm-58b5644777-jbzfl          1/1       Running   0          72s
    apim-gateway-apimgr-654fcd4744-xqxcw       0/1       Running   0          72s
    apim-gateway-apitraffic-6fc46d6788-s4mk9   0/1       Running   0          72s
    ```

4. After all the pods are up and running, you should be able to reach your Admin Node Manager, your API Gateway, the API Manager, and the Analytics UI, if enabled. To check these, get the ingresses and use them to find the URL for each of the interfaces:

    ```bash
    kubectl get ing -n mynamespace
    ```

    Result:

    ```none
    NAME                      CLASS     HOSTS                      ADDRESS   PORTS     AGE
    apim-gateway-aga          nginx     analytics.myexample.com              80, 443   13s
    apim-gateway-anm          nginx     anm.myexample.com                    80, 443   13s
    apim-gateway-apimgr       nginx     apimgr.myexample.com                 80, 443   13s
    apim-gateway-apitraffic   nginx     apitraffic.myexample.com             80, 443   13s
    ```

    Then, browse to one of the services, for example, <https://anm.my.example.com>, to reach the Admin Node Manager.

### Mount a component configuration

Components can be configured to mount custom configurations, which can then be made available to the pod at runtime. Each component has a configured persistent volume to facilitate this. The following table details the expected mount locations for each configuration type. See [API Gateway configuration files](/docs/apim_reference/config_files_reference/) for a full list of configurable files.

| Configuration Type | Pod mount point | Description |
| ------------------ | --------------- | ----------- |
| Entity Store (.fed) | /merge/fed | For a policy configuration stored as a deployment package (FED file).|
| Entity Store (.yaml) |/merge/yaml | For YAML based API Gateway policy configuration stored either as a deployment package (.tar.gz file) or as a directory. This option is applicable for API Gateway only.|
| Mandatory Files Verification | /merge/mandatoryFiles | For the verification of mandatory configuration files. |
| Apigateway Configuration | /merge/ | For all other API Gateway or Admin Node Manager configurations, for example, jvm.xml, envSettings.props, and so on.|
| Analytics Configuration | /merge/ | For all Analytics configurations, for example, jvm.xml, envSettings.props, and so on.|

#### The structure of configuration directories

`apigateway` and `analytics` configuration files must be in the same directory structure which mirrors the `/opt/Axway/apigateway` or `/opt/Axway/analytics` installation directories. For example:

```bash
apigateway
├── groups
│   ├── certs
│   │   ├── domaincert.pem
│   │   └── private
│   │       └── domainkey.pem
│   └── emt-group
│       └── emt-service
│           └── conf
│               ├── envSettings.props
│               └── jvm.xml
└── system
    └── conf
        └── log4j2.yaml
```

Configuration files can be copied to the persistent volumes using tools, such as `kubectl` and `scp`. For example:

```bash
# Copying a FED file to the API Manager component
kubectl cp apimgr.fed <apimgr pod id>:/merge/fed

# Copying an apigateway configuration directory to the API Manager component
kubectl cp apigateway <apimgr pod id>:/merge/
```

After the external configuration has been successfully copied to the relevant persistent volume, the deployment can be reloaded in order for the new configuration to be adopted to the relevant component. For example:

```bash
# Reload the API Manager deployment
kubectl rollout restart deployment apigw-gateway-apimgr -n <namespace>
```
