---
title: API Gateway and API Manager 7.7 August 2023 Release Notes
linkTitle: API Gateway and API Manager August 2023
weight: 95
date: 2023-06-12
description: null
---
API Gateway and API Manager updates are cumulative, comprising new features and changes delivered in previous updates unless specifically indicated otherwise in the Release notes.

In this update, the version of Java on which the API Management solution runs, is being upgraded from version 8 to version 11. This will be mostly transparent for customers while bringing the associated libraries more up to date and, therefore, making the API Management (APIM) solution more secure. There are some knock on impacts for scripting and customizations that customers need to be aware of. For more information, see [Important changes](#important-changes) section.

In addition, as a continuation of our container maturity story, we are confirming the ability of APIM to run with K8ssandra, a Cassandra in container solution which allows for the entire APIM stack to be containerized. We have updated our documentation to include references to K8ssandra for migration, deployment, and upgrade.

This update also delivers some improvements to the Helm chart for the deployment of APIM in container.

## Installation

* To **update** your API Gateway, see [Update from API Gateway One Version](/docs/apim_installation/apigw_upgrade/upgrade_steps_oneversion/).
* To **upgrade** from an older version, see [Upgrade from API Gateway 7.5.x or 7.6.x](/docs/apim_installation/apigw_upgrade/upgrade_steps_extcass/).
* For more details on supported platforms for software installation, see [System requirements](/docs/apim_installation/apigtw_install/system_requirements/).
* For a summary of the system requirements for a Docker deployment, see [Set up Docker environment](/docs/apim_installation/apigw_containers/deployment_flows/custom_image_deployment/docker_scripts_prereqs/).

### Update a container deployment

Any custom `.fed` files deployed to a container must be upgraded using [upgradeconfig](/docs/apim_installation/apigw_upgrade/upgrade_analytics#upgradeconfig-options) or [projupgrade](/docs/apim_reference/devopstools_ref#projupgrade-command-options). They must be upgraded the same way, regardless of whether they are API Manager enabled or not. The `.fed` files contain the updates for the API Manager configuration and can be used to build containers.

## New features and enhancements

The following new features and enhancements are available in this update.

### Cassandra in containers - K8ssandra

API Gateway container installations can now be configured to run alongside Cassandra in containers by way of *K8ssandra* - a containerized solution of Cassandra that runs in Kubernetes. For more information, see [Install Apache Cassandra in containers](/docs/apim_installation/apigtw_install/install_cassandra/#install-apache-cassandra-in-containers).

### Extra initContainers support

In our Helm chart, it is now possible to add custom initContainers to any of the components of the chart. The container, its image, and any required settings can be defined in the appropriate section of the `values` file, and will then be applied upon deployment. This can be used for many different use cases, such as validating a connected service or pulling in configuration.

## Important changes

It is important, especially when upgrading from an earlier version, to be aware of the following changes in the behavior or operation of the product in this update, which might impact your current installation.

### Java version update

API Gateway now runs on Java 11.

Java has been upgraded from version 8 to version 11. The specific version is `Azul Zulu11.64+19-CA` (built on OpenJDK `11.0.19+7-LTS`). As a result, certain Java 8 features and APIs that are now deprecated or removed might affect your custom code, scripts, and integrations.

The Java upgrade will allow the development of new features, improvements, and enhancements in future releases, but it also introduces a few changes that might impact your existing application and customizations. Therefore, we recommend you to review the following sections to ensure a smooth transition to the new Java version.

#### 1. Cassandra version 3 incompatibility

Versions of Cassandra prior to version 4 (including Cassandra 2.2.12 and 3.11.11) are not compatible with Java 11.

To resolve this issue, you must either [upgrade Cassandra to version 4](/docs/apim_installation/apigw_upgrade/upgrade_cassandra/) or supply your own JRE to run Cassandra (Note that Cassandra 3.11.11 is compatible with Java 8).

#### 2. Deprecated and removed APIs

Java 11 has deprecated and removed several APIs that were present in Java 8. You must review your custom Java code, JS scripts, and Groovy scripts to identify and update any references to these deprecated or removed APIs. The most common impacted areas include:

* `javax.xml.bind` package: This package has been removed. If your code relies on JAXB for XML binding, you need to migrate to alternative libraries, such as `jakarta.xml.bind`.
* `java.security` APIs: Certain cryptographic algorithms and security protocols might be deprecated or removed. You must ensure that your security configurations are up to date because older certificates, for example, might no longer be compatible with the default `java.security` settings.
* `OpenJSSE`: OpenJSSE is not required in Java 11 and is no longer shipped with API Gateway. If customizations have direct dependencies on OpenJSSE, they must be updated.
* Function changes: Some functions might have subtle changes to their output, and this is not always covered by documentation. When tracking erroneous behavior, you must check all function outputs. For example, the `InetAddress.getLocalHost().getHostName()` function no longer returns the fully qualified domain name (FQDN), neither the method `getCanonicalHostName()`.

#### 3. APIs and third-party libraries

Check your application for dependencies on third-party libraries, for example, libraries added to `ext/lib` to support scripts or customization. Ensure that you are using versions compatible with Java 11 as some libraries might require updates to work properly with the new Java version.

#### 4. Testing and quality assurance

Thoroughly test your application after the upgrade to ensure that all functionalities are working as expected. Pay special attention to areas that interact with custom code, scripts, or third-party libraries.

#### 5. Compatibility testing

If your application integrates with other systems or services, ensure that these integrations remain compatible with Java 11. Check for potential issues related to communication protocols, data formats, and security mechanisms.

#### 6. Backward compatibility

While we have tried to cover all possible scenarios to minimize disruptions, certain customizations might require adjustments to be fully compatible with Java 11. Be prepared to invest time in adapting your API Gateway installation to ensure long-term stability.

If you have customizations in the jvm.xml file, or other configuration files, it is recommended that these files are backed up and the customizations are re-applied manually, post upgrade to the newly added configuration files and tested for compatibility.

#### 7. Kerberos

The use of weaker cryptography algorithms has been restricted in Kerberos in this release. If your Kerberos infrastructure requires the use of RC4-HMAC or any of the DES family of algorithms, then you must set the `allow_weak_crypto` property to `true` in the `[libdefaults]` section in **Server Settings > Security > Kerberos > krb5.conf**.

Example:

```none
[libdefaults]
allow_weak_crypto = true
default_tkt_enctypes=rc4-hmac
default_tgs_enctypes=rc4-hmac
```

#### 8. Java Keystore (JKS) format

In Java 11, the default format for Java keystores (JKS) is `PKCS#12`. The older JKS format is still supported for reading but newly created keystores will be `PKCS#12`, unless explicitly stated to be otherwise. This can have an impact when sharing JKS files with other services, for example, when configuring an SSO IdP.

#### 9. Apache Commons Email

To support email functionality, we have forked the Apache Commons Email project to migrate from using `javax.mail` to using `jakarta.mail`, and from `javax.activation` to `jakarta.activation`. This is a temporary measure for this API Gateway update.

The forked project and license information can be found at the [Axway Apache Commons](https://github.com/Axway/commons-email/tree/v2.0.0_AXWAY_BRANCH) project on GitHub.

### API Client Cache changes

The API Client cache performance has been significantly improved in the [November 2022](/docs/apim_relnotes/20221130_apimgr_relnotes/) update accommodating new Organization, Application, and Application Client ID changes that could be missed while updating the internal cache with large amounts of Applications and Application Client ids, but in some scenarios, API Manager user traffic could still be held for periods of time while the cache was updating.

Now, in this update, the API Client cache performance has been improved further and it is no longer blocking while updating - it will always serve any request to access its contents.

### API Catalog property removed

The `com.axway.apimanager.configure.catalog.parallel.enabled` Java system property, introduced in the [March 2021](/docs/apim_relnotes/20210330_apimgr_relnotes/) update, has been now removed. As a result, the API Catalog now always loads in a multi-threaded manner and in parallel with the API Runtime cache (Virtualized API traffic brokers) and API Client cache. The performance improvement during startup and deployment, which was previously provided by this property, is now present by default.

For more information, see [System property changes](/docs/apim_reference/system_props/).

### Non-blocking API property updated

The `com.axway.apimanager.configure.apis.nonblocking.enabled` Java system property, introduced in the [November 2020](/docs/apim_relnotes/20201130_apimgr_relnotes/) update, behaves differently in this update.

Previously, the property allowed the API Runtime cache and the API Catalog to load in parallel, along with detaching the loading of these caches from the boot sequence (which applies to API Gateway startup and deployment).

As of this update, the following caches always load in parallel, meaning that customers who were using this property for improvements in startup and deployment performance can stop using it:

* API Catalog
* API Runtime cache
* API Client cache

This property is now only responsible for detaching the loading of the caches from the gateway boot sequence.

The property default is unchanged, `false`. If set to `true`, the gateway will complete the boot sequence while the three caches load in the background. API requests to VAPI Catalog and Virtualized APIs are held during their loading process, and only performed after they have finished loading. However, as described in the [API Client cache changes](#api-client-cache-changes) section, brokers will always be able to access the API Client cache by default, even while the cache is loading or updating.

{{< alert title="Note" color="primary">}}When this property set to `true`, the API Runtime cache could complete loading before the API client cache, resulting in requests to virtualized APIs being processed. If these requests access the API Client cache to authenticate requests, valid requests could be declined as unauthenticated if the cache has not finished loading. {{< /alert >}}

For more information, see [System property changes](/docs/apim_reference/system_props/).

### New field in OAS3 definitions for API Manager REST APIs

The OAS3 definitions for the API Manager REST APIs, specifically the VirtualizedAPI, Application, Organization, and User models now include an `additionalProperties` field to facilitate correctly defining custom properties for those models. For more information, see [Fixed issues](#fixed-issues) section.

### CORS Origin header matching as URL

The CORS `Origin` header value in CORS requests must now be a valid URL. A CORS request will now be denied if the received `Origin` header:

* is not a valid URL, or
* does not match any of the configured allowed CORS `origins`

If the optional `[path][?query][#fragment]` parts are present in the received `Origin` header value, they are now stripped out and then matched as `scheme://host[:port]`. For example, given the `Origin` header value `https://example.domain/service?param=value`, only `https://example.domain` will be considered for matching purposes.

If a `null Origin` header value is needed, then a `null` value can be configured in the **Origins** table of the CORS profile.

For more information, see [Configure Cross-Origin resource sharing](/docs/apim_policydev/apigw_gw_instances/general_cors/).

### Generic error filter SOAP fault response changes

As of this release, the **Generic Error** filter correctly generates the corresponding SOAP 1.2 fault response using the default stylesheet when there is no content body on the message whiteboard. The existing response code on the message whiteboard is preserved, otherwise the response code configured in the filter is set on the message whiteboard.

For more information, see [Fault and error handling filters, Generic error filter](/docs/apim_policydev/apigw_polref/fault_filters#generic-error-filter).

### Incomplete Web Services cannot process SOAP requests

The **Set Service Context** filter has never supported the **Failure Path** execution flow, but it incorrectly returned `false`, instead of aborting the circuit execution when the service name was undefined, and logged the filter **Failure** message.

The filter now aborts the circuit execution and logs the corresponding filter **Fatal** message, instead of the **Failure** message.

The circuits that required to maintain the **Failure Path** execution flow where the service name is undefined should implement a **Fault Handler** filter returning `false`. The API Gateway REST APIs and default API Manager `API Broker` circuits are unaffected by the change.

The **Web Service** filters with incomplete configuration are now reported in the API Gateway trace during deployment or startup, and such filters will not handle the corresponding SOAP requests until the reported errors are addressed. At the same time, the correctly configured **Web Service** filters will work as expected.

For more information, see [Monitoring and logging filters, Set service context filter](/docs/apim_policydev/apigw_polref/monitoring_logging#set-service-context-filter), [Web service filters, Set web service context filter](/docs/apim_policydev/apigw_polref/web_services#set-web-service-context-filter) and [Fault and error handling filters](/docs/apim_policydev/apigw_polref/fault_filters/).

<!-- RDAPI-9999 -->

### Support for Cassandra 4.0.10

API Gateway now supports Cassandra 4.0.10. For more information, see [Upgrade Apache Cassandra to 4.0.10](/docs/apim_installation/apigw_upgrade/upgrade_cassandra/upgrade_cassandra_v4).

### API Manager OAuth authorizations screen changes

The API Manager OAuth authorizations screen has been changed to filter authorizations on the back-end rather than on the screen. Searching for authorizations now requires either the application or the owner, or both. Partial matches will no longer be returned. For more information, see [Manage OAuth authorizations](/docs/apim_administration/apimgr_admin/api_mgmt_admin#manage-oauth-authorizations).

### Metrics database TopologyID change

Topology IDs in containerized deployments of API Gateway Analytics can be over 32 characters long. As such, the metrics database TopologyID size has been increased from 32 to 255 characters. For more information on how to update your metrics database, see [Configure a metrics database](/docs/apim_installation/apigtw_install/metrics_db_install#set-up-the-database-tables)

### Helm chart values file changes

Previously, some critical parameters were set in the extraEnvVars section of the `values` file. This could lead to inconsistencies when fields were merged if an override `values` file was applied. All variables have now been removed from this section, leaving it just for additional values a user might want to set. Critical parameters now have their own section in the values file, such as `jvmHeapSize` or `logs.trace`, where others have just been left as their default. Any value can still be defined in the extraEnvVars section, with this taking priority over other configurations. Use of an existing `values` files will continue to behave as before. For full details, refer to the `values` file in the latest chart.

The environment variable EMT_DEPLOYMENT_ENABLED has been removed as a default option from our helm chart. Without it, the setting reverts back to its default, which is to have this option disabled. It can still be configured by adding the variable to the extraEnvVars section of the `values` file.

For more information on this variable, see [Environment variables reference](/docs/apim_installation/apigw_containers/deployment_flows/axway_image_deployment/helm_runtime/)

To provide compatibility for both Kubernetes and OpenShift, our images are built in such a way that when deploying in Kubernetes, requires the user, group, and filesystem ID to be set on deployments running where `runAsNonRoot` is set to true. This is now the default configuration in our `values` file. In order to run on OpenShift, these values need to be set to `null`. For full details refer to the `values` file in the latest chart.

## End of support notices

To stay current and align our offerings with customer demand and best practices, Axway might discontinue support for some capabilities.

As part of this update, the following features have notice for removal:

<!-- There are no end of support notices in this update. -->

* Support for IBM DB2 version 10.5 as a database will end in February 2024.
* Support for CA Siteminder filters will end in August 2024.
* Support for Oracle Access Manager filters will end in August 2024.
* Support for RSA Cleartrust (RSA Access Manager) will end in August 2024.
* Support for customer created images for container deployment, by way of EMT scripts, will end in August 2024 (use of Axway created images only will be supported).

## Removed features

The following features have been removed from the product in this update:

* Support for MySQL 5.7 as a relational database to store metrics data.

## Fixed issues

This version of API Gateway and API Manager includes:

* Fixes from all 7.7 updates released prior to this version. For details of all the update fixes included, see the corresponding [Release note](/docs/apim_relnotes/) for each 7.7 update.
* Additional fixes might be delivered as patches up to 15 months after the release date. You can find the list of patches available on top of this update on [Axway Support](https://support.axway.com/en/search/index/type/Downloads/q/20230830/ipp/100/product/324/product/464/version/3034/version/3035/subtype/8). If no patches were created for this release, the link will return "No search results found".

### Fixed security vulnerabilities

| Internal ID | Case ID                                | Cve Identifier | Description          |
| ----------- | -------------------------------------- | -------------- | ---------------------- |
|RDAPI-29564|01461632||**Issue**: OpenSSL 3.0.8 has multiple CVE's published (CVE-2023-0464 CVE-2023-0465 CVE-2023-0466). **Resolution**: Updated to OpenSSL 3.0.9.|

### Other fixed issues

| Internal ID | Case ID                             | Description          |
| ----------- | ----------------------------------- | ----- |
|RDAPI-25696|01298806|**Issue**: Session objects in distributed caches were not getting updates in API Manager and OAuth sessions. **Resolution**: When Session objects are updated, they are put in the cache again to ensure propagation.|
|RDAPI-28288|01370575|**Issue**: In API Manager Oauth Authorizations page, the call to the backend results in a timeout from Cassandra because of the large number of records. **Resolution**: The user can now enter the Owner and select an application to search OAuth authorization records more efficiently.|
|RDAPI-28465|01405645|**Issue**: Security Vulnerability in Origin Header Handling for CORS. **Resolution**: CORS Origin header is now handled as a valid URL and matched against the configured allowed origins in the form of `scheme://host[:port]`, that is, optional `[path][?query][#fragment]` parts in the received Origin URL are stripped out for matching purposes. Also, a null origin is accepted, if configured.|
|RDAPI-29027|01424066|**Issue**: In API Manager updates older than February 23, after importing a generated API collection that contained an OAS3 back-end API with a query parameter that was configured with an 'object' schema definition, subsequent runtime requests would fail to forward such parameters to the back-end service due to a mismatch between what the API Manager runtime was expecting and what was presented in the client request. The root cause was the 'explode' flag on the parameter schema being incorrectly set by the third-party OAS3 parser when the API definition was originally imported. **Resolution**: API Manager runtime now correctly forwards such parameters to the back-end service. Note: The bug in the third-party OAS3 parser has been resolved, and the February 23 update of API Manager included this fixed version.|
|RDAPI-29242|01466481  01485438  01442501|**Issue**: It was not possible to filter by API in the Monitoring section of API Manager. **Resolution**: It is now possible to filter by API in the Monitoring section.|
|RDAPI-29406|01452345|**Issue**: `GET /api/portal/v1.4/users` was missing the 'authAttrs' field. **Resolution**: Users APIs are now returning the authenticated user attributes.|
|RDAPI-29507|01318293  01457621|**Issue**: The Generic Error filter does not work when there is no content body on the message whiteboard. **Resolution**: The Generic Error filter correctly generates the corresponding SOAP 1.2 fault response using default stylesheet when there is no content body on the message whiteboard.|
|RDAPI-29528|01456395  01478792|**Issue**: API Manager might fail to process and update cloned APIs that have pending virtualization on start up or loading a new configuration. **Resolution**: API Manager correctly processes and updates cloned APIs on start up or loading a new configuration.|
|RDAPI-29552|01460571|**Issue**: The OAS3 definitions for the API Manager REST APIs (that are shipped with the product) contain inconsistencies such that if those definitions are used as input to a code generator, the resulting code does not allow for the generation of requests that are indicative of the implementation of those APIs. Specifically, the VirtualizedAPI schema incorrectly contains a 'customProperties' field which implies that custom properties are defined using this field, whereas the implementation of the APIs using this model expect custom properties to be defined as peers of the fields in the model. **Resolution**: The OAS3 definitions for the API Manager REST APIs that are shipped with the product are now consistent such that if a code generator is employed, the resulting code is aligned with the implementation of the defined APIs. Specifically, the VirtualizedAPI, Application, Organization and User models now include an 'additionalProperties' field to facilitate correctly defining custom properties for those models.|
|RDAPI-29641|01442884|**Issue**: Client connection created by Connect To URL filter can be reused after it has been closed by the back-end server. This is generating a request failure with error "Disconnected while reading". **Resolution**: The client connection cache management is now checking for disconnection before a connection is reused.|
|RDAPI-29701|01467641|**Issue**: The HTTP Redirect filter is throwing "Unknown protocol" error when configured to send a deep link URI with a non-HTTP scheme. **Resolution**: The HTTP Redirect filter has been modified to accept URI with a non-HTTP scheme.|
|RDAPI-29761|01466005|**Issue**: In API Manager, when a runtime HTTP POST request with `Content-Type: application/json` and `Content-Length: 0` is sent to a virtualized API with an underlying definition containing an optional body parameter (required = false), an exception was being thrown by the parameter validator while attempting to parse the request body. **Resolution**: API Manager's body-parameter validation now caters for optional request bodies so that when a runtime HTTP POST request with `Content-Type: application/json` and `Content-Length: 0` is sent to a virtualized API with an underlying definition containing an optional body parameter (required = false), no exception is thrown.|
|RDAPI-29821|01459240|**Issue**: In the API Manager, the transaction audit message was not displayed when it contains some special characters. **Resolution**: When the transaction audit message contains special characters, they are properly escaped so that the message is always displayed.|
|RDAPI-29828|01472603|**Issue**: An exception was thrown during JSON redaction in case when JSON input data size was bigger than the fixed size of the buffer handling the reduction. **Resolution**: Elastic type of buffer is used in order to handle JSON redaction in accordance with the JSON input data size. The maximum size of the buffer is configurable in the 'JSONRedactor' tag in the redaction file (see "[Hide sensitive data](/docs/apim_administration/apigtw_admin/admin_redactors/)" page in API Gateway documentation).|
|RDAPI-29834|01468491|**Issue**: In API Manager a matching remote host is not used when importing a backend API via URL if the port is not specified in the request. **Resolution**: Now the default port for the presented URI scheme is used to match a remote host when importing a backend API via URL if the port is not specified in the request.|
|RDAPI-29838|01468491|**Issue**: The active timeout value was not pulling the correct value from the remote host settings for new connections used when importing an API. **Resolution**: Active timeout now works correctly for connections used when importing an API.|
|RDAPI-29843|01455521|**Issue**: Previously, it was possible to configure the parameter's names for Client credentials in OAuth filters, and it was possible to use mixed case when configuring the standard parameter names, which caused conflict with the OAuth RFC 6749. **Resolution**: It is no longer possible to use custom parameters to represent Client Credentials, and using mixed case will be rejected.|
|RDAPI-29852|01473853|**Issue**: The API Gateway `yamles` script converts XML Policy Shortcut filter references to an incorrect YAML relative path if the path to the filter contains duplicate naming and the target policy has the same name as the Policy Shortcut filter. **Resolution**: The API Gateway `yamles` script correctly converts XML Policy Shortcut filter references to a YAML absolute path if the path to the filter contains duplicate naming and the target policy has the same name as the Policy Shortcut filter.|
|RDAPI-29859|01474701|**Issue**: A circular dependency exception that can occur when merging configurations using Policy Studio does not give much information on the policy and filters to check. **Resolution**: Now the circular dependency exception error message is improved with policy and visited filter path information.|
|RDAPI-29916|01474906  01476593|**Issue**: The apigw-backup-tool keyspace restore command fails to reload indexes because the restored tables are not yet ready. **Resolution**: A retry mechanism was added to the reload index step of the script. By default, it retries 10 times at 1 second intervals. These numbers are configured in the tools config file.|
|RDAPI-29928|01476611|**Issue**: Within the metrics database, the 'TopologyID' field is too short to accommodate containerized topologies. **Resolution**: Increase the size of the 'TopologyID' field from 32 to 255.|
|RDAPI-29953|01472336|**Issue**: Deploying a project with an imported incomplete Web Service breaks other existing Web Services. **Resolution**: API Gateway reports incomplete Web Services and loads other Web Services as expected. The reported Web Services will not be able to process SOAP requests until they are fixed and redeployed without errors.|
|RDAPI-29954|01479031|**Issue**: API Manager UI API proxy PER-METHOD OVERRIDE with REQUIRED parameter is not working as expected. **Resolution**: The API Manager UI now disables the API proxy per-method override of a required parameter to optional if the backend API method defines the parameter as required. In the same way, the API Manager /proxies API prevents overriding such required parameters to optional. Also, if updating from a previous version, a report trace is written in the API Manager trace file if a required parameter, as defined in the backend API method definition, was overridden to optional. After importing the API collection the API Manager UI displays an error in the PER-METHOD OVERRIDE edit dialog for the overridden parameters to optional, allowing to restore them back to required.Be aware of that promoting APIs may fail if they contain a required backend method parameter overridden to optional as the frontend API update may fail with a 400 Bad Request error. In this case, the frontend API overridden method parameters must be restored to required and the API collection exported (if needed) for a successful API promotion.|
|RDAPI-29961|01477199|**Issue**: Payloads transferred using 'Content-Encoding' compression appear to be either missing or stored compressed and unredacted in Traffic Monitoring base. **Resolution**: Payloads compressed using 'Content-Encoding' are now uncompressed and redacted before being logged.|
|RDAPI-29967|01479300|**Issue**: Frontend and backend pages break when changing the page size right after the get /config API is called. **Resolution**: Fix the frontend code to check that the config store is refreshing before accessing the config parameters.|
|RDAPI-29996|01495256  01481118|**Issue**: Policy Studio Merge/Compare feature did not show the correct state of the difference tree and hence additions were merged unintentionally. **Resolution**: Policy Studio Merge/Compare now shows the correct state of the difference tree.|
|RDAPI-30012|01481286|**Issue**: XML documents containing UTF-8 BOM characters were failing to parse. **Resolution**: XML documents containing UTF-8 BOM characters now parse successfully.|
|RDAPI-30025|01399959|**Issue**: In some cases the cache filter reports wrongly a failure when adding an entry to the cache. **Resolution**: Now the cache filters only reports failure when an error is raised when adding a element to the cache.|
|RDAPI-30129|01485920|**Issue**: In Policy Studio with YAML-based configurations opening or creating a policy that contains XML Signature Generation filter causes NLS-related warning to be printed out in the console. **Resolution**: In Policy Studio with YAML-based configurations opening or creating a policy that contains XML Signature Generation filter does not cause NLS-related warning to be printed out in the console.|
|RDAPI-30162|01487997|**Issue**: Pagination bug in showing the list of applications in the API Access tab. **Resolution**: Set the pagination selected by the user after clicking on the refresh button.|
|RDAPI-30169|01488074|**Issue**: API Gateway SFTP client functions (filters BS pollers) could only support ssh-rsa signature scheme. **Resolution**: SFTP clients now support rsa-sha256 and rsa-sha512. The ssh-rsa scheme is disabled by default in both server and client. You can use the existing com.axway.apigw.sftp.knowninsecure.allow system property to enable it on the server side, and you can use the new `com.axway.apigw.sftp.client.knowninsecure.allow` system property to enable it on the client side.|
|RDAPI-30174|01487037|**Issue**: API Manager incorrectly matches API method with path segments as path parameters instead of API method with literal path segments when the `com.vordel.apimanager.uri.path.trailingSlash.preserve` Java system property is set to `true`. **Resolution**: API Manager always matches API methods with literal path segments over API method with path segments as path parameters.|
|RDAPI-30177|01486428|**Issue**: Add JSON Node filter fails when path is not found even when path components provide asterisks. **Resolution**: Add JSON Node filter succeeds without changing the JSON body.|
|RDAPI-30199|01487990|**Issue**: API Manager UI shows details of users or applications under the API Catalog page when API Manager is accessed through the approval e-mail or alert link e-mail repectively. **Resolution**: The API Manager UI now shows the users details under the Application Developers page and the applications details under the Applications page for Clients. |
|RDAPI-30230|01463488  01491960|**Issue**: The request params are not sent to the backend if it is application/x-www-form-urlencoded request. **Resolution**: Created the property com.axway.api.runtime.broker.contentType.formUrlEncoded.passthrough. If set to true, API Gateway will pass through all the request parameters to the backend of a Form URL encoded message with no or some parameters defined.|
|RDAPI-30250|01492256  01493257  01493319|**Issue**: Jackson library version 2.15.0, included with API Gateway, ships with a string processing limit of 5000000 bytes, which introduced errors on APIs that previously worked. **Resolution**: Jackson version is updated to version 2.15.2 that increases the string processing limit to 20000000 bytes.|

## Known issues

The following are known issues for this update.

### JSON Remove Node filter behavior change

The JSON Remove Node filter is not working as expected when parsing an already empty array.

If the filter uses a syntax like `$.apis[*]` to make an array empty but the array in the JSON fragment is already empty, the filter throws an exception. The only way to force the array to be empty is to provide the `$.apis` syntax, but that removes the whole field.

Related issue: RDAPI-29426

### Policy Studio Toolbar

Policy Studio perspective has an intermittent error with the toolbar failing to display on the UI. All operations provided by the toolbar can be executed using menu items.

Related issue: RDAPI-29504

### Data Maps cannot be created or configured in Policy Studio

Data maps cannot be created or configured in Policy Studio from May 2023 release because of a conflict with Eclipse RCP 4.9. The data maps created in previous releases will still work correctly on the API Gateway runtime environment. You can develop and export configurations in Policy Studio versions prior to May 2023, and deploy the configuration upgraded in this release.

Related issue: RDAPI-29504

### Policy Studio Graphic Dispose error

An error dialog notifying a Graphic Dispose error might be shown upon closing Policy Studio. If the error occurs, it will also be shown in the Policy Studio logs. This error has no impact.

Related issue: RDAPI-29504

### Changing Scripting filter engine in YAML projects

In Policy Studio YAML projects, when updating the scripting engine in the scripting filter, the old script is not deleted. Customers can manually delete the old script from the project with no impact.

Related issue: RDAPI-29887

### Scripting filter whiteboard attributes not preloaded for Jython scripts

The Scripting filter now uses a Jython 2.7 scripting environment (previously, Jython 2.5) to execute Jython scripts. As a result of this version change, the whiteboard attributes, such as `http.request.uri` and `http.request.verb`, are no longer preloaded for use by Jython scripts. However, you can run a Jython script to load these attributes before they are accessed as follows:

```none
from com.vordel.trace import Trace

def invoke(msg):
    msg.forceGenerateAttributes()
    Trace.info("This trace statement was generated in script filter!  [" + str(msg.get("http.request.verb")) + "] [" + str(msg.get("http.request.uri")) + "]")
    return True
```

Related Issue: RDAPI-21363

### API Manager runtime does not perform full parameter validation for compound schemas

In API Manager, when you import an OAS definition, which contains a `PathItem` with a content-type of `application/x-www-form-urlencoded` or `multipart/form-data`, and the `content` contains a compound schema of `anyOf` or `oneOf`, the internal model ends up with a list of optional form parameters without a link to the original schema definition. As a result, the API Manager runtime cannot correctly validate inbound requests to the API methods in question because the schema information is missing. This has the potential side-effect of incorrectly allowing certain combinations of form parameters to be sent to the backend service. This is best illustrated with an example.

Consider the following `PathItem` definition:

```json
   "/form-anyOf": {
      "post": {
        "requestBody": {
          "content": {
            "application/x-www-form-urlencoded": {
              "schema": {
                "$ref": "#/components/schemas/TestAnyOf"
              }
            }
          }
        },
        "responses": {
          "200": {
            "description": "OK"
          }
        }
      }
    }
  },
```

where the `TestAnyOf` schema is defined as:

```json
      "TestAnyOf": {
        "anyOf": [
          {
            "$ref": "#/components/schemas/Cat"
          },
          {
            "$ref": "#/components/schemas/Dog"
          }
        ]
      },
      "Cat": {
        "additionalProperties": false,
        "type": "object",
        "required": [
          "name"
        ],
        "properties": {
          "name": {
            "type": "string",
            "enum": ["cat","felix"]
          }
        }
      },
      "Dog": {
        "additionalProperties": false,
        "type": "object",
        "required": [
          "age"
        ],
        "properties": {
          "age": {
            "type": "integer"
          },
          "time": {
            "type": "string",
            "format": "date-time"
          }
        }
      }
```

This results in the optional form parameters `name`, `age`, and `time` being generated and stored as part of the internal model for the API method. If the following body is sent as part of a runtime request, parameter validation will succeed and all parameters will be forwarded to the backend service, despite the underlying schema indicating that the request body must contain `anyOf` 'Cat' or 'Dog', but not both.

```json
{code}
name=felix&age=5&time=2022-12-31T22:59:01Z
{code}
```

Related Issue: RDAPI-29098

### API Manager quota functionality does not work with MySQL 8

If you are using a MySQL database for API Manager quotas, you must use MySQL 5.7, otherwise a SQLException will be raised.

Related Issue: RDAPI-29436

### URLLIB2 issues in Jython 2.7.3

Following the upgrade to Jython 2.7.3, the following issue has been observed for some `urllib2` based requests to a server - "TypeError: cannot make memory view because object does not have the buffer interface". This does not affect all requests, and it has been documented externally as a known issue with versions above Jython 2.7.2. For custom Jython scripts, a potential workaround is to use the `urllib3` library instead.

Related Issue: RDAPI-29910

### Visual Mapper exception

During the first transformation performed by the Visual Mapper filter runtime there is an exception with a message "Could not register default TransformerFactory". This will not impact the transformation performed and can be ignored.

Related Issue: RDAPI-30439

### Cassandra uses a deprecated garbage collector

Following the upgrade to Java 11, the following warning message has been observed on startup of Cassandra, "VM warning: Option UseConcMarkSweepGC was deprecated in version 9.0 and will likely be removed in a future release".

Changing the garbage collector (GC) to `G1GC` solves the issue, the warning is not shown anymore. In addition, resource utilization might be improved in certain circumstances.

To change the GC, you must modify the `jvm11-server.options` file located in the `conf` directory inside the root directory of the Cassandra install.

Disable the CMS GC by removing or commenting out the following:

```
### CMS Settings
#-XX:+UseConcMarkSweepGC
#-XX:+CMSParallelRemarkEnabled
#-XX:SurvivorRatio=8
#-XX:MaxTenuringThreshold=1
#-XX:CMSInitiatingOccupancyFraction=75
#-XX:+UseCMSInitiatingOccupancyOnly
#-XX:CMSWaitDuration=10000
#-XX:+CMSParallelInitialMarkEnabled
#-XX:+CMSEdenChunksRecordAlways
## some JVMs will fill up their heap when accessed via JMX, see CASSANDRA-6541
#-XX:+CMSClassUnloadingEnabled
```

Enable the G1 GC by adding or uncommenting the following:

```
### G1 Settings
## Use the Hotspot garbage-first collector.
-XX:+UseG1GC
-XX:+ParallelRefProcEnabled
-XX:MaxTenuringThreshold=1
-XX:G1HeapRegionSize=16m

#
## Have the JVM do less remembered set work during STW, instead
## preferring concurrent GC. Reduces p99.9 latency.
-XX:G1RSetUpdatingPauseTimePercent=5
#
## Main G1GC tunable: lowering the pause target will lower throughput and vise versa.
## 200ms is the JVM default and lowest viable setting
## 1000ms increases throughput. Keep it smaller than the timeouts in cassandra.yaml.
-XX:MaxGCPauseMillis=300

## Optional G1 Settings
# Save CPU time on large (>= 16GB) heaps by delaying region scanning
# until the heap is 70% full. The default in Hotspot 8u40 is 40%.
-XX:InitiatingHeapOccupancyPercent=70
```

Related Issue: RDAPI-30339

## Documentation

To find all available documentation for this product's version:

1. Go to [Library, on the Axway Documentation portal](https://docs.axway.com/bundle).
2. From the left pane **Product** list, select **API Management**.

Customers with active support contracts must log in to access restricted content.

### Removal of Deprecated features section

As of this update, we have removed the **Deprecated features** section from our Release notes to simplify the communication of capabilities being removed from the solution.

There are now only two states - a capability is on notice for removal at some time in the future, or a capability has been removed from the solution.

## Supported platforms

For information on the different operating systems, databases, browsers, and thick client platforms supported by each Axway product, see [Supported Platforms](https://docs.axway.com/bundle/Axway_Products_SupportedPlatforms_allOS_en_HTML5/page/api_management.html).

## Support services

The Axway Global Support team provides worldwide 24 x 7 support for customers with active support agreements. Email [support@axway.com](mailto:support@axway.com) or visit [Axway Support](https://support.axway.com/).

See [Get help with API Gateway](/docs/apim_administration/apigtw_admin/trblshoot_get_help/) for the information that you should be prepared to provide when you contact Axway Support.
