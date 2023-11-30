---
title: API Portal 7.7 November 2023 Release Notes
linkTitle: API Portal November 2023
draft: false
weight: 95
date: 2023-09-11
description: null
---
API Portal updates are cumulative, comprising new features and changes delivered in previous updates unless specifically indicated otherwise in the Release notes.

This update contains security fixes, and it addresses a number of customer defects.

The two-factor authentication functionality, which was unavailable since the August 22 update, has been re-introduced by way of the addition of multi-factor authentication using the Joomla 4.2 multi-factor authentication module.

Also, the feature used for testing API methods is now replaced with the new Swagger UI React, which provides more stability.

A partial support for GraphQL APIs is included. This type of API is now listed in the API catalog page alongside all the other types of APIs.

## Installation

API Portal is available as a software installation or a virtualized deployment in a Docker container. For more information, see the following options:

* If you are installing API Portal for the first time using this update, see [Install API Portal](/docs/apim_installation/apiportal_install/).
* If you are already using API Portal (7.5.x, 7.6.x, 7.7.x) and want to install this update, see [Upgrade API Portal](/docs/apim_installation/apiportal_install/upgrade_automatic/).

### Docker containers

* To deploy API Portal in Docker containers, see [Deploy API Portal in containers](/docs/apim_installation/apiportal_docker/).
* If you are already using API Portal in Docker containers and you want to install this update, see [Upgrade API Portal in Docker containers](/docs/apim_installation/apiportal_docker/docker_portal_upgrade/).

## Limitations of this update

This update has the following limitations:

