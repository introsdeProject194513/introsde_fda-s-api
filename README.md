# Introduction

This is a generic API template that can be extended based on the use case's needs

**NOTE:** This template is suitable for RESTful APIs only. Please use a different template for SOAP-based APIs.

# Description

The API template application should be used every time a new API needs to be implemented. The template pre-implements the following aspects of a Mule API application:

- Properties Configuration
- API Listener (API-Auto-Discovery, HTTPListener, and APIKit Router)
- Error Handling
- HealthCheck system
- Tracking id

Once downloaded into Anypoint Studio, the template can be extended according to the API specifications. Most of the interface specifications would be detailed in the RAML file of the API, which can be used for generating the skeletons of the flows that implement the API operations.

# Installation and Configuration

Use the following software in order to run this template:

 - Oracle Java Development Kit (JDK) 1.8

 - Apache Maven 3.2.1 or later (ensure Mule EE Repository is setup with credentials)

 - Anypoint Studio 6.2.5 or later, including the following extensions:

 - Mule ESB Server Runtime 3.8.5 EE

 - Anypoint Enterprise Security 1.6.9 (please verify how to download by this [link][1])

The project will need to be imported following the POM import wizard. Once the project is imported in Anypoint Studio, it can be configured via its properties configuration files: **api-template-v1.dev.properties**, **api-template-v1.test.properties**, **api-template-v1.prod.properties** and the **api-template-v1.properties** (used for common properties).

# Basic    Customization

1. Rename the Anypoint Studio project folder from **api-template-v1**  to the actual name of your API. In order to rename the project, right-click on the project root folder in Anypoint Studio, then select **Refactor -> Rename** and type the name of your new project (e.g.  **e-api-b2c-mobile** ). Click OK to confirm;
2. Open **global.xml** located in **src/main/app folder**, click on the **Global Elements** tab and double-click on **Secure Properties Placeholder (Configuration)** to update the name of the **api-template-v1.properties** and file **api-template-v1.${mule.env}.properties** according to the new project's name (e.g. **e-api-b2c-mobile.properties** and **e-api-b2c-mobile.${mule.env}.properties**).
3. Open the **pom.xml** file in the Anypoint Studio project to update it with the actual Maven artifact's coordinates. Change **groupId** with project group id (e.g. **com.threadsolutions.integration**) and **artifactId** with the project name that has been set in the step above (e.g.  e-api-b2c-mobile). Change also the version X.X.X to **1.0.0-SNAPSHOT**. Remove type **template** form properties element. Save the pom.xml file and let Anypoint Studio re-build the project.
4. Update  **log4j2.xml**  file under **src/main/resources** by renaming all the occurrences of **api-template-v1** in the  element with the same name used for the project's root folder above (e.g.  **e-api-b2c-mobile**). Leave all the remaining parts of log4j2.xml unchanged and just update the api-template-v1 occurrences where they appear in the file.
5. Run your project from Anypoint Studio specifying desidered **mule.env** in **Arguments** in **Run Configurations** (set also the arguments **mule.logs** and **encryption.key**). By default on this template they are set in file mule-app.properties.

# API Listener

This template provides a pre-built API Listener configuration including an HTTP Listener and an APIKit Router pointing to a RAML file ( api.raml ), which is located under the  src/main/api  folder. The template is also provided with an API Auto-Discovery global element, which is responsible for registering the API in the API Manager.

# API Auto-Discovery

The API Auto-Discovery element can be configured via the two properties showed in the snippet below. It takes the name of the API as defined in the Anypoint Platform, and the API version.

```
# Get below values from Anypoint API Manager
api.autodiscovery.name=
api.autodiscovery.version=
```

# HTTP Listener Connector

Also the HTTP Listener can be configured via properties file. It is possible to change **host**, **port**, and **base path** on which the API is listening.

The default configuration for the API HTTP Listener provided in this template is showed below. The values should be updated according to the specific project's needs.

```
# API Configuration
api.name=API Template
api.version=v1
api.application.name=api-template
api.http.listener.host=0.0.0.0
api.http.listener.port=8081
api.http.listener.path=/api/${api.application.name}/${api.version}/\*
```

# APIKit Router

The APIKit Router is pointing to the **api.raml** file that contains the RAML API specification. This template comes with an example of  api.raml  file, which should be replaced with the actual RAML file of the API that needs to be implemented.

To replace the RAML file, just paste the actual  api.raml  (along with any other JSON or XML resources) in the **src/main/api** folder, and replace the existing api.raml  when prompted to do so. Once the new  api.raml  is in the Anypoint Studio project, right-click on it and select **Mule -> Generate Flows from REST API**. This will generate the skeletons of the operations that need to be implemented according to the resources specified in the new RAML file.

The skeletons of the flows can be edited in the api.xml configuration file, which is placed in the  **src/main/app** folder.

The APIKit Router opens by default the API Console that shows the RAML specification associated to this API. The console can be accessed at the following address [**http://localhost:8081/api/v1/console/**](http://localhost:8081/api/v1/console/), where [**http://localhost:8081/api/v1**](http://localhost:8081/api/v1)  is the concatenation of the HTTP Listener's host, port, and base path.

The API Console can be disabled by setting the **api.console.enabled**  property to **false** (see example below).

```
# API Configuration
api.console.enabled=false
```

# Error Handling

The API Template comes with embedded Error Handling configuration. This ensures that the most common error scenarios are dealt with consistently, and an error response is returned to the caller accordingly.

The Error Handling configuration is specified in the  **error-handling.xml**  file inside the **src/main/app** folder of the Anypoint Studio project. The configuration includes a global Choice Exception Strategy ( **global-exception-strategy** ) that wraps a number of different **Catch Exception Strategies**, which are executed based on the exception thrown at runtime.

The global Choice Exception Strategy is set on the **api-main** flow inside the **api.xml** file. This ensures that every exception thrown is always caught and dealt with.

# Error Message

Every time an exception is thrown, the Error Handling configuration collects the error information and generates an error response message accordingly. An example of error message is provided below

```
{
  "error": {
    "errorCode": "BAD\_GATEWAY",
    "errorDateTime": "2016-22-10T20:16:45+0000",
    "errorMessage": "Upstream service unable to fulfil request.",
    "errorDescription": "The request was not validated."
  }
}
```

# Extending Error Handling

If an exception is thrown and a match on the exception type is not found, the configuration generates a default error message that is returned to the caller along with **500** HTTP response status code. However, the Error Handling configuration can be extended to include other types of exception by just adding another Catch Exception Strategy to the list of the exception strategies in the global Choice Exception Strategy.

  [1]: https://docs.mulesoft.com/release-notes/anypoint-enterprise-security-release-notes