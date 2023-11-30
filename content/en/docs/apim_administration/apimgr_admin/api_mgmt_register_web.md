{
"title": "Register APIs in API Manager",
  "linkTitle": "Register APIs",
  "weight": "50",
  "date": "2019-09-17",
  "description": "Create or import a back-end API in API Manager."
}
API owners can use the API Manager web interface to register back-end APIs. You can manually create a new back-end API or import a definition for an existing API (for example, in Swagger, OAS3, or WSDL format). Using API Manager to register APIs means that you can register APIs in a browser, in multiple formats, without any service outage.

When a back-end API is registered, you can then virtualize it as a publicly exposed front-end API. Registered and virtualized APIs are governed by the API Gateway using configured policies. API owners and API administrators can use API Manager to manage registered APIs, and API consumers can use API Manager or API Portal to consume virtualized APIs in their applications.

{{< alert title="Note" color="primary" >}}
You must first register a back-end API before you can virtualize a front-end API. For more details, see [Virtualize APIs in API Manager](/docs/apim_administration/apimgr_admin/api_mgmt_virtualize_web/).
{{< /alert >}}

## Back-end and front-end APIs

In API Manager, the back-end API defines the contract with the remote server, which services the API that is virtualized on the API Gateway. The back-end API can be exposed by an application server on the network or in the Cloud, for example, Twitter. You can register a new back-end API manually, or you can import a definition for an existing API, which is in Swagger, WSDL, or GraphQL formats. The following image shows a manually registered back-end API:

![Back-end API in the web console](/Images/docbook/images/api_mgmt/api_mgmt_backend_api.png)

The front-end API is the virtualized publicly exposed API that is hosted in API Gateway, and which is invoked by client applications (for example, iPhone or Android apps). The following image shows a back-end API virtualized as a front-end API:

![Front-end API in the web console](/Images/docbook/images/api_mgmt/api_mgmt_frontend_api.png)

By default, the front-end API works in the same way as the back-end API, proxying the API "as-is". However, you can edit the front-end API to present an enriched, public-facing API to client applications. For example, you can change the URL path, change and map parameters, or improve the documentation.

In addition, this separation of front-end API and back-end API definitions allows the back-end API to change over time. This means that you can control how changes are exposed to client applications, thus minimizing or eliminating the potential impact on these applications.

{{< alert title="Note" color="primary" >}} API Manager only allows proxying of GraphQL APIs. {{< /alert >}}

## Enable an organization for API development

Before you begin registering APIs for an organization, you must first enable an organization for API registration and development. The API Manager welcome dialog prompts you to automatically create an *API Development* organization, which is enabled for API development by default.

To create the default *API Development* organization:

1. Click the **Client Registry** > **Organizations** view in API Manager.
2. Click the name of the organization to enable (for example, **Acme Inc**).
3. Set the **API Development** option to **On**.

You can now register back-end APIs and virtualize front-end APIs for this organization.

## Import a back-end API

To automatically register an existing back-end API you can import the API by way of its definition file or its URL.

