---
title: Upgrade a container deployment
linkTitle: Upgrade the container deployment
weight: 38
date: 2019-09-18
description: How to upgrade your deployment when using customized immutable images.
---
## Upgrade your API Gateway or API Manager container deployment from 7.6.2 or later

In a container deployment, an upgrade is rolled out using an orchestration tool (for example, Kubernetes or OpenShift) after new Docker images containing the upgrade are pushed to the Docker registry. This enables you to perform a rolling zero downtime update of services.

To perform an upgrade, follow these steps:

1. Download the API Gateway 7.7 Linux installer from [Axway Support](https://support.axway.com/).
2. Create a new base image using the `--installer` option to build the image from the downloaded API Gateway 7.7 installer.  

    ```
    cd emt_containers-<version>
    ./build_base_image.py --installer=apigw-770-installer.run --os=centos7
    ```

3. To upgrade your existing API Gateway FED to version 7.7, in Policy Studio 7.7, create a new project from the existing configuration to trigger an automatic upgrade.
4. Create a merge directory to contain any custom configuration (for example, `/tmp/apigateway`). The merge directory must be called `apigateway` and must have the same directory structure as the `apigateway` directory of an API Gateway installation.
5. Add any custom configuration to the merge directory. For example, to add a custom `envSettings.props` file to your image, copy `envSettings.props` to `/tmp/apigateway/conf/`.
6. Create new Admin Node Manager and API Gateway images using the `--fed` option to build the API Gateway image from the upgraded `fed` and the `--merge-dir` option to specify the merge directory containing the custom configuration. For example:

    ```
    cd emt_containers-<version>
    ./build_anm_image.py --domain-cert=certs/mydomain/mydomain-cert.pem --domain-key=certs/mydomain/mydomain-key.pem --domain-key-pass-file=/tmp/pass.txt --anm-username=gwadmin --anm-pass-file=/tmp/gwadminpass.txt --merge-dir=/tmp/apigateway
    ./build_gw_image.py --license=/tmp/api_gw.lic --domain-cert=certs/mydomain/mydomain-cert.pem --domain-key=certs/mydomain/mydomain-key.pem --domain-key-pass-file=/tmp/pass.txt --fed=my-upgraded-fed.fed --fed-pass-file=/tmp/my-group-fedpass.txt --merge-dir=/tmp/apigateway
    ```

## Apply a patch

In a container deployment, a patch or update is rolled out using an orchestration tool (for example, Kubernetes or OpenShift) after new Docker images containing the patch or update are pushed to the Docker registry. This enables you to perform a rolling zero downtime update of services.

To apply a patch, follow these steps:

{{< alert title="Note" color="primary" >}}Before you start, check the release notes of this patch for any specific instructions.{{< /alert >}}

1. Download the patch from [Axway Support](https://support.axway.com/).
2. Create a merge directory to contain the patch files and any custom configuration (for example, `/tmp/apigateway`). The merge directory must be called `apigateway` and must have the same directory structure as the `apigateway` directory of an API Gateway installation.
3. Unzip and extract the patch into the merge directory.
4. Add any custom configuration to the merge directory. For example, to add a custom `envSettings.props` file to your image, copy `envSettings.props` to `/tmp/apigateway/conf/`.
5. Create new Admin Node Manager and API Gateway images using the `--merge-dir` option to specify the merge directory containing the patch files and custom configuration. For example:

    ```
    cd emt_containers-<version>
    ./build_anm_image.py --domain-cert=certs/mydomain/mydomain-cert.pem --domain-key=certs/mydomain/mydomain-key.pem --domain-key-pass-file=/tmp/pass.txt --anm-username=gwadmin --anm-pass-file=/tmp/gwadminpass.txt --merge-dir=/tmp/apigateway
    ./build_gw_image.py --license=/tmp/api_gw.lic --domain-cert=certs/mydomain/mydomain-cert.pem --domain-key=certs/mydomain/mydomain-key.pem --domain-key-pass-file=/tmp/pass.txt --merge-dir=/tmp/apigateway
    ```

## Apply an update

Follow these instructions to apply an update ([January 2020](/docs/apim_relnotes/20200130_apimgr_relnotes/) or later) or a service pack (7.7 SP1 or 7.7 SP2) to API Gateway version 7.7.

{{< alert title="Note" color="primary" >}}Before you start, check the release notes of the update or service pack for any specific instructions.{{< /alert >}}

1. Download the latest API Gateway 7.7 Linux installer (which includes the update or service pack) from [Axway Support](https://support.axway.com/).
2. Create a new base image using the `--installer` option to build the image from the downloaded API Gateway installer.

    ```
    cd emt_containers-<version>
    ./build_base_image.py --installer=apigw-new-installer.run --os=centos7
    ```

3. Create a merge directory to contain any custom configuration (for example, `/tmp/apigateway`). The merge directory must be called `apigateway` and must have the same directory structure as the `apigateway` directory of an API Gateway installation.
4. Add any custom configuration to the merge directory. For example, to add a custom `envSettings.props` file to your image, copy `envSettings.props` to `/tmp/apigateway/conf/`.
5. Create new Admin Node Manager and API Gateway images using the `--merge-dir` option to specify the merge directory containing the custom configuration. For example:

    ```
    cd emt_containers-<version>
    ./build_anm_image.py --domain-cert=certs/mydomain/mydomain-cert.pem --domain-key=certs/mydomain/mydomain-key.pem --domain-key-pass-file=/tmp/pass.txt --anm-username=gwadmin --anm-pass-file=/tmp/gwadminpass.txt --merge-dir=/tmp/apigateway
    ./build_gw_image.py --license=/tmp/api_gw.lic --domain-cert=certs/mydomain/mydomain-cert.pem --domain-key=certs/mydomain/ mydomain-key.pem --domain-key-pass-file=/tmp/pass.txt --merge-dir=/tmp/apigateway
    ```
