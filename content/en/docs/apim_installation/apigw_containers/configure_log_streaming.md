---
title: Redirect logs to stdout
linkTitle: Redirect logs to stdout
weight: 75
date: 2019-09-18
description: Configure the API Gateway logging system to redirect the trace and traffic logs to `stdout` instead of to separate files, allowing the logs to be read directly from each container by an external logging service (for example, Elastic Stack or Splunk).
---

## Modify trace logs

The trace log behavior can be modified through the `trace.xml` file or the system environment variables.

{{< alert title="Note" color="primary" >}}The environment variables override the `trace.xml` file settings. This enables the logging behavior of individual containers to be defined at runtime.{{< /alert >}}

### Redirect trace logs using system environment variables

To redirect trace logs using environment variables, ensure the following environment variables are passed to the container:

```
APIGW_LOG_TRACE_TO_FILE=false
APIGW_LOG_TRACE_JSON_TO_STDOUT=true
```

### Redirect trace logs using the trace.xml file

To redirect trace logs using using the `trace.xml` file, update the `INSTALL_DIR/apigateway/system/conf/trace.xml` file as follows:

1. Disable the trace file on disk by commenting out the following line:

   ```
   <!--<FileRolloverTrace maxfiles="500" filename="%s\_%Y%m%d%H%M%S.trc"/>-->
   ```

2. Enable JSON output by changing the `jsonOutput` property value to `true`:

   ```
   <FileTrace filename="@stdout" jsonOutput="true"/>
   ```

## Modify open traffic logs behavior

The open traffic log behavior can be modified through the Policy Studio settings or system environment variables.

{{< alert title="Note" color="primary" >}}The environment variables override the Policy Studio settings. This enables the logging behavior of individual containers to be defined at runtime.{{< /alert >}}

### Redirect open traffic logs using Policy Studio

1. Select the **Server Settings** node in the Policy Studio tree.
2. Click **Logging > Open Traffic Event Log**.
3. Select **Use console/traces** from the **Event Log Output** field.

### Redirect open traffic logs using system environment variables

Ensure the following environment variable is passed to the container:

```
APIGW_LOG_OPENTRAFFIC_OUTPUT=stdout
```

## Further information

For more information on trace logging and open traffic logging, see [API Gateway Administrator](/docs/apim_administration/apigtw_admin/). This guide also describes the open logging JSON schema.

For more information on the environment variables that you can specify at runtime, see [Environment variables reference](/docs/apim_installation/apigw_containers/deployment_flows/custom_image_deployment/container_runtime#environment-variables-reference).
