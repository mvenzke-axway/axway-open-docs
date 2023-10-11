---
title: API Gateway and API Manager 7.7 May 2023 Release Notes
linkTitle: API Gateway and API Manager May 2023
weight: 95
date: 2023-05-25
description: null
---
API Gateway and API Manager updates are cumulative, comprising new features and changes delivered in previous updates unless specifically indicated otherwise in the Release notes.

In this update, we are shipping the upgrade of the Eclipse Rich Client Platform (RCP) for Policy Studio, from versions 3.8.2 to 4.9. Although this improvement will be mostly transparent for customers, it will bring the associated libraries more up to date and, therefore, make the solution more secure.

In addition, we have made significant changes to password management for API manager, and followed on changes to API Portal to comply with these improvements.

This update also solves an issue with generating PDFs for API Analytics reports. For more information, see [Important changes](#important-changes) section.

It should be noted that the upgrade of Java has been moved from the May 2023 update to August 2023. This helps to de-risk the updates by separating two major upgrades - Java and RCP.

## Installation

* To **update** your API Gateway, see [Update from API Gateway One Version](/docs/apim_installation/apigw_upgrade/upgrade_steps_oneversion/).
* To **upgrade** from an older version, see [Upgrade from API Gateway 7.5.x or 7.6.x](/docs/apim_installation/apigw_upgrade/upgrade_steps_extcass/).
* For more details on supported platforms for software installation, see [System requirements](/docs/apim_installation/apigtw_install/system_requirements/).
* For a summary of the system requirements for a Docker deployment, see [Set up Docker environment](/docs/apim_installation/apigw_containers/docker_scripts_prereqs/).

### Update a container deployment

Any custom `.fed` files deployed to a container must be upgraded using [upgradeconfig](/docs/apim_installation/apigw_upgrade/upgrade_analytics#upgradeconfig-options) or [projupgrade](/docs/apim_reference/devopstools_ref#projupgrade-command-options). They must be upgraded the same way, regardless of whether they are API Manager enabled or not. The `.fed` files contain the updates for the API Manager configuration and can be used to build containers.

## New features and enhancements

The following new features and enhancements are available in this update.

### Policy Studio RCP Upgrade

Policy Studio base Eclipse Rich Client Platform (RCP) have been upgraded from version 3.8 to 4.9. User functionality should remain the same with minor changes to visual assets to improve user experience.

### Set password history length

A password history length can now be set in API Manager. Once set, the password history will be used to ensure new passwords are not duplicates of old passwords. For more information, see [Password, login, and session management settings](/docs/apim_reference/api_mgmt_config_web/#password-login-and-session-management-settings).

### Temporary passwords and user activation email link expiry

In API Manager, emails containing either a reset password link, user activation link, or temporary password can now be set to expire after a configured amount of time. For more information, see [Password, login, and session management settings](/docs/apim_reference/api_mgmt_config_web/#password-login-and-session-management-settings).

### Support for Cassandra 4.0.9

API Gateway now supports Cassandra 4.0.9. This version of Cassandra is no longer vulnerable to CVE-2022-1471 (CVSS Score 9.8); therefore, we recommend you to upgrade to this version. For more information, see [Upgrade Apache Cassandra to 4.0.9](/docs/apim_installation/apigw_upgrade/upgrade_cassandra/upgrade_cassandra_v4).

## Important changes

It is important, especially when upgrading from an earlier version, to be aware of the following changes in the behavior or operation of the product in this update, which may impact your current installation.

### Policy Studio upgrade

Policy Studio has undergone a major upgrade; therefore, a service pack will not be provided for this release.

{{< alert title="Note" color="primary" >}}You must perform a full installation of Policy Studio application to avail of its updates.{{< /alert  >}}

Service packs will be provided on top of the Policy Studio May 2023 Release going forward.

### API Gateway Analytics PDF reports

Since the [May 2022](/docs/apim_relnotes/20220530_apimgr_relnotes/) update, the API Gateway Analytics PDF reports no longer included charts. This was due to an upgrade of our `Highcharts.js` library and the compatibility with our PDF generation tool. The PDF generation has now been fixed in API Gateway Analytics to include charts.

### Operating system information removed from the config API

The operating system information has been removed from the `/config` API in API Manager. This change has been made to ensure no malicious users can identify what platform the API Manager is running on.

### Traffic monitor in AAOI visibility when AAOI builder component is down

A new environment variable (`AAOI_RAISE_CONNECT_ERROR`) has been introduced to no longer allow users to see data from OpsDB when the Amplify Analytics Operational Insights (AAOI) builder component is down. As RBAC controls can only be used when the AAOI builder component is available, this change ensures users only see transactions they are allowed to see at all times.

<!-- RDAPI-28755 -->

### Quota restrictions period configuration updated in API Manager Quotas API

Since November 2021 release, the [Quota time windows](/docs/apim_administration/apimgr_admin/api_mgmt_admin#quota-time-windows), in quota rules, must be one of the following:

* `second`
* `minute`
* `hour`
* `day`
* `week`

Note that the previously documented plural form (for example, `seconds`) is no longer valid.

### Data Maps cannot be updated

Data Maps cannot be configured or updated in Policy studio in this release. For more information, see [Known issues](#data-maps-cannot-be-created-or-configured-in-policy-studio) section.

### Update your custom Helm chart values file

There are two use cases where you must now update your custom Helm chart values file:

* **When using custom certificates**

    If you are using custom certificates, you must now include the `domainId` used in the creation of your custom certificates in your Helm chart values file. For more information, see [Add a customized domain certificate configuration](/docs/apim_installation/apigw_containers/deployment_flows/axway_image_deployment/helm_deployment#add-a-customized-domain-certificate-configuration).

* **To accept general conditions**

    The Axway Helm chart now requires the `values` file to explicitly accept the Axway general terms and conditions. For more information, see [Accept general terms and conditions](/docs/apim_installation/apigw_containers/deployment_flows/axway_image_deployment/helm_deployment##acceptance-of-general-conditions-for-license-and-subscription-services).

### Removal of ambiguity in the setting of service ports in API Gateway Helm chart

The Axway API Gateway Helm chart has been updated to remove any ambiguity around setting of service ports. To avail of this enhancement, in your `custom` values file, ensure that any overwrites to service ports match exactly the layout in the [default `values`](/docs/apim_installation/apigw_containers/deployment_flows/axway_image_deployment/helm_deployment/#fetch-the-helm-chart-to-examine-the-values-file) file.

For example:

```yaml
apimgr:
  service:
    type: ClusterIP
    ports:
      ui:
        port: 8075
        protocol: TCP
```

### Apache XalanJ is reinstated

Apache has released an updated version of XalanJ (version 2.7.3) to address CVE-2022-34169. This version is shipped with this current update of API Gateway, which allows you to use the `org.apache.xalan.xsltc.trax.TransformerFactoryImpl` property as the transformer factory in filters, scripts, and jvm.xml.

## Deprecated features

<!--  No features have been deprecated in this update. -->

As part of our software development life cycle, we constantly review our API Management offering. As part of this update, please see notice of deprecation of the following capabilities.

### Client Application Registry

This is notice that we will remove support for use of the Client Application Registry (CAR) from February 2024.

The API Gateway can still act as an OAuth server without CAR, but API Manager will be required when CAR-only mode is no longer available.

## End of support notices

To stay current and align our offerings with customer demand and best practices, Axway might discontinue support for some capabilities. As part of this update, the following features have been removed:

<!-- There are no end of support notices in this update. -->

### Sun Access Manager has been retired

Sun Access Manager has been retired in this release. This includes the following Sun Access Manager filters:

* Authorization
* Log out session
* Retrieve attributes
* SSO Token Validation
* X.509 Certificate Authentication

These filters will no longer be available to use when creating policies in Policy Studio, so we strongly recommend that you replace them with the [Oracle Access Manager](/docs/apim_policydev/apigw_polref/connector_oam/) filters, or remove them entirely.

This retirement also includes the following:

* The **Access Manager** settings, found in **Server Settings > Security**.
* The **Sun Access Manager** Repositories, found in **External Connections > Authentication Repositories**. Sun Access Manager repositories can be replaced by Oracle Access Manager repositories.

Existing configurations that use these filters and settings will cause a critical warning when upgraded in Policy Studio or with an upgrade tool such as `projupgrade`.

After this repository is removed, you will still be able to deploy upgraded configuration containing Sun Access Manager; and, updated gateways will continue to run existing deployed configuration, but as a consequence, a warning will be logged in API Gateway trace files and the deploy will always return a fail response.

## Removed features

To stay current and align our offerings with customer demand and best practices, Axway might discontinue support for some capabilities. As part of this update, the following features have been removed:

### Axway PassPort

The capability to use the Axway PassPort filter to connect to Axway PassPort product has been removed from API Gateway as of May 2023.

## Fixed issues

This version of API Gateway and API Manager includes:

* Fixes from all 7.7 updates released prior to this version. For details of all the update fixes included, see the corresponding [Release note](/docs/apim_relnotes/) for each 7.7 update.
* Additional fixes might be delivered as patches up to 15 months after the release date. You can find the list of patches available on top of this update on [Axway Support](https://support.axway.com/en/search/index/type/Downloads/q/20230530/ipp/100/product/324/product/464/version/3034/version/3035/subtype/8). If no patches were created for this release, the link will return "No search results found".

### Fixed security vulnerabilities

| Internal ID | Case ID                                | Cve Identifier | Description                                                                                                                                                                                                                                                                                     |
| ----------- | -------------------------------------- | -------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|RDAPI-29048|01436106|CWE-256|**Issue**: Plain text passwords are visible in the apigw-backup-tool.ini. **Resolution**: Permissions on the apigw-backup-tool.ini file are now changed to 600, which means the owner has full read and write access to the file, while no other user can access the file. In addition to these new file permissions, the user will be prompted for the 'nodetool' and 'cqlsh' usernames and passwords, if they are not present in the apigw-backup-tool.ini file. **Note**: apigw-backup-tool.ini file will be overwritten on update of API Gateway. When restoring a previously backed up file, it is recommended that the restored file should have its permissions set to 600.|
|RDAPI-29301|01449788||**Issue**: A malicious link could be entered as an application name, which will then show as a clickable link in the monitoring view. **Resolution**: Application name entries are now escaped, so clickable links are no longer displayed.|
|RDAPI-29419|01449457||**Issue**: API Manager REST API /config response contains OS and architecture type which is seen as a security risk. **Resolution**: The architecture and OS fields have been removed from the API Manager /config response.|
|RDAPI-29488|01458783  01457665|CVE-2022-1471|**Issue**: API Gateway snakeyaml version 1.32 is vulnerable to CVE-2022-1471.  **Resolution**: API Gateway snakeyaml version is upgraded to version 2.0, which is not vulnerable to CVE-2022-1471.|
|RDAPI-29569||CVE-2021-23450|**Issue**: API Gateway dojo version 1.16.5 is vulnerable to CVE-2021-23450. **Resolution**: API Gateway dojo version is upgraded to version 1.17.3, which is not vulnerable to CVE-2021-23450.|
|RDAPI-29644||CVE-2023-1436|**Issue**: API Gateway jettison version 1.5.3 is vulnerable to CVE-2023-1436. **Resolution**: API Gateway jettison version is upgraded to version 1.5.4, which is not vulnerable to CVE-2023-1436.|
|RDAPI-29645||CVE-2023-1370|**Issue**: API Gateway json-smart version 2.1.1 is vulnerable to CVE-2023-1370. **Resolution**: API Gateway json-smart version is upgraded to version 2.4.10, which is not vulnerable to CVE-2023-1370.|
|RDAPI-29686|01467043|CVE-2023-29199 CVE-2023-29017 CVE-2022-25881 CVE-2023-0464|**Issue**: Multiple vulnerabilities found in apibuilder4elastic image.  **Resolution**: Updated vulnerable libraries and Alpine base OS to fix the vulnerabilities.|

### Other fixed issues

| Internal ID | Case ID                             | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| ----------- | ----------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|RDAPI-26973|01384668  01346285|**Issue**:  Importing Swagger APIs from an URL fails if the Swagger document uses relative path references. **Resolution**: Importing Swagger APIs that use relative path references can now be successfully imported using a URL.|
|RDAPI-27614|01367897|**Issue**: Upgrade to Highcharts 10 caused a front-end error in the PDF generator. **Resolution**: Webapp was updated to avoid the front-end error in the PDF generator.|
|RDAPI-27817|01415117  01382549|**Issue**: Posting a well-formed 4Mb JSON request payload to a ModSecurity-enabled endpoint on the API Gateway resulted in ModSecurity reporting an 'invalid string in json text.\x0a' error, with the API Gateway subsequently returning an Operation blocked response back to the client. **Resolution**: Now, clients can successfully post well-formed 4Mb request payloads to a ModSecurity-enabled endpoint on the API Gateway, with ModSecurity processing the requests without error.|
|RDAPI-28177|01394878|**Issue**: Prior to RDAPI-28177, all characters but a-zA-Z0-9 were removed, resulting in creating empty file names and triggering an error afterward at generation time as the back-end assumed a string existed before ".pdf" or ".csv". **Resolution**: Now, we support all characters, except for a set of reserved characters, on top of '/', which will be replaced with '_' if encountered. Those chars would generate errors once the file was transferred to Windows.|
|RDAPI-28864|01417138|**Issue**: In Policy Studio, comparing complex API Gateway configurations takes a considerable time, up to 45 minutes on Windows systems. **Resolution**: Comparison algorithm has been upgraded, and now comparing complex API Gateway configurations takes around 45 seconds on Windows systems.|
|RDAPI-28867|01413432|**Issue**: PassphraseExec script was not executed when managing externally signed certs through managedomain. **Resolution**: PassphraseExec script is executed when managing externally signed certs through managedomain.|
|RDAPI-28957|01410422|**Issue**: In Policy Studio, the 'View' button shows the selected item from the previous tab when switching between the tabs under Certificates > Certificates with Keys, Certificates and CA. **Resolution**: The View, Edit, and Remove buttons are enabled if something is selected in the current tab, and they also open the current selection.|
|RDAPI-29034|01435308|**Issue**: The front-end/back-end list of APIs would be stuck loading after refreshing if the user had switched organization more than once. **Resolution**: API lists can now be refreshed correctly regardless of organization switching.|
|RDAPI-29036|01423374|**Issue**: If Slot IDs change in an HSM device, they will not match the expected Slot ID in API Gateway. **Resolution**: Labels and Description are added to the search criteria for identifying an HSM Slot. If no labels or descriptions are available, the existing mechanism of using a stored Slot ID is used.|
|RDAPI-29081|01418365|**Issue**: Threat Protection filter in AAOI traffic doe not filer transactions properly when option 'Block' is chosen. **Resolution**: Threat Protection filter in AAOI traffic is now filtering the transactions according to the chosen option ('Block'/'Pass').|
|RDAPI-29099|01438294|**Issue**: Users are able to view all transactions when the API Builder container is down or stopped. **Resolution**: Change the "Use Elasticsearch API" policy to return a server error with an appropriate message when API Builder is down and AAOI_RAISE_CONNECT_ERROR environment variable is set to true.|
|RDAPI-29195|01444502|**Issue**: In Policy Studio, sub-folders are created for some filters (for example, Scripting Language Filter). If the filter or the policy containing the filter is deleted or renamed, the original sub-folders and content still exist. **Resolution**: When the filter or the policy containing the filter is deleted or renamed, the original sub-folders and content are deleted.|
|RDAPI-29275|01448630|**Issue**: Previously, Policy Studio contained a setting on Advanced SSL tab of HTTPS Listeners that showed a label error, "NLS Missing Message:…. ". This property was not relevant to HTTPS Listeners. **Resolution**: This option has been removed from HTTPS Listeners.Note: Policy Studio contains a setting on Advanced SSL tab of HTTPS Listeners that shows a label error, "NLS Missing Message:…". This is a UI artifact and does not configure any renegotiation settings for listeners. This option is only relevant for Connection filters, where the option is properly labelled as Enable unsafe legacy renegotiation.|
|RDAPI-29290|01448959|**Issue**: There is no visual hint that the password field is required in Export API dialog. If the user hits OK and the password field is empty, nothing happens and that leads to confusion. **Resolution**: When the user hits OK when password field is empty, password field turns red indicating that password is required. |
|RDAPI-29367|01455514|**Issue**: Admin Node Manager unable to connect to metrics DB after upgrading to use Axway public images. **Resolution**: If using a custom domain cert please enter the domain id in the global values section of the values file of the helm chart (available from May 2023).|
|RDAPI-29415|01450472|**Issue**: The 'params.form' attribute is not expanded, that is, 'params.form.parameter_name' is invalid. **Resolution**: Attributes 'params.form', 'params.header', 'params.path' and 'params.query' are now correctly expanded as in August 2022 release. Attributes 'params.cookie' are now expanded as well.|
|RDAPI-29416|01423374|**Issue**: It is not possible to delete certificate realms, forcing user to edit the JSON configuration file manually to do so. **Resolution**: The 'keystoreadmin' command line has been enhanced with an option to delete a certificate realm.|
|RDAPI-29432|01459059|**Issue**: Node Manager and instances may abort request with an error showing "no throwable found". **Resolution**: Java exception handling has been improved to display the exception and to avoid the exception from being re-thrown. A NullPointerException has been corrected in Domain Audit (events 2200 and 2202). When incoming HTTPS connection fails domain audit events 2200 and 2202 are no longer logged after event 2403 (which already contains the detailed information on the failure).|
|RDAPI-29445|01454728|**Issue**: EMT scripts for building API Gateway images did not support older domain keys encrypted with DES. **Resolution**: Domain keys encrypted with DES are now re-encrypted using aes-256.|
|RDAPI-29454|01461245  01468261|**Issue**: As a result of failure of signature verification, an error is produced which is not handled properly. **Resolution**: When signature verification fails, the produced error is handled properly.|
|RDAPI-29480|01456606|**Issue**: Unknown MimeType not visible in API Gateway Manager transaction logs for API Manager backend import API. **Resolution**: The unknown MimeType traces are now displayed in the API Gateway Manager transaction logs.|
|RDAPI-29524|01459012|**Issue**: API Manager Current User REST API does not update the authenticated SSO user details correctly, e.g. the 'lastSeen' property value of the 'authAttrs' attributes. API Manager SSO session cookies incorrectly contain empty "Domain=" attribute. **Resolution**: API Manager Current User REST API updates the authenticated SSO user details in the 'authAttrs' attributes. API Manager SSO session cookies do not contain empty "Domain=" attribute as per RFC 6265.|
|RDAPI-29571|01460484|**Issue**: "SMTP Server settings" dialog in PS UI doesn't throw an error pop message when the connection type is TLS and credentials are not provided. **Resolution**: The error pop up dialog is shown now for TLS connection type when credentials are not provided (similar to the behavior when connection type SSL is chosen).|
|RDAPI-29588|01461783|**Issue**: When using API Gateway YAML configuration, policies using double quotes in their name are not shown in traffic monitor policy execution. **Resolution**: Now when using API Gateway YAML configuration, policies using double quotes in their name are shown in traffic monitor policy execution.|
|RDAPI-29596|01462725|**Issue**: In Policy Studio, the Upload to Amazon S3 filter Encryption key field did not allow a Certificate to be selected. **Resolution**: Now in Policy Studio, the Upload to Amazon S3 filter Encryption key field allows a Certificate to be selected.|
|RDAPI-29608|01454955|**Issue**: An empty page or blank content for some API Gateway Manager screens was displayed when logging in with a custom RBAC role user. **Resolution**: All pages in API Gateway Manager are displayed properly when logging in with a custom RBAC role user.|
|RDAPI-29612|01463488|**Issue**: Request body is not sent to the backend if it is application/x-www-form-urlencoded request. **Resolution**: Created the property com.axway.api.runtime.broker.contentType.formUrlEncoded.passthrough. If set to true, API Gateway will pass through the request body to the backend of a Form URL encoded message with no parameters defined.|
|RDAPI-29618|01463586|**Issue**: No message that export of API failed in API Manager browser page. **Resolution**: The export API error response from server is now displayed.|
|RDAPI-29655|01455514|**Issue**: No data sent for a period of time of 'activeTimeout' settings when sending back the HTTP response. **Resolution**: Condition to unblock the write operation on a socket have been modified to enforce detection of write capability.|
|RDAPI-29706|01467874|**Issue**: Duplicate Headers were not being forwarded to ICAP Server. **Resolution**: Duplicate headers are now forwarded.|
|RDAPI-29726|01465267  01471843|**Issue**: Response body was not return between API Gateway - client transaction and an error "no content available" was thrown when Connect to URL, OCSP or XACML PEP filters were invoked with "Load Response body and release connection" selected. **Resolution**: Response body is returned correctly when invoking Connect to URL, OCSP or XACML PEP filters with "Load Response body and release connection" selected.|
|RDAPI-29742|01468501|**Issue**: The cache of database result sets was combining results from different queries. **Resolution**: The result set from each filter DB query is merged and placed onto the the request request whiteboard. However this process also merged the underlying collection data structure which had the unwanted affect of merging result sets in the cache. Solution here is to use a separate collection data structure when merging results onto the whiteboard.|
|RDAPI-29749|01462621|**Issue**: APIGW container cannot connect to Cassandra when it is not running in a container. **Resolution**:  Add a step in the docs when it is not running the Cassandra in a container, then users must set the Cassandra keyspace name to a static value.|
|RDAPI-29770|01472247|**Issue**: Documentation displayed an outdated default cipher string. **Resolution**: Documentation displays the current default cipher string that is shown in Policy Studio.|
|RDAPI-29807|01416403|**Issue**: API Gateway Visual Mapper filter XML transformations fail occasionally due to thread interference. **Resolution**: The thread interference in the API Gateway Visual Mapper filter is fixed and XML transformations no longer fail due to thread interference.|
|RDAPI-29817|01469980|**Issue**: Quotas API - validation of time period changed. **Resolution**: Quotas API documentation updated to reflect period second value. Period value can be one of second, minute, hour, day or week.|
|RDAPI-30232|01491975|**Issue**: API Manager returns an error message when the user tries to import an OpenAPI specification with a colon as a separator between two path parameters, and the import fails. **Resolution**: The swagger parser is updated and it is now possible to import an OpenAPI specification with a colon as a separator between two path parameters.|

## Known issues

The following are known issues for this update.

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

## Documentation

To find all available documentation for this product version:

1. Go to [Manuals on the Axway Documentation portal](https://docs.axway.com/bundle).
2. In the left pane **Filters** list, select your product or product version.

Customers with active support contracts must log in to access restricted content.

For information on the different operating systems, databases, browsers, and thick client platforms supported by each Axway product, see [Supported Platforms](https://docs.axway.com/bundle/Axway_Products_SupportedPlatforms_allOS_en).

## Support services

The Axway Global Support team provides worldwide 24 x 7 support for customers with active support agreements.

Email [support@axway.com](mailto:support@axway.com) or visit [Axway Support](https://support.axway.com/).

See [Get help with API Gateway](/docs/apim_administration/apigtw_admin/trblshoot_get_help/) for the information that you should be prepared to provide when you contact Axway Support.