1. Click the **API** > **Backend API** view in API Manager.
2. Click **New API** and select one of the following:

   * **Import Swagger API**: Import an API in Swagger/OAS format.
   * **Import WADL API**: Import an API in [Web Application Description Language](https://www.w3.org/Submission/2009/SUBM-wadl-20090831) (WADL) format.

       Note that API Manager only supports the `http://wadl.dev.java.net/2009/02` XML namespace for WADL elements.
   * **Import GraphQL API** : Import an API in GraphQL format.
3. On the **Import from** dialog, complete the following:

   * **Source**: Select the source type from the list.
   * **File** or **URL**: Click to select the definition file, or enter the URL of the host where the service definition is published, or the URL of the back-end service itself.
   * **URL (Introspection)**: Available only for GraphQL APIs.
   * **API Name**: A user-friendly name for the API (for example, Test API).
   * **Backend URL**:  Available only for GraphQL APIs using **File** or **URL** definitions. This is the URL of the back-end service, and it is not required for **URL Introspection** because Introspection retrieves the service definition from a live service and so the back-end URL is inferred.
   * **Organization**: Select the organization from the list (for example, Acme Inc).
   * **Authentication**: For URL-based APIs only, enter a **User name** and **Password** if required.
4. Click **Import** to import the API into the catalog.

After the API is imported, it is displayed as read-only in the list of APIs, from where you can click it to view its details.

{{< alert title="Note" color="primary" >}}Do not use spaces or the URL encoded `%20` in the base path URL.{{< /alert >}}

### Import Swagger definitions

SmartBear Swagger parser third-party libraries are used to import OAS definitions. Certificate checking is used in the parser when resolving remote references (import from URL), therefore the following flags may need to be added to `jvm.xml` file to resolve external references:

   ```
   <VMArg name="-Dio.swagger.parser.util.RemoteUrl.trustAll=true"/>
   <VMArg name="-Dio.swagger.v3.parser.util.RemoteUrl.trustAll=true"/>
   ```

For more information, see [SSL Certificate error while trying to import swagger definition](https://community.smartbear.com/t5/ReadyAPI-Questions/SSL-Certificate-error-while-trying-to-import-swagger-definition/td-p/205283) and [Dealing with self-signed SSL certificates](https://github.com/swagger-api/swagger-parser#dealing-with-self-signed-ssl-certificates).

## Import a Web service back-end API

To import an existing Web service-based back-end API, perform the following steps:

1. Click the **API Registration** > **Backend API** view in API Manager.
2. Click **New API** > **Import WSDL API**.
3. On the **Import Web Service** dialog, complete the following:

   * **URL**: Enter the URL for the web service. For example, `http://wsf.cdyne.com/WeatherWS/Weather.asmx?WSDL`.
   * **API Name**: A user-friendly name for the API (for example, `Weather API`).
   * **Organization**: Select the organization from the list (for example, **Acme Inc**).
   * **Authentication**: For URL-based APIs only, enter a **User name** and **Password** if required.
4. Click **Import** to import the API into the catalog.

### Limitations of web services support

API Manager does not support the following features:

* WSDL import from file: You can only import a web service from a URL. You must use Policy Studio if you want to import from a file.
* WS-Policy/WS-Security: If this is required, you must register the web service using Policy Studio.
* Schema validation: If schemas are available, you can validate using custom request/response policies. Otherwise, you should register using Policy Studio.
* Binding/operation selection: You cannot select specific bindings/operations for import. If this is required, you must use Policy Studio.
* Cloning: You cannot clone an API registered from a web service.

{{< alert title="Note" color="primary" >}}When your web service schemas are loaded in API Manager, they are protected by your chosen front-end API security device. Your client testing tool might be able to reach the WSDL, but not the schema imports without the appropriate key and credentials.{{< /alert >}}

For details on registering web services using Policy Studio, see [Register and secure web services](/docs/apim_policydev/apigw_web_services/).

## Import a cloud-based back-end API

Before importing a cloud API, you must first ensure that the API connector and OAuth profile for the cloud API have been configured. For example, see [Configure a connector for Salesforce APIs](/docs/apim_administration/apimgr_admin/api_mgmt_connector#configure-a-connector-for-salesforce-apis).

To import a cloud-based back-end API for existing cloud-based APIs in API Manager, perform the following steps:

1. Select **API Registration** > **Backend API**.
2. Click **New API**, and select **Import from Salesforce.com** or **Import from ServiceNow**.
3. Enter your login credentials from your cloud API provider (for example, Salesforce.com) in the dialog (if prompted).
4. Select the cloud APIs that you require in the dialog.
5. Click **Import** to import the selected APIs as a single back-end API in API Manager.

## Import a back-end API deployed on an API Gateway

To import an existing back-end or SOAP API that is already deployed on an API Gateway, perform the following steps when logged in as API administrator:

1. Click the **API Registration** > **Backend API** view in API Manager.
2. Click **New API** > **Import API from Topology**.
3. In the **Import from Topology** dialog, complete the following required settings:

   * **Host**: Enter the Admin Node Manager host name (for example, `localhost`).
   * **Port**: Enter the Admin Node Manager port number (for example, `8090`).
   * **Username**: Enter your Admin Node Manager login name.
   * **Password**: Enter your Admin Node Manager password.
   * **Group**: Select the API Gateway group name (for example, **AcmeGatewayGroup**).
   * **Instance**: Select the API Gateway instance name (for example, **AcmeGateway**).
   * **Service Type**: Select **REST API** or **SOAP service**.
   * **Service**: Select the API Gateway service name (for example, **AcmePayment**).
   * **API name**: Enter the API name (for example, **Acme Payment API**).
   * **Organization**: Select the organization (for example **Acme Inc**).
4. Click **Import** to import the API into the catalog.

## Manually register a new back-end API

To manually register a new back-end API in API Manager, perform the following steps:

1. Click the **API Registration** > **Backend API** view in API Manager.
2. Click **New API** > **New**.
3. In the **API** tab, complete the following general details:

   * **API name**: Enter a required name for the API (for example, **Acme API**).
   * **Service type**: Enter a service type for the API (for example, defaults to **REST**).
   * **Organization**: Select a required organization for the API (for example, **Acme Inc**).
   * **Backend URL**: Select a back-end URL (Available only for OAS3 APIs with multiple backends).
   * **Base path URL**: Enter a resource path. Defaults to `http://basepath.org`.

     * Do not use spaces or the URL encoded `%20` in the base path URL.
   * **Resource path**: Enter a resource path for the API. Defaults to `/api`.
   * **API version**: Enter an optional version number for the API. Defaults to `1.0`.
   * **Summary**: Enter an optional summary for the API to display in the **API Catalog**.
   * **Description**: Click the **Edit** tab, and enter an optional description for the API.
4. Click **Create** to register the API.

## Create REST API methods

This section applies only to REST API methods.

To add a REST API method to a newly registered API, perform the following steps:

1. On the **API Methods** tab, complete the following:

   * **Method Name**: Enter a required name for the API method (for example, `GetProducts`), and enter an optional **Method summary**.
   * **Verb**: Enter a required HTTP verb for the API method. Defaults to `GET`.
   * **Path**: Enter the path for the method. Defaults to `/`.
   * **Description**: Click the **Edit** tab, and enter an optional description for the API.
2. To add a parameter exposed by the API method, click the add button in the **PARAMETERS** section, and complete the following:

   * **NAME**: Enter a required name for the parameter (for example, `customer_name`).
   * **DESCRIPTION**: Enter an optional description for the parameter.
   * **TYPE**: Select the parameter type (for example, `query`, `path`, `form`, `body`, or `header`). Defaults to `query`.
       The [Swagger specification](https://swagger.io/docs/specification/2-0/describing-request-body/) only allows a method to have one `body` parameter.
   * **DATA TYPE**: Select the parameter data type (for example, `string`, `int`, `boolean`, and so on). Defaults to `string`.
   * **REQUIRED**: Select whether the parameter is required. Defaults to `No`.
   * **ALLOW MULTIPLE**: Select whether multiple parameters are allowed. Defaults to `No`.
3. To specify content types that can be consumed by the API method, click the plus (+) button in the **Consumes content-type**
   section, and enter the content type. For example, `application/xml`, `text/plain`, and so on. Defaults to `application/json`.
4. To specify content types that can be produced by the API method, click the plus (+) button in the **Produces content-type**
   section, and enter the content type. For example, `application/xml`, `text/plain`, and so on. Defaults to `application/json`.
5. To specify response codes that can be produced by the API method, click the plus (+) button in the **Response codes**
   section, and select the response codes (for example, `Create codes (201, 403, 500)`).
6. Click **Add** to add the new API method.

## Create an API data model

For JSON-based REST APIs only, you can enter the JSON schema model for the API data on the **Models** tab. This tab is not displayed for web service APIs. This model is optional and describes the data that is passed to the API as the request payload or returned from the API as the response payload. The data model is then displayed for application developers in the **API Catalog**, in API Manager or in API Portal.

Alternatively, you can add or edit the data model for a manually created REST API, but this model is read-only for an imported REST API. In this case, the data model is part of the imported definition, and cannot be changed. However, if you need to update the model (for example, to fix a bug in the schema), you can do so by cloning the API as described in the next section.

## Manage back-end API lifecycle

The API Admin can configure all back-end APIs to be editable by default by using the **Allow users to modify Backend APIs** option in **API Manager settings**.

After you have registered a back-end API, you can select it from the list of registered APIs, click **Manage selected**, and choose one of the following options:

* **Delete**: Deletes the selected API registered in the **API Registration** > **Backend API** view. You can delete APIs created as front-end APIs in the **Frontend API** view.
* **Clone API**: Creates a copy of the selected API, which you can then edit as required. You cannot clone a back-end API imported from a WSDL-based web service.

    After an API is cloned, if you subsequently delete the original back-end API, the **Download original API definition** option is no longer enabled (for the clone) because the original API definition no longer exists.
* **Export API**: Exports a copy of the selected back-end API (in `.json` format). You can then import the file into another API Manager environment as a back-end API.

    Note that GraphQL back-end APIs cannot be exported in Swagger format.
* **Download original API definition**: Downloads a copy of the original API definition.

## Next steps

After you have registered a back-end API, the next step is to virtualize it as a publicly exposed front-end API. For more details, see [Virtualize APIs in API Manager](/docs/apim_administration/apimgr_admin/api_mgmt_virtualize_web/).