* API Portal 7.7.20231130 is compatible with API Gateway and API Manager 7.7.20231130 only.
* To update to API Portal November 2023 (which is based on Joomla 4) from versions older than February 2022, we recommend you to upgrade to API Portal February 2022 first, then follow the [Upgrade API Portal May 2022 or latest](/docs/apim_installation/apiportal_install/upgrade_automatic/#upgrade-to-api-portal-may-2022-or-latest-releases) section.
* Direct update to API Portal November 2023 can be done only from API Portal May 2022 and later by following the [Upgrade from the Joomla! Administrator Interface](/docs/apim_installation/apiportal_install/upgrade_automatic/#upgrade-from-the-joomla-administrator-interface) section.
* This update is not available as a virtual appliance or as a managed service on Axway Cloud.

## New features and enhancements

The following new features and enhancements are available in this update.

### Multi-factor authentication

The two-factor authentication functionality, which was unavailable since the August 22 update, has been re-introduced using the Joomla native Multi-factor Authentication - Verification Code plugin.

For more information, see [Enable two-factor authentication](/docs/apim_installation/apiportal_install/secure_harden_portal/#enable-two-factor-authentication).

## Important changes

It is important, especially when upgrading from an earlier version, to be aware of the following changes in the behavior or operation of the product in this update.

### API Catalog Swagger UI upgrade

The built-in test capability in API Catalog, used for testing APIs, is now upgraded and provides more stability and support. It is based on React v.18 and it provides significantly improved security and performance.

For more information, see [Key capabilities in API Portal](/docs/apim_administration/apiportal_admin/apip_overview/).

### Support for PHP 8.1 added in classic mode

The users of API Portal classic mode (excludes container installations) are now able to use the product with PHP 8.1.

### Refresh of cached Redis data is needed

The login page template contains some in-template JS code (part of the code changes the password field `type` attribute). The script tag that contains the code has `nonce` attribute and its value is generated for each page request. After the page is cached by redis its contents become constant for all subsequent requests along with nonce value. The fix of that issue requires redis cache refresh to take effect .

## End of support notices

<!--   To stay current and align our offerings with customer demand and best practices, Axway might discontinue support for some capabilities.
As part of this update, the following features have notice for removal:-->

There are no end of support notices in this update.

## Removed features

<!--   The following features have been removed from the product in this update: -->

No features have been removed in this update.

## Fixed issues

This version of API Portal includes:

* Fixes from all 7.7 updates released prior to this version. For details of all the update fixes included, see the corresponding [release note](/docs/apim_relnotes/) for each 7.7 update.
* Additional fixes might be delivered as patches up to 15 months after the release date. You can find the list of patches available on top of this update on [Axway Support](https://support.axway.com/en/search/index/type/Downloads/q/20231130/ipp/100/product/545/version/3036/subtype/8). If no patches were created for this release, the link will return "No search results found".

### Fixed security vulnerabilities

| Internal ID  | Case ID | CVE Identifier | Description                                                                                                                                                                                        |
|--------------|---------|----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| IAP-6086    |     01470639    |              | **Issue**:   API Portal exposes templates for direct web access. **Resolution**: Documentation updated. |
| IAP-6429    |    01521479     |      CVE-2023-44487          | **Issue**: API Portal image contains vulnerable version of nghttp2 library. **Resolution**: This library has been upgraded and API Portal is no longer vulnerable.            |
| IAP-6297    |    01502825     |      CVE-2023-35945          | **Issue**: Multiple security vulnarabilities found in API Portal Docker Image. **Resolution**: All reported CVEs are resolved in the August and November 23 releases.            |
| IAP-6420    |   01517803     |      CVE-2023-4863          | **Issue**: API Portal prebuilt image contains CVE-2023-4863 and CVE-2023-41064 vulnerabilities. **Resolution**: Packages have been upgraded and API Portal is no longer vulnerable.            |

### Other fixed issues

| Internal ID | Case ID | Description                  |
|-------------|---------|----------------------------------|
| IAP-4679    | 01295149, 01296641        | **Issue**: Documentation of setting return URI while configuring OAuth is misleading. **Resolution**: Documentation for OAuth configuration is fixed.                              |
| IAP-5606    |         | **Issue**: API Portal ships with database-related errors on JAI Dashboard. **Resolution**: There are no such errors when installing and upgrading API Portal.                                                                                               |
| IAP-6274    |     01469035    | **Issue**: There is no option to configure the default items per page when Elastic Search is enabled, and by default, the search functionality is limited to the five results displayed by default per page. **Resolution**: An option for configuring the default items to be displayed per page has been added to the Joomla Admin Interface.                                              |
| IAP-6285    |     01495733    | **Issue**:  API Portal Try-it page fails to load `chosen` library. **Resolution**: References to absent chosen library are removed.                                                                                               |

## Known issues

The following are known issues for this update.

### Possible performance impact on Application view page

In order to retrieve quota information on the [Applications view](/docs/apim_administration/apiportal_admin/customize_page_content/#customize-application-settings) page, API Portal needs to do some additional calls. This could lead to poor performance for customers with large numbers of APIs and not using Elastic search.

<!-- Related Issue: IAP-? -->

### Missing Method Examples section on Try-it page

With the implementation of the new Swagger React UI, in the **Method** tab, the **Method Example** section is missing for this release. The missing section will be re-introduced in the upcoming release.

<!-- Related Issue: IAP-? -->

### Edit profile menu item cannot be edited

Users cannot edit the **Profile** menu item because, on save, it shows an error for a missing required field, the **Menu item type**. There is no specific type for this menu item, and it should not be edited.

Related Issue: IAP-5710

### Non-SSO users cannot login if the primary API Manager is configured to use SSO

When API Portal is configured to work with multiple API Managers and the *primary* API Manager is configured to use SSO, a new non-SSO user cannot be automatically created on the *secondary* API Manager on first login because the *primary* API Manager throws an error.

Related Issue: IAP-5869

### Data Request feature is not available for SSO users without email

[Data Request](/docs/apim_administration/apiportal_admin/manage_privacy_personal_data#manage-data-requests) feature is not able to send request for SSO users without email only for New UI page builder design T4 and Page Builder.

The functionality is available, but the emails are automatically detected from Joomla login details, while for SSO users registered without emails this information is not available and the request generation is not able to proceed. For users upgrading from versions earlier than November 22 and still using T3 and Purity III, the stylization for API Portal Data Request functionality works as before.

Related Issue: IAP-5649

## Documentation

To find all available documentation for this product's version:

1. Go to **Library** on the [Axway Documentation portal](https://docs.axway.com/bundle).
2. From the **Product** list, select **API Management**.

Customers with active support contracts must log in to access restricted content.

## Supported platforms

For information on the different operating systems, databases, browsers, and thick client platforms supported by each Axway product, see [Supported Platforms](https://docs.axway.com/bundle/Axway_Products_SupportedPlatforms_allOS_en_HTML5/page/api_management.html).

## Support services

The Axway Global Support team provides worldwide 24 x 7 support for customers with active support agreements. Email [support@axway.com](mailto:support@axway.com) or visit [Axway Support](https://support.axway.com/).

See [Get help with API Gateway](/docs/apim_administration/apigtw_admin/trblshoot_get_help/) for the information that you should be prepared to provide when you contact Axway Support.
