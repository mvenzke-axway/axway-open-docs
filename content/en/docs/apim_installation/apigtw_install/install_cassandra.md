{
"title": "Install Apache Cassandra",
  "linkTitle": "Install Apache Cassandra",
  "weight": "8",
  "date": "2019-10-02",
  "description": "Install Apache Cassandra in virtual machines (VMs), or deploy it in containers, to store data for API Manager or API Gateway client registry (API key and OAuth)."
}

Apache Cassandra is required to store data for API Manager (for example, API catalog, quotas, and client registry) or API Gateway client registry (API key and OAuth). In addition, Cassandra is optional to store data for the following API Gateway components:

* Custom KPS table definitions and data.
* OAuth token stores.

{{< alert title="Note" color="primary" >}}You must ensure that Cassandra is installed and running to use API Manager or API Gateway client registry.{{< /alert >}}

## Install Cassandra in VMs

This section describes the requirements, recommendations, and procedures to install Cassandra in virtual machines (VMs).

### Supported Cassandra versions

API Gateway supports Cassandra versions 3.11.11 and 4.0.11. The recommended version is 4.0.11. For more details, see [Apache Cassandra](http://cassandra.apache.org/) documentation.

* For details on upgrading your Cassandra version, see [Upgrade Apache Cassandra](/docs/apim_installation/apigw_upgrade/upgrade_cassandra/).
* For details on upgrading your API Gateway version, see [API Gateway Upgrade Guide](/docs/apim_installation/apigw_upgrade/).

### Production environment requirements

API Gateway supports the following in a production environment:

* **Operating systems**:
    * All supported Linux platforms
* **Cassandra**:
    * Cassandra version 4.0.11
    * 64-bit OpenJDK JRE or Oracle JRE version 8

{{< alert title="Note" >}}The prerequisites are a minimum configuration. For environments built on AWS, s3.large instances might not be enough depending on the volume of data in production. In this case, an upgrade to a m4.xlarge instance can be better to meet the high demand in a production environment.{{< /alert >}}

For API Gateway requirements, see [API Gateway Prerequisites](/docs/apim_installation/apigtw_install/system_requirements), and for requirements for high availability, see [Configure a Cassandra HA cluster](/docs/cass_admin/admin_cassandra_classic/cassandra_config/).

### JRE requirements and recommendations

The default API Gateway installation includes a 64-bit OpenJDK JRE (`apigateway/Linux.x86_64/jre/bin`). You can configure Cassandra to use the API Gateway JRE (for example, in a demo environment), but it is recommended that you install a separate JRE (OpenJDK or Oracle) for use with Cassandra. When using a separate JRE, use the same version (or at least the same major version) as the API Gateway uses and ensure that the `JAVA_HOME` environment variable is set and pointing to the file system location where the JRE is installed.

### Cassandra hardware recommendations

Cassandra is designed to run on commodity distributed drives, and therefore it is strongly recommended not to use a storage area network (SAN) for Cassandra deployments.

For more information on Cassandra hardware recommendations, see [Hardware choices](https://cassandra.apache.org/doc/4.0.11/cassandra/operating/hardware.html).

### AWS general recommendations

For environments built on Amazon Web Services nodes, observe the following checklist before installing Cassandra.

* Use `M5.xlarge - M5.4xlarge` or `R5.xlarge - R5.4xlarge` instance types
* If using Ubuntu, use 20.04 AMI
* EBS volumes must have at least 10k IOPS available to them
* Use either `ext4` or `xfs` filesystems, using block size 4096 bytes (xfs is preferred)
* EBS are `gp2` optimized
* Each instance is a dedicated instance
* With a recent kernel, use the kyber IO scheduler
* Set the `current_clocksource` to `tsc`
* Ensure disk `nomerges` is enabled
* Ensure disk `read_ahead_kb` is `4`
* Ensure `zone_reclaim_mode` is off
* Ensure `irqbalance` is running (enabled)
* Ensure no swap is mounted, and swappiness is set to zero
* Ensure `/etc/localtime` is symlinked to your correct timezone, for example, Europe/Berlin
* Ensure an `ntp` client is running on system (`chrony` is a good option for AWS)

### Install Cassandra in GUI mode

{{< alert title="Note" color="primary" >}}Apache Cassandra 4.0.11 is installed by default in an API Gateway standard or complete setup.{{< /alert >}}

In GUI mode, to install Cassandra only, use the steps described in [API Gateway Installation](/docs/apim_installation/apigtw_install/installation) with the following selections:

* **Setup Type**: Select **Custom**.
* **Select Components**: Select **Cassandra**.
* **Cassandra configuration**: Enter your Cassandra **Installation Directory** and your **JRE Location**.

### Install Cassandra in unattended mode

To install Cassandra using the API Gateway installer in unattended mode, follow the steps described in [Unattended installation](/docs/apim_installation/apigtw_install/installation_unattended).

The following command is an example of how to install Cassandra in unattended mode on Linux:

```none
./APIGateway_7.7.20230530_Install_linux-x86-64_BN<n>.run --mode unattended --acceptGeneralConditions yes --setup_type advanced --enable-components cassandra --disable-components apigateway,qstart,policystudio,analytics,configurationstudio,apimgmt,packagedeploytools --cassandraInstalldir /opt/db/cassandra --cassandraJDK /opt/jre --startCassandra 0
```

### Keep Cassandra installation after API Gateway is uninstalled

To keep your Cassandra installation after API Gateway is uninstalled, you must ensure that you first install Cassandra only. For example, perform the following steps:

1. Run the API Gateway installer, and select Cassandra only.
2. Run the API Gateway installer, and select API Gateway components to install.

If API Gateway is uninstalled, Cassandra remains installed.

For more details, see Apache Cassandra documentation:

* [Start installation](http://cassandra.apache.org/)
* [Installation options](https://docs.datastax.com/en/cassandra-oss/3.x/index.html)

## Install Apache Cassandra in containers

Apache Cassandra is deployable as a containerized solution. Different projects are available and maintained, with each having different configuration methods. The following link is an example of one of these solutions - K8ssandra, an open source, cloud-native distribution of Cassandra that runs on Kubernetes.

```none
https://docs.k8ssandra.io/install/local/single-cluster-helm/
```

### Connect to the Cassandra container cluster

After your API Gateway cluster is up and running, connecting to cassandra in a container is similar to a standard installation. There might be some differences depending on whether you are only exposing it within a kubernetes cluster, or externally through something like a load balancer, but once there is a reachable endpoint, connecting is the same as in any cassandra cluster.

Some container solutions may offer their own additional methods of connecting.

For more information on how to connect your API Gateway cluster to K8ssandra, see the [K8ssandra connect](https://docs.k8ssandra.io/tasks/connect/).