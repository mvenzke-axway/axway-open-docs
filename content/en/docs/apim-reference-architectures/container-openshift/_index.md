---
title: Amplify API Management container reference architecture on Openshift
linkTitle: Container reference architecture on Openshift
weight: 30
date: 2022-12-15
description: Learn how to deploy and maintain Amplify API Management using API Gateway in containers on Openshift
---

Amplify API Management (APIM) is a leading API management solution, which supports [container-based deployment](/docs/apim_installation/apigw_containers/container_intro/). The purpose of this document is to share Axway reference architecture for the container-based deployment of an API management solution on Openshift. It will address architectural, development, and operational aspects of the proposed architecture.

Deploying APIM using Docker containers orchestrated by Openshift brings tremendous benefits in installing, developing, operating and monitoring an API management solution.

OpenShift is a cloud development Platform as a Service (PaaS) hosted by Red Hat. It is an open source cloud-based user-friendly platform used to create, test, and run applications, and finally deploy them on cloud. Using Openshift, customers have the advantage of creating a secured and scalable cluster, on a wide variety of cloud providers or their own infrastructure, and be provided with the tools to easily maintain and monitor the environment.

The technology choices, Docker and Openshift, are portable. This means that most of the information in this guide should apply across an on-premise environment and many cloud providers' Openshift solutions.

This document provides recommendations on deploying and maintaining an Openshift environment, including:

* Physical and deployment architectures.
* Explanation and consideration for selecting underlying infrastructure components.
* Openshift considerations.
* Performance, logging, and monitoring aspects.
* Back up and recovery, including disaster recovery.
* Constraints and roadmap.

## Target audience

The target audience for this document is architects, developers, and operations personnel. To get the most value from this document, a reader should have a good knowledge of Docker, Openshift, and API management.

## General architecture

This section describes the architecture in support of an API management deployment on a dedicated Openshift cluster, architectural principles, and the required and optional components. There are many ways to deploy software on an Openshift cluster, but this document shares Axway's experience acquired from deploying Amplify API Management on Openshift Container Platform.

Ensure the constraints listed here are respected in the case of deployment on an existing Openshift cluster.

### Principles

When deploying in containers, there are some differences in how Amplify API Management is operated and configured, with many aspects handed over to the orchestration platform, in this case, Openshift. Existing users of Amplify API Management must be aware that in container deployments, the role of the Admin Node Manager (ANM) becomes more of a monitoring tool, and the Node Manager is completely removed from the container architecture.

Openshift manages many important aspects of runtime, security, monitoring, and operations, for example:

* Autoscaling API Gateway with CPU or memory consumption threshold.
* Self-healing.
* Configuration through persisted volumes.
* Rolling updates with zero downtime.

In generic terms, a reference architecture can be built by stacking four layers of different capabilities, as shown in the following image:

![Reference architecture layers](/Images/apim-reference-architectures/container-openshift/ReferenceArchitectureLayers.jpg)  

To help customers with setting up an environment, the following table describes the required and recommended options.

| Description                                                                                        | Required?   |
|----------------------------------------------------------------------------------------------------|-------------|
| A DevOps pipeline is strongly recommended for managing deployment of Docker images to the cluster  | Recommended |
| A storage system with enough capacity to store dedicated data and to share data between components | Required    |
| A bastion host for administration tasks on API management and Openshift                            | Required    |

