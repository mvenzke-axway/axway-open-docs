---
title: "Administer Cassandra in Containers"
linkTitle: "Administer Cassandra in Containers"
weight: 54
date: 2019-06-05
hide_readingtime: true
description: >
  Configure and manage the Apache Cassandra database in Containers (K8ssandra) for API Gateway and API Manager.
---

This page lists the administration and maintenance tasks related to Apache Cassandra in Containers (K8ssandra). Some of these tasks can be simplified with additional services available to configure and deploy, which do a lot of the heavy lifting. For more information, see the [K8ssandra Operator tasks](https://docs.k8ssandra.io/tasks/) documentation.

## Reaper for Cassandra repairs

Reaper is an open source tool to schedule and orchestrate repairs of Apache Cassandra clusters. Reaper enables you to maintain anti-entropy for your data, which is necessary for partition-tolerant distributed systems like your Kubernetes-managed Cassandra database. For more information, see the [K8ssandra Reaper for Cassandra repairs](https://docs.k8ssandra.io/components/reaper/) documentation.

## Medusa for Cassandra backup and restore

K8ssandra provides Helm charts for taking backups or triggering the restoration of data. This is accomplished by way of the Medusa for Apache Cassandra project. For more information, see the [Medusa for Cassandra backup and restore](https://docs.k8ssandra.io/components/medusa/) documentation.