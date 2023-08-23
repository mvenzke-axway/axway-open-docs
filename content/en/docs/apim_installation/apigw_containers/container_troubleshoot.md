---
title: Troubleshoot container deployments
linkTitle: Troubleshooting
weight: 110
date: 2019-09-18
description: Problems you might encounter when running API Gateway and API Manager in Docker containers, and possible solutions. 
---

## Reset the default API administrator password

Problem
: You have forgotten the password for the default API administrator user in API Manager and you cannot log in to the API Manager UI.

Solution
: After the default API administrator account has been created in Apache Cassandra, you cannot change the password in Policy Studio. If you cannot log in to the API Manager UI to change the password, you must use the `setup-apimanager` script with the `--resetPassword` option to reset it. Follow these steps:

1. Use the `docker exec` command to connect to the running Admin Node Manager Docker container.

    ```none
    docker exec -it <anm-container-id> bash
    ```

2. Change to the `bin` directory.

    ```none
    cd /opt/Axway/apigateway/posix/bin
    ```

3. Run the `setup-apimanager` script with the `--resetPassword` option.

    ```none
    ./setup-apimanager --resetPassword
    ```

## Manage KPS with kpsadmin

Problem
: You need to perform administrative or management of a key property store (KPS) in a container deployment.

Solution
: To run the `kpsadmin` tool in a container deployment, you must connect to the Admin Node Manager container and run the tool from there. Follow these steps:

1. Use the `docker exec` command to connect to the running Admin Node Manager Docker container.

    ```none
    docker exec -it <anm-container-id> bash
    ```

2. Change to the `bin` directory.

    ```none
    cd /opt/Axway/apigateway/posix/bin
    ```

3. Run the `kpsadmin` tool.

    ```none
    ./kpsadmin
    ```

For more information on using `kpsadmin`, see [API Gateway Key Property Store User](/docs/apim_policydev/apigw_kps/).

## Logs do not persist when container stops

Problem
: Traffic monitor data and trace logs do not persist when a container stops.

Solution
: You can redirect the trace and traffic logs to `stdout` instead of to separate files, which allows the logs to be read directly from each container by an external logging service. For more information, see [Redirect logs to stdout](/docs/apim_installation/apigw_containers/configure_log_streaming/).

## Use Apache Cassandra as a distributed data store

Problem
: Distributed Ehcache is not supported in a container deployment.

Solution
: You can use Apache Cassandra as a distributed data store. This involves using the KPS scripting API, which enables you to perform CRUD operations and interact directly with a KPS. For more information, see [API Gateway Key Property Store User](/docs/apim_policydev/apigw_kps/).

## API Gateway Manager users cannot be added or changed

Problem
: When you try to add or edit users on API Gateway Manager, you find that all options are disabled.

Solution
: This is deliberately disabled because API Gateway Manager users are stored in the `adminUsers.json` file, and changes to that file do not persist when the container stops. As a consequence, there is no way to enable the ability to add or edit users within API Gateway Manager. Instead, you should store and manage these users externally by customizing an Admin Node Manager FED file to [integrate with LDAP](/docs/apim_administration/apigtw_admin/general_rbac_openldap) or [integrate with Active Directory](/docs/apim_administration/apigtw_admin/general_rbac_ad_ldap), then [creating an ANM image using existing fed and customized configuration](/docs/apim_installation/apigw_containers/deployment_flows/custom_image_deployment/docker_script_anmimage).

Alternatively, you can use the [container User Management](https://github.com/Axway-API-Management-Plus/emt-user-management) script to create a new `adminUsers.json` file with the desired users and include that custom file in your images.
