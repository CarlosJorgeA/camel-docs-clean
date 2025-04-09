::: {#header}
# REST DSL with contract-first OpenAPI
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
::::: {#preamble}
:::: sectionbody
::: paragraph
From **Camel 4.6** onwards, the [Rest DSL](rest-dsl.html) has been
improved with a *contract-first* approach using vanilla OpenAPI
specification.
:::
::::
:::::

::::::: sect1
## How it works {#_how_it_works}

:::::: sectionbody
::: paragraph
The Rest DSL OpenAPI is a facade that builds [Rest
OpenAPI](components::rest-openapi-component.html) endpoint as consumer
for Camel routes. The actual HTTP transport is leveraged by using the
[Platform HTTP](components::platform-http-component.html), which makes
it plugin to Camel Spring Boot, Camel Quarkus or can run standalone with
Camel Main.
:::

:::: sect2
### Limitations {#_limitations}

::: paragraph
Camel does not support websockets from the OpenAPI 3.1 specification.
Neither is (at this time of writing) any security aspects from the
OpenAPI specification in use.
:::
::::
::::::
:::::::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Contract first {#_contract_first}

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
The *contract-first* approach requires you to have an existing OpenAPI
v3 specification file. This contract is a standard OpenAPI contract, and
you can use any existing API design tool to build such contracts.
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | Camel support OpenAPI v3.0 and    |
| Tip                               | v3.1.                             |
| :::                               |                                   |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
In Camel, you then use the Rest DSL in *contract-first* mode. For
example, having a contract in a file named `my-contract.json`, you can
then copy this file to `src/main/resources` so it's loaded from
classpath.
:::

::: paragraph
In Camel Rest DSL you can then very easily define *contract-first* as
shown below:
:::

::::: exampleblock
:::: content
::: dlist

Java

:   :::: listingblock
    ::: content
    ``` highlight
    @Override
    public void configure() throws Exception {
        rest().openApi("petstore-v3.json");
    }
    ```
    :::
    ::::

XML

:   :::: listingblock
    ::: content
    ``` highlight
    <rest>
      <openApi specification="petstore-v3.json"/>
    </rest>
    ```
    :::
    ::::

YAML

:   :::: listingblock
    ::: content
    ``` highlight
    - rest:
        openApi:
          specification: petstore-v3.json
    ```
    :::
    ::::
:::
::::
:::::

::: paragraph
When Camel startup the OpenAPI specification file is loaded and parsed.
For every API Camel builds HTTP REST endpoint, which are routed 1:1 to
Camel routes using the `direct:operationId` naming convention.
:::

::: paragraph
The *pestore* has 18 APIs here we look at the 5 user APIs:
:::

:::: listingblock
::: content
``` highlight
 http://0.0.0.0:8080/api/v3/user                       (POST)   (accept:application/json,application/x-www-form-urlencoded,application/xml produce:application/json,application/xml)
 http://0.0.0.0:8080/api/v3/user/createWithList        (POST)   (accept:application/json produce:application/json,application/xml)
 http://0.0.0.0:8080/api/v3/user/login                 (GET)    (produce:application/json,application/xml)
 http://0.0.0.0:8080/api/v3/user/logout                (GET)
 http://0.0.0.0:8080/api/v3/user/{username}            (DELETE,GET,PUT)
```
:::
::::

::: paragraph
These APIs are outputted using the URI that clients can use to call the
service. Each of these APIs has a unique *operation id* which is what
Camel uses for calling the route. This gives:
:::

:::: listingblock
::: content
``` highlight
 http://0.0.0.0:8080/api/v3/user                       direct:createUser
 http://0.0.0.0:8080/api/v3/user/createWithList        direct:createUsersWithListInput
 http://0.0.0.0:8080/api/v3/user/login                 direct:loginUser
 http://0.0.0.0:8080/api/v3/user/logout                direct:logoutUser
 http://0.0.0.0:8080/api/v3/user/{username}            direct:getUserByName
```
:::
::::

::: paragraph
You should then implement a route for each API that starts from those
direct endpoints listed above, such as:
:::

::::: exampleblock
:::: content
::: dlist

Java

:   :::: listingblock
    ::: content
    ``` highlight
    @Override
    public void configure() throws Exception {
        rest().openApi("petstore-v3.json");

        from("direct:getUserByName")
           ... // do something here
    }
    ```
    :::
    ::::

XML

:   :::: listingblock
    ::: content
    ``` highlight
    <rest>
      <openApi specification="petstore-v3.json"/>
    </rest>
    <route>
      <from uri="direct:getUserByName"/>
      // do something here
    </route>
    ```
    :::
    ::::

YAML

:   :::: listingblock
    ::: content
    ``` highlight
    - rest:
        openApi:
          specification: petstore-v3.json
    - route:
        from:
          uri: direct:getUserByName
          steps:
            - log:
                message: "do something here"
    ```
    :::
    ::::
:::
::::
:::::

:::::::: sect2
### Ignoring missing API operations {#_ignoring_missing_api_operations}

::: paragraph
When using OpenAPI with *contract-first*, then Camel will on startup
check if there is a corresponding `direct:operationId` route for every
API service. If some operations are missing, then Camel will fail on
startup with an error.
:::

::: paragraph
During development, you can use `missingOperation` to ignore this as
shown:
:::

:::: listingblock
::: content
``` highlight
    rest().openApi("petstore-v3.json").missingOperation("ignore");
```
:::
::::

::: paragraph
This allows you to implement the APIs one by one over time.
:::
::::::::

::::::::::::::::::::::: sect2
### Mocking API operations {#_mocking_api_operations}

::: paragraph
This is similar to ignoring missing API operations, as you can tell
Camel to mock instead, as shown:
:::

:::: listingblock
::: content
``` highlight
    rest().openApi("petstore-v3.json").missingOperation("mock");
```
:::
::::

::: paragraph
When using *mock*, then Camel will (for missing operations) simulate a
successful response:
:::

::: {.olist .arabic}
1.  attempting to load canned responses from the file system.

2.  for GET verbs then attempt to use example inlined in the OpenAPI
    `response` section.

3.  for other verbs (DELETE, PUT, POST, PATCH) then return the input
    body as response.

4.  if none of the above, then return empty body.
:::

::: paragraph
This allows you to have a set of files that you can use for development
and testing purposes.
:::

::: paragraph
The files should be stored in `camel-mock` when using Camel JBang, and
`src/main/resources/camel-mock` for Maven/Gradle based projects.
:::

::: paragraph
For example, the following [Camel JBang
example](https://github.com/apache/camel-kamelets-examples/tree/main/jbang/open-api-contract-first)
is structured as:
:::

:::: listingblock
::: content
``` highlight
README.md
camel-mock/pet/123.json
petstore-v3.json
petstore.camel.yaml
```
:::
::::

::: paragraph
And the Camel route:
:::

:::: listingblock
::: content
``` highlight
- restConfiguration:
    clientRequestValidation: true
- rest:
    openApi:
      missingOperation: mock
      specification: petstore-v3.json
```
:::
::::

::: paragraph
When running this example, you can call the APIs and have an empty
successful response. However, for the url `pet/123` the file
`camel-mock/pet/123.json` will be loaded as the response as shown below:
:::

:::: listingblock
::: content
``` highlight
$ curl http://0.0.0.0:8080/api/v3/pet/123
{
  "pet": "donald the dock"
}
```
:::
::::

::: paragraph
If no file is found, then Camel will attempt to find an example from the
*response* section in the OpenAPI specification.
:::

::: paragraph
In the response section below, then for success GET response (200) then
for the `application/json` content-type, we have an inlined example.
Note if there are multiple examples for the same content-type, then
Camel will pick the first example, so make sure it's the best example
you want to let Camel use as mocked response body.
:::

:::: listingblock
::: content
``` highlight
"responses": {
    "200": {
        "description": "successful operation",
        "content": {
            "application/xml": {
                "schema": {
                    "$ref": "#/components/schemas/Pet"
                }
            },
            "application/json": {
                "schema": {
                    "$ref": "#/components/schemas/Pet"
                },
                "examples": {
                    "success": {
                        "summary": "A cat",
                        "value": "{\"pet\": \"Jack the cat\"}"
                    }
                }
            }
        }
    },
    "400": {
        "description": "Invalid ID supplied"
    },
    "404": {
        "description": "Pet not found"
    }
```
:::
::::
:::::::::::::::::::::::

:::::::::::::::::::::::: sect2
### Binding to POJO classes {#_binding_to_pojo_classes}

::: paragraph
*contract-first* Rest DSL with OpenAPI also supports binding mode to
JSON and XML. This works the same as *code first* [Rest
DSL](rest-dsl.html).
:::

::: paragraph
However, we have added the `bindingPackageScan` configuration to make it
possible for Camel to automatically discover POJO classes from
classpath.
:::

::: paragraph
When using Spring Boot or Quarkus, then you must configure the package
names (base) such as follows:
:::

:::: listingblock
::: content
``` highlight
// turn on json binding and scan for POJO classes in the model package
restConfiguration().bindingMode(RestBindingMode.json)
        .bindingPackageScan("sample.petstore.model");
```
:::
::::

::: paragraph
You can also configure this in `application.properties`:
:::

:::: listingblock
::: content
``` highlight
camel.rest.bindingMode = json
camel.rest.bindingPackageScan = sample.petstore.model
```
:::
::::

::: paragraph
Then Camel will automatically for every OpenAPI operation detect the
specified schemas for incoming and outgoing responses, and map that to
Java POJO classes by class name.
:::

::: paragraph
For example, the `getPetById` operation in the OpenAPI contract:
:::

:::: listingblock
::: content
``` highlight
"responses": {
    "200": {
        "description": "successful operation",
        "content": {
            "application/xml": {
                "schema": {
                    "$ref": "#/components/schemas/Pet"
                }
            },
            "application/json": {
                "schema": {
                    "$ref": "#/components/schemas/Pet"
                }
            }
        }
    },
```
:::
::::

::: paragraph
Here Camel will detect the `schema` part:
:::

:::: listingblock
::: content
``` highlight
"schema": {
    "$ref": "#/components/schemas/Pet"
}
```
:::
::::

::: paragraph
And compute the class name as `Pet` and attempt to discover this class
from classpath scanning specified via the `bindingPackageScan` option.
:::

::: paragraph
You can also use `title` attribute of the Schema to provide the name of
the POJO class. This is helpful when you need to use one name for the
Schema in the OpenAPI contract and use another name for the actual POJO
class in the implementation.
:::

:::: listingblock
::: content
``` highlight
"components": {
        "schemas": {
            "Pet": {
                "type": "object",
                "title": "PetResponseDto",
                "properties": {
                    ...
                }
            }
        }
    },
```
:::
::::

::: paragraph
Here Camel will detect the class name as `PetResponseDto` and try to
discover it from the classpath. This can be used for both Responses and
RequestBodies.
:::

::: paragraph
You can source code generate Java POJO classes from an OpenAPI
specification via tooling such as the `swagger-codegen-maven-plugin`
Maven plugin. For more details, see this [Spring Boot
example](https://github.com/apache/camel-spring-boot-examples/tree/main/openapi-contract-first).
:::
::::::::::::::::::::::::

::::::::::::: sect2
### Expose API specification {#_expose_api_specification}

::: paragraph
The OpenAPI specification is by default not exposed on the HTTP
endpoint. You can make this happen by setting the rest-configuration as
follows:
:::

:::: listingblock
::: content
``` highlight
- restConfiguration:
    apiContextPath: /api-doc
```
:::
::::

::: paragraph
Then the specification is accessible on `/api-doc` on the embedded HTTP
server, so typically that would be
[`http://localhost:8080/api-doc`](http://localhost:8080/api-doc){.bare}.
:::

::: paragraph
In the returned API specification the `server` section has been modified
to return the IP of the current server. This can be controlled via:
:::

:::: listingblock
::: content
``` highlight
- restConfiguration:
    apiContextPath: /api-doc
    hostNameResolver: localIp
```
:::
::::

::: paragraph
And you can turn this off by setting the value to `none` so the server
part is taken verbatim from the specification file.
:::

:::: listingblock
::: content
``` highlight
- restConfiguration:
    apiContextPath: /api-doc
    hostNameResolver: none
```
:::
::::
:::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::::: sect1
## Examples {#_examples}

::::: sectionbody
::: paragraph
You can find a few examples such as:
:::

::: ulist
- [https://github.com/apache/camel-kamelets-examples/tree/main/jbang/open-api-contract-first](https://github.com/apache/camel-kamelets-examples/tree/main/jbang/open-api-contract-first){.bare}

- [https://github.com/apache/camel-spring-boot-examples/tree/main/openapi-contract-first](https://github.com/apache/camel-spring-boot-examples/tree/main/openapi-contract-first){.bare}
:::
:::::
::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
