---
title: API Gateway and API Manager 7.7 November 2023 Release Notes
linkTitle: API Gateway and API Manager November 2023
weight: 95
date: 2023-09-11
description: null
---
API Gateway and API Manager updates are cumulative, comprising new features and changes delivered in previous updates unless specifically indicated otherwise in the Release notes.

The key feature delivery for this update is the support of GraphQL, which has been the highest voted item on our [Ideas portal](https://amplifyideas.axway.com/?project=APIG&sort=popular) for some time. This support enables customers to import GraphQL specifications and apply security and policies, as well as view the specification in API Manager and try it out.

In this update we have also done further work on the Helm charts for container deployment, making them more flexible and enabling new features.

Support for newer base operating systems (OS) has also been delivered - RHEL 9, Oracle Linux 9, and SUSE 15 are now supported. For our Axway images used for container deployment, these have been upgraded to use Red Hat Universal Base Images (UBI) 9 as the base OS.

## Installation

* To **update** your API Gateway, see [Update from API Gateway One Version](/docs/apim_installation/apigw_upgrade/upgrade_steps_oneversion/).
* To **upgrade** from an older version, see [Upgrade from API Gateway 7.5.x or 7.6.x](/docs/apim_installation/apigw_upgrade/upgrade_steps_extcass/).
* For more details on supported platforms for software installation, see [System requirements](/docs/apim_installation/apigtw_install/system_requirements/).
* For a summary of the system requirements for a Docker deployment, see [Set up Docker environment](/docs/apim_installation/apigw_containers/deployment_flows/custom_image_deployment/docker_scripts_prereqs/).

### Update a container deployment

Any custom `.fed` files deployed to a container must be upgraded using [upgradeconfig](/docs/apim_installation/apigw_upgrade/upgrade_analytics#upgradeconfig-options) or [projupgrade](/docs/apim_reference/devopstools_ref#projupgrade-command-options). They must be upgraded the same way, regardless of whether they are API Manager enabled or not. The `.fed` files contain the updates for the API Manager configuration and can be used to build containers.

## New features and enhancements

The following new features and enhancements are available in this update.

### Support for GraphQL APIs

This update of API Gateway ships with support for GraphQL APIs. This enables customers to import GraphQL specifications, virtualize the registered back-end API exposing it as a publicly accessible front-end API, secure it, and manage its API lifecycle.

You can also view the specification in the API Manager Catalog, and utilize the Try-It functionality facilitated by the inbuilt [GraphQL IDE](https://github.com/graphql/graphiql).

For more information, see the [Administer API Manager](/docs/apim_administration/apimgr_admin/) guide.

As GraphQL is not a REST API, GraphQL back-end APIs cannot be exported in Swagger format.

GraphQL subscriptions are not currently supported.

Traceability and Discovery Agents updates to pick up GraphQL APIs will follow in a future release.

### AAOI Helm charts v5.7.0 are now available

With the release of the Amplify Analytics Operational Insights (AAOI) Helm charts version `5.7.0`, the version of the ELK images and charts installed has been upgraded from version `7` to version `8`; hence, there are some differences in the method of deployment of the AAOI chart.

These differences mostly affect the Helm `install` command, whereas all other instructions for installing and configuring AAOI Helm charts version `5.6.0` are also relevant for version `5.7.0`.

The main change with ELK `v8` is that Kibana cannot be installed as the same time as Elasticsearch, so it must be disabled for the initial Helm install run.

For more information, see [Configure a production setup for Helm](/docs/operational_insights/production_setup/op_insights_setup_prod_helm/).

This chart upgrade also affects API Portal as its port exposure methodology in the Helm chart has been updated. Customers with API Portal configured should review the configuration in this area.

## Important changes

It is important, especially when upgrading from an earlier version, to be aware of the following changes in the behavior or operation of the product in this update, which might impact your current installation.

### AAOI version compatibility

Amplify Analytics Operational Insights (AAOI) has been updated to version `5.7.0` to improve performance by utilizing a new endpoint. This change is not backwards compatible with older API Management (APIM) releases. Customers will need to upgrade APIM before upgrading to AAOI new version. APIM remains backwards compatible with older version of AAOI.

### Enum validation update

API Manager has been updated to correctly validate a parameter of type string with an enumeration if Allow Multiple is enabled. Clients may have existing items that are now correctly blocked by this change. Clients will have to turn off validation if this occurs until calls are correctly updated.

### PGP Integrity Checks

The option to add an integrity check to encrypted Pretty Good Privacy (PGP) messages has been removed in this release. Integrity checks are not supported by the PGP key type currently available in API Gateway.

### API Key Headers in responses

API Key headers attached by API Gateway processes to call back-end services are no longer returned in responses to the caller.

### OAuth Client State Cache settings

Previously, the Cache settings in Policy Studio **OAuth Client State Cache** option were ignored at runtime. This is fixed, and the cache settings are now properly configured.

After upgrading, if the **OAuth Client State Cache** had default settings prior to the upgrade, the `Eternal` flag will now be unset, and default values will be set for TTL and TTI. Therefore, you must review these settings to ensure they match expectations.

## End of support notices

<!-- There are no end of support notices in this update. -->

To stay current and align our offerings with customer demand and best practices, Axway might discontinue support for some capabilities.

As part of this update, the following features have notice for removal.

### Operating systems and hardware

Support for CentOS 5, CentOS 6, Suse 11, and Suse 12 ended in November 2023.

### KPS database storage

Support for IBM DB2 version 10.5 as a database will end in February 2024.

### Client Application Registry for Oauth servers

Support for Client Application Registry (CAR) will end in February 2024. The API Gateway can still act as an OAuth server without CAR, but API Manager will be required when CAR-only mode is no longer available.

### CA SiteMinder filters

Support for [CA SiteMinder](/docs/apim_policydev/apigw_polref/ca_siteminder/) filters will end in August 2024.

### Oracle Access Manager filters

Support for [Oracle Access Manager](/docs/apim_policydev/apigw_polref/connector_oam/) filters will end in August 2024.

### Access Manager filter

Support for RSA Cleartrust ([RSA Access Manager](/docs/apigtw_auth_auth/rsa_access_mgr_overview/)) will end in August 2024.

### Images for container deployment

Support for customer created images for container deployment, by way of EMT scripts, will end in August 2024. The use of Axway created images only will be supported.

## Removed features

No features have been removed in this update.

## Fixed issues

This version of API Gateway and API Manager includes:

* Fixes from all 7.7 updates released prior to this version. For details of all the update fixes included, see the corresponding [Release note](/docs/apim_relnotes/) for each 7.7 update.
* Additional fixes might be delivered as patches up to 15 months after the release date. You can find the list of patches available on top of this update on [Axway Support](https://support.axway.com/en/search/index/type/Downloads/q/20231130/ipp/100/product/324/product/464/version/3034/version/3035/subtype/8). If no patches were created for this release, the link will return "No search results found".

### Fixed security vulnerabilities

| Internal ID | Case ID                                | Cve Identifier | Description                                                                                                                                                                                                                                                                                     |
| ----------- | -------------------------------------- | -------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|RDAPI-30831|01524981||**Issue**: Empty XML SOAP requests in API Manager cause segmentation faults when the encoding is set to UTF-8 or UTF-16, and it is posted to WSDL-based APIs. **Resolution**: Empty XML bodies no longer cause a segmentation fault.|
|RDAPI-30898|01527463|CVE-2023-5363|**Issue**: CVE-2023-5363 in OpenSSL 3.0.11. **Resolution**: OpenSSL has been updated to 3.0.12.|
|RDAPI-30916|01529928  01529431  01529474  01531258  01529948  01530540|CVE-2023-46604|**Issue**: API Gateway has potential activeMQ vulnerabilities due to CVE-2023-46604. **Resolution**: The activeMQ module has been updated to version 5.17.6 to address any potential vulnerabilities in API Gateway from CVE-2023-46604.|

### Other fixed issues

| Internal ID | Case ID                             | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| ----------- | ----------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|RDAPI-28747|01495104  01514834  01471833|**Issue**: The API Manager GET /api/portal/v1.4/users endpoint is inefficient and makes a number of unnecessary queries to Cassandra. **Resolution**: This endpoint is now more efficient and the number of queries to Cassandra have been reduced significantly. |
|RDAPI-29426|01510226  01504028  01491523|**Issue**: The Remove JSON Node filter fails when the path expression is not found, even when the path component provides asterisks to search for an array. **Resolution**: The Remove JSON Node filter now succeeds when the path is not found.|
|RDAPI-29974|01479062|**Issue**: API Manager does not validate a parameter of type string with an enumeration if "Allow Multiple" is enabled for the parameter. **Resolution**: Now, API Manager validates a parameter of type string with an enumeration if "Allow Multiple" is enabled for the parameter.|
|RDAPI-30116|01485414|**Issue**: API Gateway process memory can grow when the response processing of threat protection is enabled in Policy Studio. **Resolution**: A memory leak has been corrected in the threat protection stream management.|
|RDAPI-30145|01481751|**Issue**: SSO login page can get stuck and result in a freeze. **Resolution**: A race condition that could lead to a deadlock has been corrected in the commons-sso dependency.|
|RDAPI-30211|01478844|**Issue**: Backend API Keys were visible to clients calling the frontend API when configured on the header. **Resolution**: Backend API Keys set in the header will no longer be returned to the caller.|
|RDAPI-30352|01497951|**Issue**: There is a potential race condition when a thread is in the process of reading all the organizations, it also saves the organization back to the data store using its DN as the key. If another thread updated an Organization's name at the same time, it could cause the first thread to attempt to update an organization using a DN that no longer exists. **Resolution**: This additional update to an organization during retrieval of all organizations, using the organization's DN, is not required and has been removed.|
|RDAPI-30366|01498485|**Issue**: Impossible to environmentalize input and output encodings with values 'gzip' and 'deflate'. **Resolution**: Envorinmentalization has been enhanced to accept multiple string values that will fully override the attribute. UI is now displaying the same encodings' selection widget inside the Environment Settings section.|
|RDAPI-30467|01500846|**Issue**: Cannot download message content in a queue from the API Gateway Manager. **Resolution**: Message content in a queue can now be downloaded.|
|RDAPI-30509|01503673|**Issue**: User was not able to generate a certificate using a domain ID that contained a dot. **Resolution**: User can now generate certificates with any characters in their domain ID.|
|RDAPI-30555|01508507|**Issue**: API Manager incorrectly identified certain array parameters as bad data. **Resolution**: API Manager no longer reports supported array types incorrectly.|
|RDAPI-30598|01511677|**Issue**: The refresh button on the Frontend API page of API Manager greys out after several clicks. **Resolution**: The refresh button now continues to work as expected.|
|RDAPI-30601|01511713  01513326  01521091|**Issue**: KPS Admin fails when querying data with numbers of length greater than '1000', which was introduced with Jackson version 2.15.0. **Resolution**: The default number length limit is increased to ‘1500‘ which is appropriate for RSA-4096 numbers. A new ‘com.axway.kpsadmin.maxNumberLength’ system property has been added which can be configured in the jython.xml.|
|RDAPI-30608|01503863|**Issue**: APIBuilder4Elastic was not considering the payload folders with a name format like HH.MI-N. **Resolution**: Fixed the regex expression in the node function in APIBuilder4Elastic.|
|RDAPI-30627|01512985|**Issue**: API Manager Proxy API requests may cause high CPU consumption under load. **Resolution**: The processing of API Manager Proxy API requests has been improved by reducing memory footprint and better performance when generating JSON payloads for Proxy API responses.|
|RDAPI-30660|01515844|**Issue**: The API Manager GET /api/portal/v1.4/applications endpoint is inefficient and makes a number of unnecessary queries to Cassandra. **Resolution**: This endpoint is now more efficient and the number of queries to Cassandra have been reduced significantly. |
|RDAPI-30684|01512659|**Issue**: "Web Service Repository is not found" error in Policy Studio after upgrade to August 2023 release. **Resolution**: The error is no longer displayed in Policy Studio or Configuration Studio.|
|RDAPI-30694|01508129|**Issue**: Filter 'Insert MTOM Attachment'  does not correctly Base64 encode binary data. **Resolution**: The missing Base64 padding at the end of encoded data has been restored.|
|RDAPI-30700|01516063|**Issue**: Previously, the Cache settings in OAuth Client State Cache were ignored. **Resolution**: Cache settings are used for state cache. Upgrade will automatically update cache settings if they match the original default settings.|
|RDAPI-30714|01515997|**Issue**: Inadequate error message to misconfigured system property "com.vordel.dwe.auto204response=False". **Resolution**: An error trace is now written with the system property settings.|
|RDAPI-30757|01514246|**Issue**: Links in readme.md and values.yaml were outdated. **Resolution**:  Links in readme.md and values.yaml were updated.|
|RDAPI-30764|01519431|**Issue**: The entity store passphrase was incorrectly added into the APIM helm chart. **Resolution**: This configuration has been removed.|
|RDAPI-30832|01525195|**Issue**: Slow performance from AAOI lookup API call, due to an expensive Cassandra call **Resolution**: Create new API that is more optimized for the kind of calls needed by AAOI|
|RDAPI-30902|01525517|**Issue**: The "JSON Path" Filter is failing to parse complex path expressions due to a missing json-smart jar dependency. **Resolution**: Included the json-smart jar version 2.5.0 as a runtime dependency of json-path. This change allows the "JSON Path" Filter to successfully parse complex path expressions.|
|RDAPI-30904|01506867|**Issue**: Importing a WSDL API made duplicate APIs leading to usability issues. **Resolution**: The duplicated WSDL backend API name can now be updated to easily identify every API.|
|RDAPI-30933|01530071|**Issue**: Content-Type header values were checked with case sensitivity. **Resolution**: Content-Type header values are now check without case sensitivity.|
|RDAPI-30943|01528585|**Issue**: Possible core dump when HSM layer is performing RSA decryption using OAEP padding. **Resolution**: Not having MGF1 algorithm explicitly defined is now supported.|
|RDAPI-30965|01527679|**Issue**: The HTTP Status Code filter is causing NPE when configured selector cannot be resolved. **Resolution**: The HTTP Status Code filter is now aborting with no NPE.|

## Known issues

The following are known issues for this update.

### Policy Studio Help window only opening for a single item

When using the Help button on a dialog box in Policy Studio, for example, from a policy filter, the in-built Help window is correctly opened in the browser, but, if you try to open the help page for a subsequent item from a dialog box, the new help window will not open. The only way to open a window for a new item is by closing the current window.

As a workaround, to avoid having to close the first window which was opened, you can navigate the documentation in the already opened Help web page.

This issue is not present when clicking the Help buttom from the main navigational window.

Related issue: RDAPI-31067

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

### Supported characters for filtering Applications in API Manager

Characters which are not included in the ISO-8859-1 standard (for example, Chinese characters) are not supported for use in API Manager when filtering Applications.

Related Issue: RDAPI-24001

## Documentation

To find all available documentation for this product's version:

1. Go to [Library, on the Axway Documentation portal](https://docs.axway.com/bundle).
2. From the left pane **Product** list, select **API Management**.

Customers with active support contracts must log in to access restricted content.

## Supported platforms

For information on the different operating systems, databases, browsers, and thick client platforms supported by each Axway product, see [Supported Platforms](https://docs.axway.com/bundle/Axway_Products_SupportedPlatforms_allOS_en_HTML5/page/api_management.html).

## Support services

The Axway Global Support team provides worldwide 24 x 7 support for customers with active support agreements. Email [support@axway.com](mailto:support@axway.com) or visit [Axway Support](https://support.axway.com/).

See [Get help with API Gateway](/docs/apim_administration/apigtw_admin/trblshoot_get_help/) for the information that you should be prepared to provide when you contact Axway Support.
