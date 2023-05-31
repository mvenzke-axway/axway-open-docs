---
title: API Portal 7.7 May 2023 Release Notes
linkTitle: API Portal May 2023
draft: false
weight: 95
date: 2023-05-23
description: null
---
API Portal updates are cumulative, comprising new features and changes delivered in previous updates unless specifically indicated otherwise in the Release notes.

In this update, we have introduced support for lightweight endpoints of API Manager APIs, we have integrated with the Joomla native CSP plugin, and we have delivered some password management improvements, specifically the API Portal will comply with the password improvements that have been made in API manager in the May 2023 update. We have also completed some security fixes and addressed some defects.

## Installation

API Portal is available as a software installation or a virtualized deployment in a Docker container. For more information, see the following options:

* If you are installing API Portal for the first time using this update, see [Install API Portal](/docs/apim_installation/apiportal_install/).
* If you are already using API Portal (7.5.x, 7.6.x, 7.7.x) and want to install this update, see [Upgrade API Portal](/docs/apim_installation/apiportal_install/upgrade_automatic/).

### Docker containers

* To deploy API Portal in Docker containers, see [Deploy API Portal in containers](/docs/apim_installation/apiportal_docker/).
* If you are already using API Portal in Docker containers and you want to install this update, see [Upgrade API Portal in Docker containers](/docs/apim_installation/apiportal_docker/docker_portal_upgrade/).

## Limitations of this update

This update has the following limitations:

