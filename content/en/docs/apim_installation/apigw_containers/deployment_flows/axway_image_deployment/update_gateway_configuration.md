---
title: Update API Gateway configuration
linkTitle: Update API Gateway configuration
weight: 24
date: 2023-01-24
hide_readingtime: true
description: How to update the configuration of the API Gateway Helm deployment.
---

The Admin Node Manager, API Gateway, and Analytics images are built with a default configuration, which can be updated at runtime by externalizing the customized configuration files.

The API Gateway image is built with YAML configuration by default but it can be updated with FED configuration. Updates to the configuration are done by externalizing the deployment package (FED or YAML) as outlined in [Mount a component configuration](/docs/apim_installation/apigw_containers/deployment_flows/axway_image_deployment/helm_deployment#mount-a-component-configuration).

For more information on mount configuration, see [Sample customized values file](/docs/apim_installation/apigw_containers/deployment_flows/axway_image_deployment/helm_deployment#sample-customized-values-file).

### Promoting configuration between environments

To simplify promotion between environments, it is recommended that you environmentalize as much as the configuration as possible. When done correctly, the same configuration files can be used between environments, with any changes between environments being set through the helm `values.yaml` file. For more information, see [Environment variables reference](/docs/apim_installation/apigw_containers/deployment_flows/axway_image_deployment/helm_runtime#environment-variables-reference).