---
title: Runtime parameters
linkTitle: Runtime parameters
weight: 26
date: 2023-02-15
description: Customize the behavior of a container at runtime.
---

When you deploy the API Gateway Helm chart, you can customize the behavior of the applications (Admin Node Manager, API Gateway, API Manager, and API Gateway Analytics) at runtime:

* You can pass environment variables to the `helm` command via your local `values` file.
* You can configure or add extra items, such as volume mounts, to the deployment by adding them to your local `values` file.

{{< alert title="Note" color="primary" >}}To run API Gateway, API Manager, Admin Node Manager, and API Gateway Analytics containers in Helm, you must accept Axway General Terms and Conditions. For more information, see [Acceptance of General conditions for license and subscription services](/docs/apim_installation/apigw_containers/deployment_flows/axway_image_deployment/helm_deployment/#acceptance-of-general-conditions-for-license-and-subscription-services).{{< /alert >}}

## Environment variables reference

A large number of environment variables are already defined with default values in the `values.yaml` file that is shipped with the Helm chart. For more information, see [Fetch the Helm chart to examine the values file](/docs/apim_installation/apigw_containers/deployment_flows/axway_image_deployment/helm_deployment/#fetch-the-helm-chart-to-examine-the-values-file).

The following table describes the environment variables set in the `values.yaml` Helm chart.

| Variable Name | Description |
|------|-------|
| `EMT_DEPLOYMENT_ENABLED`         | Enables deploying changes to a running container by way of Policy Studio only. **Note**: This parameter should only be used in a development environment as the changes only last for the lifetime of the current pods. In production, the FED updates should be done using the `merge` folders.|
| `EMT_TOPOLOGY_LOG_DEST`          | Specifies the destination to which topology log records are written. Possible values: 1 (Topology log), 2 (Trace log), 3 (both). Defaults to 1.   |
| `EMT_TOPOLOGY_LOG_DIR`           | The directory into which topology logs are written. Defaults to `/opt/Axway/apigateway/logs`. If you mount this directory as a volume, you can set a location outside the container to write the logs. |
| `EMT_TOPOLOGY_LOG_ENABLED`       | Specifies whether topology logging is enabled or disabled. Possible values: `true`, `false`. Defaults to `false`.  |
| `EMT_TOPOLOGY_LOG_INTERVAL`      | Time interval, in seconds, between successive log records for each gateway. Defaults to 60. Maximum value: 3600. For example, if set to 60, then a log record is written once a minute for each gateway, recording the number of transactions since the last log record was written.  |
| `EMT_TOPOLOGY_TTL`               | The time, in seconds, that an Admin Node Manager container will wait without receiving a registration request from a gateway before it deletes that gateway from the topology. When a gateway is removed from the topology, it disappears from the topology view in the API Gateway Manager UI. This could occur, for example, if the container orchestration framework scales down the number of gateway containers in response to dropping traffic levels. This parameter has a default value of `120`, and it should not normally need to be set explicitly. Sample usage: `-e EMT_TOPOLOGY_TTL=140`.|
| `EMT_HEAP_SIZE_MB`               | Specifies the initial and maximum Java heap size for the Admin Node Manager, API Gateway, or API Gateway Analytics processes. JVM processes within a Docker container sometimes set their heap size from the limit of the host machine, thereby choosing a value that is too high. You can use this parameter to limit the memory footprint of the JVM. Sample usage: `-e EMT_HEAP_SIZE_MB=4096`. |
| `EMT_TRACE_LEVEL`                | Specifies the trace level for an Admin Node Manager or API Gateway container. This can be useful for debugging issues in the runtime environment  without having to build a new image. Possible values: INFO, DEBUG, DATA. Defaults to INFO. Sample usage: `-e EMT_TRACE_LEVEL=DEBUG`. |
| `APIGW_LOG_OPENTRAFFIC_OUTPUT`   | Log open traffic data. Defaults to `stdout`. |
| `APIGW_LOG_TRACE_JSON_TO_STDOUT` | Output JSON formatted trace to `stdout`. Defaults to `true`. |
| `APIGW_LOG_TRACE_TO_FILE`        | Write trace files to disk. Defaults to `true`.|
| `GROUP_ID`                       | API Gateway Group Id. Can be set as required. Default value is `DefaultGroup`. |
| `CASS_HOST`                      | Apache Cassandra host used to store the API Manager data.  |
| `METRICS_DB_URL`                 | URL to connect with the metrics database. |
| `METRICS_DB_USERNAME`            | User name to connect with the metrics database. |
| `METRICS_DB_PASS`                | Password to connect with the metrics database.  |
| `EMT_FIPS_MODE`                  | Enables FIPS mode. Defaults to `false`. |

In your local `values` file, you can both override the values of the variables and add additional environment variables. If you wish to do so, you must include the full list in your local `values` file because the Helm chart does not merge the environment variables from both files.

You can also add your own environment variables to your configuration, which can then be set and modified in the `values` file. To do so, just populate any compatible field with `${environment.YOUR_VARIABLE}`.

## Modify other areas of the deployment

The API Gateway Helm chart ships with some volume mounts, but others can be added in the local `values` file as demonstrated in the [Sample customized values](/docs/apim_installation/apigw_containers/deployment_flows/axway_image_deployment/helm_deployment#sample-customized-values-file) file where the following lines were added in order to persist events:

```
  extraVolumeMounts:
    - name: events
      mountPath: /opt/Axway/apigateway/events
  extraVolumes:
    - persistentVolumeClaim:
        claimName: events
      name: events

```

### API Gateway licenses

For details on updating other areas of configuration, see [Update API Gateway configuration](/docs/apim_installation/apigw_containers/deployment_flows/axway_image_deployment/update_gateway_configuration).