Besides [Docker](https://www.docker.com/resources/what-container) and [Openshift](https://docs.openshift.com/), we use [Helm](https://helm.sh) charts to describe the entire deployment configuration. Using Helm provides an efficient way to package all configuration parameters for Openshift and API Management containers to be deployed with a single
command.

### Target use case

Consider a single deployment option where all API Management components are running inside a single Openshift cluster. This pattern allows you to virtualize back-end apps or APIs hosted inside or outside the cluster. It is possible to deploy multiple API gateway groups in the same cluster.

In our scenario, we expose several entry points for the external clients inside the cluster. All API gateways write their transaction events to a shared volume. Admin Node Manager streams those events to a relational database management system. API Gateway Manager UI can be used to view the traffic monitor and events data.

A dedicated deployment environment requires:

* Openshift cluster.
* Cassandra cluster.
* RDBMS.
* Storage system for shared volume.
* Monitoring system.
* Bastion host for cluster access.
* DevOps pipelines to control Docker image deployment to a target environment.

The following diagram shows a general architecture of a single cluster configuration:

![Single cluster configuration](/Images/apim-reference-architectures/container-openshift/SingleClusterConfiguration.jpg)

### Additional components and considerations

Considerations for using additional components in the single cluster architecture.

#### Container registry

While our suggested deployment method uses images and charts published by Axway, you can build and maintain your own images should you need to. Building and maintaining your own Docker images requires a registry to store the images. While you can use an existing registry you may have, provided the cluster can access it, Openshift also provides an integrated container image registry that you can use. For more information, see [OpenShift Container Platform registry](https://docs.openshift.com/container-platform/4.11/registry/index.html#registry-integrated-openshift-registry_registry-overview) documentation.

Once a registry setup is in place, Docker images can then be pulled by Openshift from your registry and deployed to a cluster. When deploying your cluster, you must pass container registry credentials during the Helm package deployment. You will need to use the "secret" resource to define these credentials.

#### SMTP relay

API Manager can send alerts and messages. By default, it only supports SMTPs to send emails, but you can customize alert policies to send an email by way of an API.

## Implementation details

The following are requirements and configuration for each component required for an Openshift environment capable of running Amplify API Management.

### Application view

The following diagram represents the main objects inside Openshift and how they connect up externally to Load Balancers and the different databases. The diagram shows only one availability zone.

![Application view](/Images/apim-reference-architectures/container-openshift/ApplicationView.png)

### Choice of runtime infrastructure components

In the configuration for a typical implementation of the runtime infrastructure components, all assets of the Openshift cluster are deployed in the same data center or a region (in case of a cloud deployment), even though components are spread out in various racks, rooms, or availability zones.

The following table lists the number of runtime components in this configuration.

|Assets |Spec|
|--- |---|
|Worker nodes                                    |3 to 6|
|Cassandra nodes                                    |3|
|MySQL Database                                     |1|
|NFS Storage                                      |90GB|
|External IP (public or private)                  |1 min|
|Load Balancer                                      |1|
|Bastion                                            |1|
|Worker pipeline                                    |1|

{{< alert title="Note" color="primary" >}}
These values are the minimum recommended starting point. Your actual values will depend on many factors, like the number of APIs, payload size, and so on.
{{< /alert >}}

#### Network specification

This typical network deployment is based on a minimal number of segregated zones. It consists of three subnets in one Virtual Network:

* **Bastion Subnet** where administration tasks will be done. The recommended network mask is `/28`.
* **Openshift Subnet** for Openshift worker nodes. The recommended network mask is `/23`.
* **Data Subnet** to host Cassandra or MySQL Databases. The recommended network mask is `/27`.

Restrict access from subnets to only required services, like databases or backend services external to the cluster.

#### Openshift container platform

Openshift has an extensive documentation to aid with the installation on the various different platforms. For more information, choose the appropriate section for your chosen platform and follow the steps on [Openshift installation](https://docs.openshift.com/container-platform/4.11/installing/index.html) documentation.

#### Bastion host

Administrative tasks should be executed safely. A bastion host should be used to bridge to the following instances via the internet:

* Openshift master nodes for managing a cluster.
* Openshift worker nodes.
* RDBMS and Cassandra.
* Debugging any issue with the Openshift cluster.

The bastion must have high traceability with specific RBAC permissions to allow a few selected users to access infrastructure components.

#### DevOps pipeline

A DevOps pipeline is based on a suite of tools to build and push gateway configuration and APIs to a target deployment environment and run a set of verification tests. While direct access to the cluster will still be required for tasks like debugging, it is recommended that pipelines are used for all deployment operations as it adds consistency and traceability. If you are also building and maintaining your own images, you may consider maintaining at least two pipelines: one for building images and the other for deploying them to a target environment. The image building pipeline builds and pushes images to the registry. The deployment pipeline deploys the Helm chart, including the registry and images as parameters.

#### Cassandra cluster size

Cassandra is a distributed database, so each node requires its own storage. For production, Axway recommends a standard Cassandra cluster on three nodes. Axway also recommends a VM with large bandwidth and a capacity to use premium SSD storage. Three Cassandra VMs must be spread on three availability zones, data centres, or server racks.

#### MySQL

Admin Node Manager uses an RDBMS to store data used for system metrics and so, an RDBMS must be configured to take advantage of this. RDBMS uses less storage than Cassandra, so 20GB for a single instance is acceptable.

|Description | Type |
|---|---|
|Activate TLS configuration                      |Recommended|
|Use Endpoint termination                        |Recommended|

#### NFS for shared storage

A shared NFS storage solution is required for several aspects of the API Manager solution. While each component will be separate from the others when implemented, you must consider the total size requirement of all components when setting up the storage solution. Depending on your cloud provider or whether you are deploying on your own infrastructure, the setup and implementation of the NFS will be different and you should refer to the documentation of your platform. After the storage solution is configured, it must be made available to the cluster in the form of a storage class to allow the helm chart to make use of the storage as required.

##### Application configuration

API Gateway, Admin Node Manager, and Analytics all support external configuration provided from a persistent volume. Because each component may come up on any node in the cluster, as well as the fact that multiple replicas of the gateway need to share the same configuration, this configuration needs to be stored on a shared volume. The size required for the storage can vary greatly based on your configuration but we would recommend a minimum of 1GB of storage dedicated to configuration.

##### Events logs

API gateways generate event logs. The logs must be written to a shared volume in order to written and processed. The volume is limited by a setting inside the deployment package. By default, this value is set to 1GB. Three gateways are deployed in the minimal configuration, but autoscaling can increase this number to 12 replicas or more, for example, in your custom configuration. So, we recommend you to configure a storage of 13 GB.

The following image shows the Transaction Event Log settings.

![Server Settings - Transaction Event Log](/Images/apim-reference-architectures/container-openshift/image4.png)

Follow this method to estimate disk space:

```
Max_disk_space x API_gateway_max_replica + 1GB = recommended_disk_space
```

It is important to select a proper disk option for logs in your target environment (cloud or on-premises) because in the Openshift environment, there may be many pods simultaneously writing to shared storage. So, the selected disks must support this target workload.

#### Network Access

External access to the cluster can be configured through two primary methods in Openshift, ingress and routes. Both provide a way to safely expose just the components that require access from outside the cluster. This is often done by connecting up with a load balancer, the options and configuration of which will be different depending on your platform. For more details on which would work best for you, see the [Openshift Networking](https://docs.openshift.com/container-platform/4.11/networking/understanding-networking.html) documentation.

### Openshift considerations

This section focuses on additional Openshift objects and configuration inside the cluster to support Axway components. This is a required step before deploying containers.

#### Network CNI

OpenShift Container Platform offers two supported choices, OpenShift SDN and OVN-Kubernetes, for the default Container Network Interface (CNI) network provider. While the default choice, Openshift SDN, should suffice most use cases, you can refer to [Openshift CNI](https://docs.openshift.com/container-platform/4.11/networking/openshift_sdn/about-openshift-sdn.html) documentation to learn why you might choose OVN-Kubernetes instead.

#### RBAC Permission

RBAC permission is a secure mechanism to manage authorization inside Openshift. It is recommended to set people or application permissions to manage resources:

* Allow Helm to manage resources.
* Allow worker nodes autoscaling.
* Allow specific users to view pods, to deploy pods, to access Openshift Dashboards.

This is a minimal configuration and you can define more specific permissions with cluster roles and bindings in the cluster.

#### Namespaces

A namespace allows splitting of an Openshift cluster into separated virtual zones. You can configure multiple namespaces that will be logically isolated from each other. Pods from different namespaces can communicate with a full DNS pattern `<service-name>.<namespace-name>.svc.cluster.local`. A name is unique within a namespace, but not across namespaces.

As mentioned in [Openshift](https://docs.openshift.com/online/pro/architecture/core_concepts/projects_and_users.html#namespaces) documentation, namespaces provide a unique scope for:

* Named resources to avoid basic naming collisions.
* Delegated management authority to trusted users.
* The ability to limit community resource consumption.

Most objects in the system are scoped by namespace, but some are excepted and have no namespace, including nodes and users.

#### Autoscaling

The reference architecture uses two scaling techniques to properly increase or decrease the number of runtime components to accommodate a workload:

* Nodes/VMs autoscaling
* Kubernetes pod autoscaling

##### Node scaling

Depending on your platform, node autoscaling of your Openshift cluster may be possible. This allows new nodes to be created and added to the cluster when a pod cannot be scheduled because of insufficient resources (CPU or RAM). For example, when you perform a rolling update of an API gateway, Openshift will create pods from the new docker configuration, before deleting the old ones. For example, if you run six replicas with a maximum of two CPUs per pod, Openshift will need six more CPUs to create a new deployment during a short time. This means that Openshift will create new nodes to meet the demand. Without node autoscaling, should your cluster reach capacity, nodes will have to be manually added to the cluster to handle the increased usage.

##### Horizontal Pod Autoscaler

Using Horizontal Pod Autoscaler (HPA) you can automatically scale the number of API Gateway components. HPA uses a control loop that checks selected utilization metrics every 15 seconds (default value). There are several options for triggering autoscaling. The average CPU utilization is anyone that can be used. It should be set to a high enough value for optimal resource usage. When CPU utilization exceeds this threshold, Openshift adds more pods. You need to test what would be a good CPU utilization based on your pod startup time, traffic pattern, and potential impact on the overall performance. An average CPU utilization of 75 percent is a good starting point. Keep in mind that to get HPA working, you need to define resource limits (notice, that our CPU limit is 2cpu). The following is an example of this setting in Helm:

```
metrics:
- type: Resource
  resource:
    name: cpu
    target:
      type: Utilization
      averageUtilization: 75
```

#### External traffic

Our helm chart provides the ability to use either routes or ingress to expose API management apps and services to external clients. These components are able to dynamically create externally reachable URLs for desirable endpoints, load balance traffic between pods, as well as provide TLS management.

A specific DNS entry is required to route requests to a service inside a Kubernetes cluster. These are the default exposed interfaces in our helm configuration:

* `anm.FQDN` for API Gateway Manager UI.
* `apimgr.FQDN` for API Manager UI.
* `apitraffic.FQDN` for API traffic.

DNS records must match the certificate and host configuration. For more information, see [Openshift](https://docs.openshift.com/container-platform/4.11/networking/understanding-networking.html) documentation.

### API Management implementation details

Refer to our helm chart documentation, [Deploy API Gateway using Helm](/docs/apim_installation/apigw_containers/deployment_flows/axway_image_deployment/helm_deployment/), for details on deploying API Manager in containers.

### Cassandra considerations

It is recommended to host Cassandra outside of openshift, in its own VM cluster. Minimum three nodes are required: one node in each availability zone. For more information, see [Administer Apache Cassandra](/docs/cass_admin/).

The current supported Cassandra version is 4.0.11.

Communication and access to the database must be protected:

* Use login and password.
* Configure mutual authentication with a valid certificate in Cassandra security options.
* Deactivate SSLv3 and TLSv1 protocols.

For a single region configuration, an initial replication must be set as follows:

* Keyspace must have an initial replication of three with a simple strategy.
* Throttling configuration must be configured with an initial replication of three with a simple strategy. Read and write consistency level at LOCAL_QUORUM.

A JMX exporter must be configured on each Cassandra node to monitor Cassandra. The listener is configured with the default port of `7070`.

### Security considerations

The following is a summary of security considerations discussed within this document:

* Any administrative access to infrastructure components is enabled through a bastion subnet only.
* Openshift uses RBAC permissions to set roles per user or group.
* CNI plugin protects communications inside the cluster.
* All external connections are protected by an ingress or routes with a public certificate.
* In the application layer, sensitive data is protected inside a secret. All communications are encrypted in a cluster. API Management uses only TLS 1.2.
* Do not run containers in a privileged mode.
* Only allow communication on required ports.

For additional information on security, see [Openshift security](https://docs.openshift.com/container-platform/4.11/security/index.html) documentation.

### SQL database considerations

Amplify API Management supports several RDBMS:

* MySQL or MariaDB
* Microsoft SQL Server
* Oracle database

### Logging and tracing

By default, logs will not persist within a container in the event of certain failures or restarts. However, it is important to maintain some of this information in order to accurately monitor and debug a system. The following logs must be persisted:

* Trace files.
* Admin Node Manager: `INSTALL_DIR/trace`.
* API Gateway instance: `INSTALL_DIR/groups/<group-id>/<instance-id>/trace`.
* Transaction audit log: see [Transaction audit log settings](/docs/apim_reference/log_global_settings/#transaction-audit-log-settings).
* Transaction access log: see [Transaction access log settings](/docs/apim_reference/log_global_settings/#transaction-access-log-settings).
* Transaction event log: see [Transaction event log settings](/docs/apim_reference/log_global_settings/#transaction-event-log-settings).
* Open traffic event log: see [Open traffic event log settings](/docs/apim_reference/monitor_traffic_events_metrics/#open-traffic-event-log-settings).

Trace, open traffic, and audit logs are streamed to a containers stdout by default. It is recommended to use a logging tool, such as [Fluentd](https://www.fluentd.org/), to stream these logs to an external source to persist and manage them efficiently.

### Monitoring

OpenShift Container Platform delivers monitoring best practices out of the box. It includes a preconfigured, preinstalled, and self-updating monitoring stack that provides monitoring for core platform components. It also allows you to enable monitoring for user-defined projects.

A cluster administrator can configure the monitoring stack with the supported configurations.

For more details on the components and usage of monitoring, see [Openshift Monitoring](https://docs.openshift.com/container-platform/4.11/monitoring/monitoring-overview.html) documentation.

## Configuration and data backups

It is essential for the smooth operation of an API Management solution that you perform regular back ups of data and configurations. At a minimum, the following data, source code, and artifacts must be included:

* Policies and server configuration. There are different ways to maintain your deployment artifacts. You can maintain an API Gateway configuration as a Policy Studio folder in a source code management system (SCM). GitHub and GitLab are two popular choices. As an alternative, you can version control configuration files.

In addition to the source code, the following also must be maintained:

* Yaml or fed configuration, as well all other modified configuration files, such as jvm.xml, envSettings.props, and so on.
* Deployment artifacts in the form of a Docker image, if you are building and maintaining you own images.
* RDBMS backup: use the appropriate backup procedure for the selected RDBMS. Axway recommends selecting geo-redundant backup storage where possible.
* Cassandra: follow recommendations in [Cassandra backup and restore](/docs/cass_admin/admin_cassandra_classic/cassandra_bur/). A daily backup is good practice, however you might decide to backup more or less often depending on what data you store (KPS, API Manager, OAuth) and how much data you can afford to lose in case of disaster.
* Log, trace, event files: as mentioned, it is recommended to use a logging tool to move these logs to a service where they can be persisted.

## Disaster recovery

Disaster recovery configuration is defined by your needs and acceptable downtime in the event of system failure. Using backed up configuration, data, and Docker registry, you should be able to run a CI/CD pipeline for creating a new Openshift cluster in another region and get a similar system back on line. However, if downtime is not acceptable, you might want to run a multi-region/multi-data centre openshift cluster that will continue to operate in the event that one region or data centre is lost.

## Known constraints and roadmap

As of Amplify API Management v7.7, there are some differences or constraints compared to the classic mode deployment:

* Embedded Analytics is not supported in container mode. They should be deployed outside of a Openshift cluster.
* Distributed Ehcache is not supported. However, you can use Apache Cassandra as a distributed data store where CRUD operations are supported to directly interact with KPS, using scripts.
* Running with Embedded ActiveMQ configured in an instance is not advised. The recommendation is to use a JMS provider deployed outside of the API Management cluster.

API Management v7.7 does not support all the configurations that currently exist for the classic deployment. Axway plans to address these limitations by delivering the following capabilities in the future:

* Broader support of current APIM configurations.
* Native support of Embedded Analytics.
* Advancements in the current container mode.

For more information, see [roadmaps](https://community.axway.com/s/product-roadmaps) on Axway Community Portal.

## Glossary of terms

The following are common terms that are used in this document.

|Reference| Description|
|----| ----|
|SAML        |Security Assertion Markup Language - based on XML, this protocol permits SSO authentication|
|SSO         |Single Sign-On - unique authentication for a user|
|FED         |The file extension of the federated deployment package (policy + environment packages)|
|POL         |The file extension of a policy package file|
|ENV         |A file extension of an environment package file|
|VM          |Virtual Machine|
|PaaS        |Platform as a Service|
|HA          |High Availability|
|RDBMS       |Relational Database Management System|
|RBAC        |Role Based Access Control|