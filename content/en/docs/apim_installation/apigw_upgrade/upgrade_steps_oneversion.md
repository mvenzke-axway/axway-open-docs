{
"title": "Update from API Gateway One Version",
  "linkTitle": "Update from API Gateway One Version",
  "weight": 25,
  "date": "2020-09-30",
  "description": "Learn how to update from API Gateway One Version to the latest delivery."
}

After you **upgrade** your [API Gateway 7.5.x or 7.6.x](/docs/apim_installation/apigw_upgrade/upgrade_steps_extcass/) to [API Gateway One Version](https://community.axway.com/s/question/0D52X00008WUjgeSAD/introducing-one-version-for-api-management) (API Gateway 7.7), follow the instructions on this page to **update** your API Gateway One Version to the latest delivery.

* The [sysupgrade](/docs/apim_installation/apigw_upgrade/upgrade_script/) script is not used when updating to API Gateway One Version.
* If you have installed a licensed version of API Gateway One Version or API Manager One Version, you do not require a new license to install updates.

## Before you start

All the following steps are mandatory, and you must perform them in the correct order to update your API Gateway to the latest update.

1. [Back up customized files](#back-up-customized-files).
2. [Install an API Gateway server update](#install-an-api-gateway-server-update).
3. [Install a Policy Studio update](#install-a-policy-studio-update).
    * You must fix any [broken references](/docs/apigtw_devops/team_dev_dependencies#show-broken-references) before updating Policy Studio. Broken references are not maintained, and will be removed on update.
4. [Install a Configuration Studio update](#install-a-configuration-studio-update).
5. [Install an API Gateway Analytics update](#install-an-api-gateway-analytics-update).

## Back up customized files

Before updating your product, ensure to back up any customized files from your `INSTALL_DIR` directory.

The following is a list of directories that might contain customized files:

```none
webapps/apiportal/vordel/apiportal
webapps/emc/vordel/manager/app
webapps/emc
system/conf/apiportal/email
system/conf
samples/scripts/
tools/filebeat-VERSION-PLATFORM
conf
```

When you are restoring the files, ensure that you merge any updated files instead of copying them back directly to avoid any regex matching issues.

## Install an API Gateway server update

If you have installed an existing version of API Manager, installing the **API Gateway server** update automatically also installs the updates and fixes (Fixes are only applied if they have not previously been applied) for API Manager.

You can easily install API Gateway server update by running the `update_apigw.sh` script. The script performs the following:

* Check that the installation directory is valid.
* Check that the user who owns the API Gateway binaries is the same user running the `update_apigw.sh` script.
* Check that the Node Manager and API Gateway instances that run on the local machine are not running.
* Take a backup unless `--no_backup` has been specified.
* Install the update content into the API Gateway installation directory.

The script also performs all of the steps that were performed by a post installation script in earlier updates. This includes:

* JRE cleanup.
* `system/lib/modules` cleanup.
* Third-party and Axway JAR cleanup.
* Apply `acl.json` fix.
* Apply passphrase obfuscation fix.
* Apply modifications to Node Manager entity store configuration.

To see the `update_apigw.sh` script options, run the script with `--help`, for example:

```bash
./update_apigw.sh --help
```

To run the script without user interaction, specify `--mode unattended`.

### Steps to install the API Gateway server update

To install the update on your existing API Gateway 7.7 server installation, perform the following steps:

1. Ensure that your existing API Gateway instance and Node Manager have been stopped.
2. Remove any previous patches from your `INSTALL_DIR/apigateway/ext/lib` and `INSTALL_DIR/apigateway/META-INF` directories (or the `ext/lib` directory in an API Gateway instance).
    * All patches have already been included in API Gateway One Version, so you do not need to copy patches from a previous version.
3. If you have used `setcap` to grant API Gateway permission to use [privileged ports](#allow-api-gateway-to-listen-on-privileged-ports), you must remove these permissions now because they might prevent files from being overwritten:

   ```bash
   setcap -r INSTALL_DIR/apigateway/platform/bin/vshell
   ```

4. Download and unpack the API Gateway 7.7 server update file into a new directory, and not into the existing API Gateway installation directory. For example:

   ```bash
   mkdir 77update
   tar xzvf APIGateway_7.7.YYYYMMDD_Core_linux-x86-64_BNnn.tar.gz -C 77update
   ```

5. Run the `update_apigw.sh` script from the directory into which you extracted the update file (for example, `77update`) and specify your API Gateway installation directory using the `--install_dir` option. For example:

   ```bash
   ./update_apigw.sh --install_dir /opt/Axway-7.7/
   ```
6. Restart your Node Manager and API Gateway instances on the local machine.

### Change the trace level

The `update_apigw.sh` script generates a trace file in the `update-output/trace` directory. Use the `--tracelevel` option to [change the level of tracing](/docs/apim_administration/apigtw_admin/tracing#set-api-gateway-trace-levels).

### Specify a directory to back up your installation

The script takes a backup of your entire API Gateway installation directory and places it in a `tar` file in the `update-output/backups` directory. Specify a different directory using the `--backup_dir` option. To manage your own backups, use the `--no_backup` option.

## Install a Policy Studio update

You can run the `update_policy_studio.sh` script, available from API Gateway One Version Policy Studio update pack (for example, `APIGateway_7.7.YYYYMMDD_PolicyStudio_linux-x86-64_BNnn.tar.gz`) to update your existing Policy Studio installation up to the [February 2023](/docs/apim_relnotes/20230228_apimgr_relnotes/) release.

A full installation is required for the May 2023 release because of the the Eclipse RCP 4.9 base update. Forward updates from this version onwards will be supplied.

Running this script performs the following:

* Back up your existing `INSTALL_DIR/policystudio` directory. The backup of the installation is created at `INSTALL_DIR/backups/policystudio/<date_time>`.
* Remove old JRE versions by deleting the `INSTALL_DIR/policystudio/jre` directory.
* Remove old plugins by deleting the `INSTALL_DIR/policystudio/plugins` directory.
* Unzip and extract API Gateway 7.7 Policy Studio update over the `policystudio` directory in your existing API Gateway 7.7 installation directory.
* Start Policy Studio with `policystudio -clean`.

To install the Policy Studio update, download and unpack the API Gateway 7.7 Policy Studio update file into a new directory. For example:

```bash
mkdir 77update
tar -xzvf APIGateway_7.7.YYYYMMDD_PolicyStudio_linux-x86-64_BNnn.tar.gz -C 77update
```

* You must execute the update script using the same user who installed Policy Studio.
* You must extract the tar.gz file into a new directory, and not into the existing API Gateway installation directory.

Run the `update_policy_studio.sh` script from the directory into which you extracted the update file (for example, `77update`), and specify your API Gateway installation directory as an argument with the `--install_dir` flag:

```bash
./update_policy_studio.sh --install_dir $INSTALL_DIR
```

`$INSTALL_DIR` is the base API Gateway 7.7 installation directory that contains the `policystudio` directory.

For example:

```bash
./update_policy_studio.sh --install_dir /opt/Axway-7.7/
```

{{< alert title="Note" color="primary" >}}If you had applied any modifications to the `policystudio.ini` file, you must reapply them after upgrade.{{< /alert >}}

### Install a Policy Studio update on Windows

For installations running on Windows 7, you must manually unzip the Policy Studio update.

The `update_policy_studio.bat` update script is available for Windows. It is located in the API Gateway 7.7 Policy Studio Update pack (`.zip`) for Windows. For example:

```
update_policy_studio.bat --install_dir $INSTALL_DIR
```

## Install a Configuration Studio update

You can run the `update_configuration_studio.sh` script, which is available from API Gateway One Version Configuration Studio update pack (for example, `APIGateway_7.7.YYYYMMDD_ConfigurationStudio_linux-x86-64_BNnn.tar.gz`), to update your existing Configuration Studio installation up to the [February 2023](/docs/apim_relnotes/20230228_apimgr_relnotes/) release.

A full installation is required for the May 2023 release because of the Eclipse RCP 4.9 base update. Forward updates from this version onwards will be supplied.

Running this script performs the following:

* Back up your existing `INSTALL_DIR/configurationstudio` directory. The backup of the installation is created at `INSTALL_DIR/backups/configurationstudio/<date_time>`.
* Remove old JRE versions by deleting the `INSTALL_DIR/configurationstudio/jre` directory.
* Remove old plugins by deleting the `INSTALL_DIR/policystudio/plugins` directory.
* Unzip and extract API Gateway 7.7 Configuration Studio Update over the `configurationstudio` directory in your existing API Gateway 7.7 installation directory.
* Start Configuration Studio with `configurationstudio -clean`

To install the Configuration Studio update, download and unpack the API Gateway 7.7 Configuration Studio update file into a new directory. For example:

```bash
mkdir 77update
tar -xzvf APIGateway_7.7.YYYYMMDD_ConfigurationStudio_linux-x86-64_BNnn.tar.gz -C 77update
```

* You must execute the update script using the same user who installed Configuration Studio.
* You must extract the file into a new directory and not into the existing API Gateway installation directory.

Run the `update_configuration_studio.sh` script from the directory into which you extracted the Update file (for example, `77update`), and specify your API Gateway installation directory as an argument with the `--install_dir` flag:

```bash
./update_configuration_studio.sh --install_dir $INSTALL_DIR
```

`$INSTALL_DIR` is the base API Gateway 7.7 installation directory that contains the `configurationstudio` directory.

For example:

```bash
./update_configuration_studio.sh --install_dir /opt/Axway-7.7/
```

{{< alert title="Note" color="primary" >}}If you had applied any modifications to the `configurationstudio.ini` file, you must reapply them after upgrade.{{< /alert >}}

### Install a Configuration Studio update on Windows

For installations running on Windows 7, you must manually unzip the Configuration Studio update.

The `update_configuration_studio.bat` update script is available for Windows. It is located in the API Gateway 7.7 Configuration Studio update pack (`.zip`) for Windows.

```bash
update_configuration_studio.bat --install_dir $INSTALL_DIR
```

## Install an API Gateway Analytics update

To install the update on your existing API Gateway Analytics 7.7 installation, perform the following steps:

1. Ensure that your existing API Gateway Analytics instance and Node Manager have been stopped.
2. Remove old third-party libraries by deleting the following directories:

   ```bash
   INSTALL_DIR/analytics/system/lib/modules
   ```
3. Remove old JRE versions by deleting the following directories:

   ```bash
   INSTALL_DIR/analytics/platform/jre
   ```
4. Verify the owners of API Gateway binaries before extracting the update.

   ```bash
   ls -l INSTALL_DIR/analytics/posix/bin
   ```
5. Using the same user who owns the API Gateway Analytics binaries, unzip and extract API Gateway 7.7 Analytics update over the `analytics` directory in your existing API Gateway 7.7 installation directory. For example:

   ```bash
   tar -xzvf APIGateway_7.7.YYYYMMDD_Analytics_linux-x86-64_BNnn.tar.gz -C /opt/Axway-7.7/analytics/
   ```
6. Change to the `analytics` directory in your installation:

   ```bash
   cd $INSTALL_DIR/analytics
   ```
7. Enable the `execute` flag for the post-installation script, if it is not enabled yet:

   ```bash
   chmod +x apigw_analytics_sp_post_install.sh
   ```
8. Run the post-install script for API Gateway Analytics.

   ```bash
   apigw_analytics_sp_post_install.sh
   ```

## Allow API Gateway to listen on privileged ports

If you are running API Gateway as a non-root user, you must perform the following steps to grant the required privileges to API Gateway processes to use privileged ports, ports below `1024`. These steps are not required if API Gateway only needs to use ports numbered `1024` and above.

After updating your installation, perform the following steps:

1. Add the following line to the `INSTALL_DIR/system/conf/jvm.xml` file:

   ```none
   <VMArg name="-Djava.library.path=$VDISTDIR/$DISTRIBUTION/jre/lib/amd64/server:$VDISTDIR/$DISTRIBUTION/jre/lib/amd64:$VDISTDIR/$DISTRIBUTION/lib/engines:$VDISTDIR/ext/$DISTRIBUTION/lib:$VDISTDIR/ext/lib:$VDISTDIR/$DISTRIBUTION/jre/lib:$VDISTDIR/$DISTRIBUTION/jre/lib/server:system/lib:$VDISTDIR/$DISTRIBUTION/lib"/>
   ```

2. Allow API Gateway to listen on privileged ports:

   ```bash
   setcap 'cap_net_bind_service=+ep cap_sys_rawio=+ep' INSTALL_DIR/platform/bin/vshell
   ```

## Update API Manager

Updating API Manager is now carried out during the application of the latest API Gateway update. This will update the API Manager `config` script, which can then be pulled into a Policy Studio project. However, it is still required to perform one of the following three options to ensure that the update of API Manager performed by the API Gateway update is not overwritten by older deployments or policies.

* Policy Studio project upgrades. Importing an existing API Manager Policy Studio project will upgrade API Manager. The upgrade is also applied when creating a new project from an existing `fed` file.
* API Manager `.fed` files can be upgraded using the [upgradeconfig](/docs/apim_installation/apigw_upgrade/upgrade_analytics#upgradeconfig-options) script.
* The [projupgrade](/docs/apim_reference/devopstools_ref#projupgrade-command-options) script will apply API Manager updates to any existing projects.

## Update cipher scheme

The cipher scheme for all encrypted data in the system (such as Database/LDAP passwords, private keys, and so on) uses Password Based Key Derivation Function 2 (PBKDF2) with more secure parameters. This reduces vulnerability to brute force attacks. The cipher scheme is backwards compatible with previous versions of the cipher scheme, and it is able to decrypt data, which was encrypted in the old cipher scheme.

The Entity store is re-encrypted with PBKDF2 as part of API Gateway update process, but the Key property store will not be re-encrypted.

To make use of a more secure cipher scheme, you must re-encrypt your KPS data using the `kpsadmin` command. For more information see, [Re-encrypt the KPS data](/docs/apim_policydev/apigw_kps/how_to_use_kpsadmin_command#re-encrypt-the-kps-data).

### Transfer KPS data between environments

Encrypted KPS data cannot be transferred directly between environments even when the passphrase in use is the same in both environments. Instead, you must use one of the following options:

* For internal API Manager tables, use the [Cassandra Backup and Restore](/docs/cass_admin/cassandra_bur/) process and run [KPS Admin Re-Encrypt](/docs/apim_policydev/apigw_kps/how_to_use_kpsadmin_command#re-encrypt-the-kps-data).
* For non-Cassandra KPS table, use the [KPS Admin Backup and Restore](/docs/apim_policydev/apigw_kps/how_to_use_kpsadmin_command#back-up-and-restore) process. The restore command decrypts the data from the source environment and re-encrypts the data for the target environment.

### Use the cipher scheme with custom code

When instantiating a `PasswordCipher` in custom libraries or in Policy Studio script filters, be aware that this enhanced cipher algorithm will result in longer generation times. While not recommended, it is possible to revert to the previous weaker cipher if absolutely necessary, should performance be the higher concern.

To revert to the weaker cipher algorithm, you can use the `setMode` method, such as in the following `jython` snippet:

   ```
   from com.vordel.common.crypto import PasswordCipher
   
   def invoke(msg):
      cipher = PasswordCipher("ThisIsAPassword!123".toCharArray())
      cipher.setMode(PasswordCipher.Mode.SHA1_3DES)
   ```
