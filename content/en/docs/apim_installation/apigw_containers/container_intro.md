---
title: Overview
linkTitle: Overview
weight: 10
date: 2019-09-18
description: Overview of the container deployment process and an example containerized topology. 
---
Deploy API Gateway and API Manager in Docker containers on a cloud platform hosted by infrastructure-as-a-service (IaaS) providers, and use the elastic capability that their automation tools and techniques provide. This enables you to manage the load on your system easily, as well as adding and removing nodes as needed.

## How container deployments differ from classic deployments

There are significant differences when deploying in containers and running in EMT mode compared to deploying and running in non-containerized classic mode. The following table outlines some of the differences.

|Container deployment|Classic deployment|
|---------|------|
| Docker manages API Gateway topology.         | Admin Node Manager manages API Gateway topology.   |
| An API Gateway domain comprises one Admin Node Manager and one or more API Gateways.  | An API Gateway domain comprises one Admin Node Manager, one or more Node Managers, and one or more API Gateways.  |
|API Gateway configuration (FED file) is baked into the API Gateway Docker image. To deploy changes to API Gateway configuration, you must export a FED file from Policy Studio, rebuild the API Gateway Docker image, and restart the API Gateway Docker container.|   You can deploy changes to API Gateway configuration directly from Policy Studio. |  
|You can easily scale a domain up or down by starting or stopping Docker containers.     | You cannot easily scale a domain up or down. The Admin Node Manager manages starting and stopping API Gateways. |

{{< alert title="Note" color="primary" >}}In a development environment, when you deploy in containers, you can use the `EMT_DEPLOYMENT_ENABLED` environment variable when starting an API Gateway container to enable deploying changes directly from Policy Studio to the running container. {{< /alert >}}

## Example containerized topology

In the classic, non-containerized deployment, the topology is managed internally by way of an Admin Node Manager communicating with node managers in API Gateway nodes. However, in a container deployment, the topology is externally managed. Instead of the Admin Node Manager, a cluster manager (such as, Openshift or Kubernetes) manages the topology and communicates directly with each API Gateway.

The API Gateway Docker images do not include a Node Manager.

The following diagram shows an example of the topology in a container deployment:

![Example elastic topology architecture.](/Images/ContainerGuide/elastic_topology_arch.png)

### API Gateway containers

In the example topology, four API Gateway nodes run in Docker containers:

* The containers have been deployed from two images that use different FED files (`file1.fed` and `file2.fed`), thus forming two groups, `EMT - Group 1` and `EMT - Group 2`.
* Because there are no node managers, each API Gateway node exposes the management interface directly on port `8085`.

### Admin Node Manager container

The topology includes one Admin Node Manager (*Container 5*), but with a different role than in the classic deployment. The Admin Node Manager in this example,

* *does not* manage the topology, but is managed by the same cluster manager as the API Gateway nodes.
* provides access to monitoring data and runtime settings.
* builds a dynamic picture of the topology:
    * New API Gateway nodes automatically start sending transaction event logs to the Admin Node Manager when they are started.
    * The Admin Node Manager listens to the API Gateway nodes, processes the received logs, and writes them to the metrics database.
    * The Admin Node Manager container uses a shared volume to access the transaction event logs from API Gateway containers.

### Cluster manager

A cluster manager manages the topology, and adds or removes nodes as the load on the system changes:

* When a node is added, the image for the new node is passed to the cluster manager that starts the required number of containers.
* When a node is removed, the cluster manager waits for the traffic to the node to stop before removing it.

### Apache Cassandra

If the deployment includes API Manager, then Apache Cassandra is configured for high availability (HA) to store API Manager data and Key Property Store (KPS) tables. For more details, see
[Configure a Cassandra HA cluster](/docs/cass_admin/admin_cassandra_classic/cassandra_config/).

### Discovery and Traceability agents

If the deployment includes API Manager, then Discovery and Traceability agents can be used to send information to Amplify Central. For more information, see [Set up Discovery agent](https://docs.axway.com/bundle/amplify-central/page/docs/connect_manage_environ/connect_api_manager/gateway-administation/index.html#discovery-agent) and [Set up Traceability agent](https://docs.axway.com/bundle/amplify-central/page/docs/connect_manage_environ/connect_api_manager/gateway-administation/index.html#traceability-agent).
