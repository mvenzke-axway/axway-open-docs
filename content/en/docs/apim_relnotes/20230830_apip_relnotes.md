---
title: API Portal 7.7 August 2023 Release Notes
linkTitle: API Portal August 2023
draft: false
weight: 95
date: 2023-06-12
description: null
---
API Portal updates are cumulative, comprising new features and changes delivered in previous updates unless specifically indicated otherwise in the Release notes.

This update contains security fixes, and it addresses a number of customer defects.

There is also a performance improvement for the UI in displaying the API Details page. This has been made much more efficient by removing a column (Authentication detail) from the table of Subscribed applications, and now only calling the information required. The authentication information is still available, and can be seen by clicking into any row of the table.

## Installation

API Portal is available as a software installation or a virtualized deployment in a Docker container. For more information, see the following options:

* If you are installing API Portal for the first time using this update, see [Install API Portal](/docs/apim_installation/apiportal_install/).
* If you are already using API Portal (7.5.x, 7.6.x, 7.7.x) and want to install this update, see [Upgrade API Portal](/docs/apim_installation/apiportal_install/upgrade_automatic/).

### Docker containers

* To deploy API Portal in Docker containers, see [Deploy API Portal in containers](/docs/apim_installation/apiportal_docker/).
* If you are already using API Portal in Docker containers and you want to install this update, see [Upgrade API Portal in Docker containers](/docs/apim_installation/apiportal_docker/docker_portal_upgrade/).

## Limitations of this update

This update has the following limitations:

* API Portal 7.7.20230830 is compatible with API Gateway and API Manager 7.7.20230830 only.
* To update to API Portal August 2023 (which is based on Joomla 4) from versions older than February 2022, we recommend you to upgrade to API Portal February 2022 first, then follow the [Upgrade API Portal May 2022 or latest](/docs/apim_installation/apiportal_install/upgrade_automatic/#upgrade-to-api-portal-may-2022-or-latest-releases) section.
* Direct update to API Portal August 2023 can be done only from API Portal May 2022 and later by following the [Upgrade from the Joomla! Administrator Interface](/docs/apim_installation/apiportal_install/upgrade_automatic/#upgrade-from-the-joomla-administrator-interface) section.
* This update is not available as a virtual appliance or as a managed service on Axway Cloud.

## New features and enhancements

<!-- The following new features and enhancements are available in this update. -->

No new features are deployed with this update.

## Important changes

It is important, especially when upgrading from an earlier version, to be aware of the following changes in the behavior or operation of the product in this update.

### Manual migration of Elasticsearch indexation schedules

The tool for indexation scheduling (Jobber) was replaced with Ofelia. As a result, the old Elasticsearch indexation schedules for APIs and Applications must be backed up and manually migrated in JAI after a Docker container upgrade.

### API Details page and JAI settings changes

In the **API Details** page, the **Authentication** column in the table of subscribed applications is removed now. Also, the settings for show and hide API keys, OAuth, and external OAuth are removed from the API Portal **Try It Application** module settings.

<!-- IAP-6088 -->

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
* Additional fixes might be delivered as patches up to 15 months after the release date. You can find the list of patches available on top of this update on [Axway Support](https://support.axway.com/en/search/index/type/Downloads/q/20230830/ipp/100/product/545/version/3036/subtype/8). If no patches were created for this release, the link will return "No search results found".

### Fixed security vulnerabilities

| Internal ID  | Case ID | CVE Identifier | Description                                                                                                                                                                                        |
|--------------|---------|----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| IAP-6262     |         |    CVE-2022-31147            | **Issue**:  jquery.validate version 1.19.3 is used and it has security vulnerabilities. **Resolution**: jquery.validate is upgraded to version 1.19.5 in which the security vulnerability is fixed. |
| IAP-6087     |    01471805     |      CVE-2023-29491          | **Issue**: Vulnerabilities were found in the third-party component that we used for scheduling the indexation job for Elasticsearch. **Resolution**: This component has been replaced by the open source scheduler called Ofelia.            |

### Other fixed issues

| Internal ID | Case ID | Description                  |
|-------------|---------|----------------------------------|
| IAP-4699    | 01295149, 01296641        | **Issue**: After several logins and logouts in the authorization server through API Portal, the scopes are not properly sent. **Resolution**: OAuth scopes are sent in the same format each time.                              |
| IAP-5606    |         | **Issue**: API Portal ships with database-related errors on JAI Dashboard. **Resolution**: There are no such errors when installing and upgrading API Portal.                                                                                               |
| IAP-6088    |     01469035    | **Issue**: Too many API requests against API Manager are executed on the API Details page, impacting the performance of the page. **Resolution**: The number of API requests executed is decreased by code refactoring and the removal of the authentication column in the subscribed applications table.                                              |
| IAP-6234    |     01478733    | **Issue**: Registration form is not sanitized, and XSS is possible. **Resolution**: Registration form is properly sanitized.                                                                                               |
| IAP-6241    |     01472398    | **Issue**: Ending approval Application subscriptions have no visual indicator in the New T4 based UI. **Resolution**: There is a new visual indicator for when an Application subscription is pending approval.                              |
| IAP-6247    |     01483929, 01487222, 01495520    | **Issue**: An error is shown when trying to install EasyBlog. **Resolution**: EasyBlog is installed successfully                                                                     |

## Known issues

The following are known issues for this update.

### API Portal pages cannot be displayed after upgrade

After upgrade to PHP 8.0, for API Portal versions February 2022 (7.7.20220228) to November 2022 (7.7.20221130) only, an error is shown. Follow [KB Article #182479](https://support.axway.com/en/articles/article-details/id/182479/do/search) to learn how to upgrade to PHP 8.0.

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

## Documentation

To find all available documentation for this product's version:

1. Go to **Library** on the [Axway Documentation portal](https://docs.axway.com/bundle).
2. From the **Product** list, select **API Management**.

Customers with active support contracts must log in to access restricted content.

### Removal of Deprecated section

As of this update, we have removed the **Deprecated features** section from our Release notes to simplify the communication of capabilities being removed from the solution.

There are now only two states - a capability is on notice for removal at some time in the future, or a capability has been removed from the solution.

## Supported platforms

For information on the different operating systems, databases, browsers, and thick client platforms supported by each Axway product, see [Supported Platforms](https://docs.axway.com/bundle/Axway_Products_SupportedPlatforms_allOS_en_HTML5/page/api_management.html).

## Support services

The Axway Global Support team provides worldwide 24 x 7 support for customers with active support agreements. Email [support@axway.com](mailto:support@axway.com) or visit [Axway Support](https://support.axway.com/).

See [Get help with API Gateway](/docs/apim_administration/apigtw_admin/trblshoot_get_help/) for the information that you should be prepared to provide when you contact Axway Support.
