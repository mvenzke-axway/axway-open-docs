{
"title": "Upgrade an Apache Cassandra in Containers",
  "linkTitle": "Upgrade Apache Cassandra in Containers",
  "weight": "8",
  "draft":"true",
  "date": "2023-08-02",
  "description": "Upgrade Apache Cassandra in Containers to store data for API Manager or API Gateway client registry (API key and OAuth)."
}

### Upgrade Apache Cassandra Containers

Upgrading Cassandra in containers should vastly simplify the problem. While the available offerings may handle some things differently, most will lean on the orchestration of kubernetes or helm, to handle the upgrade as smoothly as possible. In some cases this may be as seemless as updating to the latest helm chart version, where as others may require some more hands on steps, before or after an upgrade.

Please be sure to carefully read all documentation related to upgrading for your chosen cassandra solution, prior to any installation, so you are aware of the ongoing impact this will have.