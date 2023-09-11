---
title: "Administer Apache Cassandra"
linkTitle: "Administer Apache Cassandra"
weight: 53
no_list: true
date: 2019-06-05
hide_readingtime: true
description: >
  Configure and manage the Apache Cassandra database for API Gateway and API Manager.
---
Apache Cassandra is a highly scalable distributed NO-SQL database that provides high service availability.

Cassandra is required to store data for API Manager, and you can optionally use it to store data for API Gateway components, such as the Key Property Store (KPS), OAuth, and API keys.

For details on how to install the Cassandra database, see [Install an Apache Cassandra database](/docs/apim_installation/apigtw_install/install_cassandra/).

For details on how to administer a Cassandra cluster, see the following sections:

* [Administer Cassandra in virtual machines](/docs/cass_admin/admin_cassandra_classic)
* [Administer Cassandra in containers](/docs/cass_admin/admin_cassandra_containers)

## Migrate to Apache Cassandra in Containers

Before you migrate your existing on-premise Cassandra cluster to containers, you must carefully consider some aspects of your installation, for example:

* Apache Cassandra version.
* The location of your Cassandra installation (on premise or cloud).
* Cluster size.

All these aspects have a different impact on the process of migration. Therefore, we recommend you to read the related documentation on migration thoroughly prior to starting the process, and ensure you have a recent backup of your data.

For more information, see [Migrate a Cassandra cluster to K8ssandra](https://docs.k8ssandra.io/tasks/migrate/) documentation.