* API Portal 7.7.20230530 is compatible with API Gateway and API Manager 7.7.20230530 only.
* To update to API Portal May 2023 (which is based on Joomla 4) from versions older than February 2022, we recommend you to upgrade to API Portal February 2022 first, then follow the [Upgrade API Portal May 2022 or latest](/docs/apim_installation/apiportal_install/upgrade_automatic/#upgrade-to-api-portal-may-2022-or-latest-releases) section.
* Direct update to API Portal May 2023 can be done only from API Portal May 2022 and later by following the [Upgrade from the Joomla! Administrator Interface](/docs/apim_installation/apiportal_install/upgrade_automatic/#upgrade-from-the-joomla-administrator-interface) section.
* This update is not available as a virtual appliance or as a managed service on Axway Cloud.

## New features and enhancements

The following new features and enhancements are available in this update.

### Support for new password in API Manager

Support has been added in API Portal for the new password improvements in API manager. For more information, see [API Gateway and API Manager 7.7 May 2023](/docs/apim_relnotes/20230530_apimgr_relnotes) release notes.

<!-- IAP-6044, IAP-6046, IAP-6079 -->

## Important changes

It is important, especially when upgrading from an earlier version, to be aware of the following changes in the behavior or operation of the product in this update.

### Temporary password expiring time and translation of appropriate error message

In the scenario where a user receives a temporary password, this password must be used within a configurable time frame. If this time frame has expired, API Portal will display an error message on the login screen.

Because API Portal ships with english translations by default, a new "translatable placeholder" (translatable key-value pair in a language file) has been added to support translation of this error message. Customers using API Portal in a language other than English should add the translation of this error to the corresponding language file, otherwise the error message will be displayed in English.

<!-- IAP-6036 -->

### Password history rules and translation of appropriate error message

In the scenario where a user attempts to use a password that they have previously used, and that password is contained in the password history (for example, the last X passwords, where X is configurable), API Portal will display a new error message on the password reset screen.

Because API Portal ships with english translations by default, a new "translatable placeholder" (translatable key-value pair in a language file) has been added to support translation of this error message. Customers using API Portal in a language other than English should add the translation of this error to the corresponding language file, otherwise the error message will be displayed in English.

<!-- IAP-6035 -->

### Synchronize default login protection settings

Default login protection settings between API Portal and API Manager can be synchronized on fresh install, but on upgrade, we do not change any of the user settings. Therefore, you must verify that the settings are synchronized, or there might be a discrepancy between the login protection settings of API Portal and API Manager.

For example, the following will lead to 3 more available requests to API Portal:

* API Manager: Lock user account after 3 wrong login attempts.
* API Portal: Lock user account after 6 wrong login attempts.

The same logic applies for all other login protection settings (for example, lockout duration,and so on).

<!-- IAP-6033 -->

### CSP is not applied when using the new T4 template

In the Joomla! Administration Interface (JAI), you must manually copy the Content Security Policy (CSP) text from the **API Portal - System** plugin and apply it to the Joomla! native CSP plugin, located under **System > Plugins > System - HTTP Headers**. This will restore the CSP rules in API Portal.

<!-- IAP-5970 -->

### Multi-catalog - API list displayed is not correct when creating an application

When displaying APIs in different catalogs using tags, the correct APIs are shown in each catalog. However, when you create or edit an application and attempt to subscribe to an API, the list displayed in this scenario was not correct.

This issue is now fixed, but if Elasticsearch is enabled in API Portal, you must perform a re-indexation from the Joomla! Administration Interface (JAI) to ensure this data is up to date and correctly displayed in the application views.

<!-- IAP-5826 -->

### Multi-Organization - Incorrect API catalog data displayed

The API catalog data stored in the Redis cache when a user is a member of multiple organizations was not correct. To ensure the cache is correctly updated, the JAI Administrator must perform a Redis cache purge after upgrade, so that it can be repolulated, and the fix can take immediate effect.

<!-- IAP-5232 -->

### Inconsistent display of application quota data

Quota data displayed in API Portal now matches that displayed in API Manager. If Elasticsearch is enabled and in use, a re-indexation must be triggered because a new property has been added to the Elasticsearch "apis" index for this fix.

<!-- IAP-5058 -->

## Deprecated features

<!-- As part of our software development life cycle, we constantly review our API Portal offering. As part of this update, the following capabilities have been deprecated. -->

No features have been deprecated in this update.

## End of support notices

There are no end of support notices in this update

## Removed features

To stay current and align our offerings with customer demand and best practices, Axway might discontinue support for some capabilities. As part of this review, the following features have been removed.

<!--  No features have been removed in this update. -->

### Removal of API Portal CSP implementation

The custom Content Security Policy (CSP) implementation of API Portal is no longer used. The native Joomla CSP plugin (found at **JAI > System > Manage > Plugins > System - HTTP Headers**) must be used instead.

## Fixed issues

This version of API Portal includes:

* Fixes from all 7.7 updates released prior to this version. For details of all the update fixes included, see the corresponding [release note](/docs/apim_relnotes/) for each 7.7 update.
* Additional fixes might be delivered as patches up to 15 months after the release date. You can find the list of patches available on top of this update on [Axway Support](https://support.axway.com/en/search/index/type/Downloads/q/20230530/ipp/100/product/545/version/3036/subtype/8). If no patches were created for this release, the link will return "No search results found".

### Fixed security vulnerabilities

| Internal ID  | Case ID | CVE Identifier | Description                                                                                                                                                                                        |
|--------------|---------|----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| IAP-6079     | 1458756 |     CVE-2022-3064           | **Issue**: API Portal docker image is using packages that have security vulnerabilities. **Resolution**: API Portal docker image packages were upgraded and are not longer vulnerable.                                                                                     |
| IAP-6044     |         |      CVE-2023-27560          | **Issue**: phpseclib 3.0.14 has security vulnerability. **Resolution**: phpseclib was upgraded to version 3.0.19, which is not vulnerable. |
| IAP-6043     |         |                | **Issue**: Supervisor and Apache packages from API Portal Docker image have security vulnerabilities. **Resolution**: Apache package was upgraded and it is no longer vulnerable. Supervisor vulnerability was analyzed and marked as false positive.            |
| IAP-5020     |         |  | **Issue**: daterangepicker.js is vulnerable to cross-site scripting. **Resolution**: A fix for cross-site scripting vulnerability was added in daterangepicker.js.                                 |
| IAP-5970     |         |                | **Issue**: Content Security Policy (CSP) is not applied in new UI scenario. **Resolution**: Content Security Policy is applied in new UI scenario. API Portal switches from using custom CSP implementation to using Joomla CSP implementation.                                              |

### Other fixed issues

| Internal ID | Case ID | Description                                                                                                                                                                                                                                                 |
|-------------|---------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| IAP-5058    | 1334036        | **Issue**: API methods quota settings are not correctly displayed in the API Portal. **Resolution**: API methods quota settings are now displayed correctly in API Portal.                                                                                               |
| IAP-5232    | 1337256 | **Issue**: With Redis caching enabled, API Catalog data is mixed for different organizations within one API Manager. **Resolution**: With Redis caching enabled, API Catalog is shown only for organizations the current user is in. |
| IAP-5606    |  | **Issue**: API Portal ships with Database-related errors on JAI Dashboard. **Resolution**: There are no such errors when installing and upgrading API Portal.            |
| IAP-5715    | 1417024 | **Issue**: After upgrading to PHP 8.0 for API Portal versions 7.7.20220228 to 7.7.20221130, API Portal fails with json decode error. **Resolution**: SQL migration query added to fix JSON decode and created a KB article on how to fix the issue for older versions.            |
| IAP-5826    | 1419271 | **Issue**: Creating or editing application APIs list with available APIs to subscribe is not correct when multiple API catalog menu items are defined in API Portal. **Resolution**: APIs list with available APIs to subscribe is correctly shown on application create / edit, regardless of how many API catalog menu items are defined in the API Portal.     |
| IAP-5968    | 1469388, 1440374 | **Issue**: APIs published prior to November 2021 release will not be able to use OAuth Client Credentials flow on API details page. **Resolution**: "Use Legacy OAuth Configuration Fallback" toggle was added in JAI to enable the usage of OAuth Client Credentials for APIs published prior to November 2021 release.            |
| IAP-6002    |  | **Issue**:  API lightweight endpoint was not used in API Portal. **Resolution**: API lightweight endpoint is implemented in API Portal and it is used if summary is chosen for API information source in JAI.            |

## Known issues

The following are known issues for this update.

<!-- known issues below were added during May 23 release -->

### API Portal pages cannot be displayed after upgrade

After upgrade to PHP 8.0, for API Portal versions February 2022 (7.7.20220228) to November 2022 (7.7.20221130) only, an error is shown.

Follow [KB Article #182479](https://support.axway.com/en/articles/article-details/id/182479/do/search) to learn how to upgrade to PHP 8.0.

Related Issue: IAP-5715

### Edit profile menu item cannot be edited

Users cannot edit the **Profile** menu item because, on save, it shows an error for a missing required field, the **Menu item type**. There is no specific type for this menu item, and it should not be edited.

Related Issue: IAP-5710

### Non-SSO users cannot login if the primary API Manager is configured to use SSO

When API Portal is configured to work with multiple API Managers and the *primary* API Manager is configured to use SSO, a new non-SSO user cannot be automatically created on the *secondary* API Manager on first login because the *primary* API Manager throws an error.

Related Issue: IAP-5869

<!-- known issues below were preexistent to May 23 release -->

### Data Request feature is not available for SSO users without email

[Data Request](/docs/apim_administration/apiportal_admin/manage_privacy_personal_data#manage-data-requests) feature is not able to send request for SSO users without email only for New UI page builder design T4 and Page Builder.

The functionality is available, but the emails are automatically detected from Joomla login details, while for SSO users registered without emails this information is not available and the request generation is not able to proceed. For users upgrading from versions earlier than November 22 and still using T3 and Purity III, the stylization for API Portal Data Request functionality works as before.

Related Issue: IAP-5649

### Two-factor authentication is not available with Joomla 4.2.x in API Portal

Joomla 4.2.0 includes changes around two(multi)-factor authentications. However, this functionality is not yet available in API Portal. For more information, see [Joomla 4.2 release notes](https://www.joomla.org/announcements/release-news/5865-joomla-4-2-release.html).

Related issue: IAP-5504

### Custom properties permissions per role are not respected in some scenarios

Custom properties permissions per role will not be respected for the organization in the following scenarios:

* Permissions will not be respected for the organization the API is assigned to because respecting them will cause significant performance impact on the Try-it page. The custom properties permissions will follow the role of the user's primary organization.
* On creating an application, the App has no organization yet, so the custom properties permissions cannot be respected per role of the organization. They will follow the permissions for the role of the primary organization of the user.

Related issue: IAP-4474

### Page layout and alignment for Arabic language

Changing the API Portal language to Arabic (or any other right to left language) results in issues with page layout and alignment on the API Portal Home and Pricing pages, and some buttons are not visible. As a workaround, you can turn on the development mode in JAI. Follow these steps:

1. Log in to Joomla! Admin Interface (JAI).
2. In the JAI top navigation bar, click **Extensions > Templates**.
3. Click your template style (for example, `purity_III * Default`) to open it.
4. Click the **General** tab.
5. Change **Development Mode** to `ON`.
6. Click **Save** and click **Close** to close the template style.

Related issue: IAP-308

## Documentation

To find all available documentation for this product version:

1. Go to [Manuals on the Axway Documentation portal](https://docs.axway.com/bundle).
2. In the left pane **Filters** list, select your product or product version.

Customers with active support contracts need to log in to access restricted content.

For information on the different operating systems, databases, browsers, and thick client platforms supported by each Axway product, see [Supported Platforms](https://docs.axway.com/bundle/Axway_Products_SupportedPlatforms_allOS_en).

## Support services

The Axway Global Support team provides worldwide 24 x 7 support for customers with active support agreements.

Email [support@axway.com](mailto:support@axway.com) or visit [Axway Support](https://support.axway.com/).

See [Get help with API Gateway](/docs/apim_administration/apigtw_admin/trblshoot_get_help/) for the information that you should be prepared to provide when you contact Axway Support.
