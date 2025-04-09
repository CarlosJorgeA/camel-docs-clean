::: {#header}
# REST DSL
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
::::::: {#preamble}
:::::: sectionbody
::: paragraph
Apache Camel offers a REST styled DSL.
:::

::: paragraph
The intention is to allow end users to define REST services (hosted by
Camel) using a REST style with verbs such as get, post, delete, etc.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | From **Camel 4.6** onwards, the   |
| Note                              | Rest DSL has been improved with a |
| :::                               | *contract-first* approach using   |
|                                   | vanilla OpenAPI specification     |
|                                   | files. This is documented in the  |
|                                   | [Rest DSL with OpenAPI contract   |
|                                   | first](rest-dsl-openapi.html)     |
|                                   | page. This current page documents |
|                                   | the *code-first* Rest DSL that    |
|                                   | Camel provides for a long time.   |
+-----------------------------------+-----------------------------------+
:::
::::::
:::::::

::::: sect1
## How it works {#_how_it_works}

:::: sectionbody
::: paragraph
The Rest DSL is a facade that builds
[Rest](components::rest-component.html) endpoints as consumers for Camel
routes. The actual REST transport is leveraged by using Camel REST
components such as [Netty HTTP](components::netty-http-component.html),
[Servlet](components::servlet-component.html), and others that have
native REST integration.
:::
::::
:::::

:::::: sect1
## Components supporting Rest DSL {#_components_supporting_rest_dsl}

::::: sectionbody
::: paragraph
The following Camel components support the Rest DSL:
:::

::: ulist
- [camel-rest](components::rest-component.html) **required** contains
  the base rest component needed by Rest DSL

- [camel-netty-http](components::netty-http-component.html)

- [camel-jetty](components::jetty-component.html)

- [camel-platform-http](components::platform-http-component.html)

- [camel-servlet](components::servlet-component.html)

- [camel-undertow](components::undertow-component.html)
:::
:::::
::::::

:::::::::: sect1
## Rest DSL with Java DSL {#_rest_dsl_with_java_dsl}

::::::::: sectionbody
::: paragraph
To use the Rest DSL in Java DSL, then just do as with regular Camel
routes by extending the `RouteBuilder` and define the routes in the
`configure` method.
:::

::: paragraph
A simple REST service can be defined as follows, where we use `rest()`
to define the services as shown below:
:::

:::: listingblock
::: content
``` highlight
@Override
public void configure() throws Exception {
    rest("/say")
        .get("/hello").to("direct:hello")
        .get("/bye").consumes("application/json").to("direct:bye")
        .post("/bye").to("mock:update");

    from("direct:hello")
        .transform().constant("Hello World");

    from("direct:bye")
        .transform().constant("Bye World");
}
```
:::
::::

::: paragraph
This defines a REST service with the following url mappings:
:::

+-----------------+-----------------+-----------------+-----------------+
| Base Path       | Uri template    | Verb            | Consumes        |
+=================+=================+=================+=================+
| *`/say`*        | *`/hello`*      | `get`           | *all*           |
+-----------------+-----------------+-----------------+-----------------+
| *`/say`*        | *`/bye`*        | `get`           | `ap             |
|                 |                 |                 | plication/json` |
+-----------------+-----------------+-----------------+-----------------+
| *`/say`*        | *`/bye`*        | `post`          | *all*           |
+-----------------+-----------------+-----------------+-----------------+

::: paragraph
Notice that in the REST service we route directly to a Camel endpoint
using `to()`. This is because the Rest DSL has a shorthand for routing
directly to an endpoint using `to()`.
:::
:::::::::
::::::::::

::::::: sect1
## Rest DSL with XML DSL {#_rest_dsl_with_xml_dsl}

:::::: sectionbody
::: paragraph
The example above can be defined in XML as shown below:
:::

:::: listingblock
::: content
``` highlight
<camelContext xmlns="http://camel.apache.org/schema/spring">
  <rest path="/say">
    <get path="/hello">
      <to uri="direct:hello"/>
    </get>
    <get path="/bye" consumes="application/json">
      <to uri="direct:bye"/>
    </get>
    <post path="/bye">
      <to uri="mock:update"/>
    </post>
  </rest>
  <route>
    <from uri="direct:hello"/>
    <transform>
      <constant>Hello World</constant>
    </transform>
  </route>
  <route>
    <from uri="direct:bye"/>
    <transform>
      <constant>Bye World</constant>
    </transform>
  </route>
</camelContext>
```
:::
::::
::::::
:::::::

::::::::::::::::: sect1
## Using a base path {#_using_a_base_path}

:::::::::::::::: sectionbody
::: paragraph
The REST DSL allows defining a base path to help applying the *\"don't
repeat yourself\"* (DRY) practice. For example, to define a customer
path, we can set the base path in `rest("/customer")` and then provide
the uri templates in the verbs, as shown below:
:::

:::: listingblock
::: content
``` highlight
rest("/customers/")
    .get("/{id}").to("direct:customerDetail")
    .get("/{id}/orders").to("direct:customerOrders")
    .post("/neworder").to("direct:customerNewOrder");
```
:::
::::

::: paragraph
And using XML DSL, it becomes:
:::

:::: listingblock
::: content
``` highlight
<rest path="/customers/">
  <get path="/{id}">
    <to uri="direct:customerDetail"/>
  </get>
  <get path="/{id}/orders">
    <to uri="direct:customerOrders"/>
  </get>
  <post path="/neworder">
    <to uri="direct:customerNewOrder"/>
  </post>
</rest>
```
:::
::::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | The REST DSL will take care of    |
| Tip                               | duplicate path separators when    |
| :::                               | using base path and uri           |
|                                   | templates. In the example above   |
|                                   | the rest base path ends with a    |
|                                   | slash `/` and the verb starts     |
|                                   | with a slash `/`. Camel will take |
|                                   | care of this and remove the       |
|                                   | duplicated slash.                 |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
It is not required to use both base path and uri templates. You can omit
the base path and define the base path and uri template in the verbs
only. The example above can be defined as:
:::

:::: listingblock
::: content
``` highlight
<rest>
  <get path="/customers/{id}">
    <to uri="direct:customerDetail"/>
  </get>
  <get path="/customers/{id}/orders">
    <to uri="direct:customerOrders"/>
  </get>
  <post path="/customers/neworder">
    <to uri="direct:customerNewOrder"/>
  </post>
</rest>
```
:::
::::

::: paragraph
You can combine path parameters to build complex expressions. For
example:
:::

:::: listingblock
::: content
``` highlight
 rest("items/")
     .get("{id}/{filename}.{content-type}")
     .to("direct:item")
```
:::
::::
::::::::::::::::
:::::::::::::::::

:::::::: sect1
## Managing Rest services {#_managing_rest_services}

::::::: sectionbody
::: paragraph
Each of the rest services becomes a Camel route, so in the first
example, we have 2 x get and 1 x post REST service, which each becomes a
Camel route. This makes it *the same* from Apache Camel to manage and
run these services, as they are just Camel routes. This means any
tooling and API today that deals with Camel routes, also work with the
REST services.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | To use JMX with Camel then        |
| Note                              | `camel-management` JAR must be    |
| :::                               | included in the classpath.        |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
This means you can use JMX to stop/start routes, and also get the JMX
metrics about the routes, such as the number of messages processed, and
their performance statistics.
:::

::: paragraph
There is also a Rest Registry JMX MBean that contains a registry of all
REST services that has been defined.
:::
:::::::
::::::::

::::::::::::::::: sect1
## Inline Rest DSL as a single route {#_inline_rest_dsl_as_a_single_route}

:::::::::::::::: sectionbody
::: {.admonitionblock .important}
+-----------------------------------+-----------------------------------+
| ::: title                         | Camel 4.4 or older has            |
| Important                         | inline-routes disabled by         |
| :::                               | default. Camel 4.5 or newer has   |
|                                   | inline-routes enabled by default. |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
Each of the rest services becomes a Camel route, and this means, that if
the rest service is calling another Camel route via `direct`, which is a
widespread practice. This means that each rest service then becomes two
routes. This can become harder to manage if you have many rest services.
:::

::: paragraph
When you use `direct` endpoints then you can enable Rest DSL to
automatically *inline* the direct route in the rest route, meaning that
there is only one route per rest service.
:::

::: paragraph
To do this you **MUST** use `direct` endpoints, and each endpoint must
be unique name per service. And the option `inlineRoutes` must be
enabled.
:::

::: paragraph
For example, in the Java DSL below we have enabled inline routes and
each rest service uses `direct` endpoints with unique names.
:::

:::: listingblock
::: content
``` highlight
restConfiguration().inlineRoutes(true);

rest("/customers/")
    .get("/{id}").to("direct:customerDetail")
    .get("/{id}/orders").to("direct:customerOrders")
    .post("/neworder").to("direct:customerNewOrder");
```
:::
::::

::: paragraph
And in XML:
:::

:::: listingblock
::: content
``` highlight
<restConfiguration inlineRoutes="true"/>

<rest>
  <get path="/customers/{id}">
    <to uri="direct:customerDetail"/>
  </get>
  <get path="/customers/{id}/orders">
    <to uri="direct:customerOrders"/>
  </get>
  <post path="/customers/neworder">
    <to uri="direct:customerNewOrder"/>
  </post>
</rest>
```
:::
::::

::: paragraph
If you use Camel Main, Camel Spring Boot, Camel Quarkus or Camel JBang,
you can also enable this in `application.properties` such as:
:::

:::: listingblock
::: content
``` highlight
camel.rest.inline-routes = true
```
:::
::::
::::::::::::::::
:::::::::::::::::

::::::::::: sect1
## Disabling REST services {#_disabling_rest_services}

:::::::::: sectionbody
::: paragraph
While developing REST services using Rest DSL, you may want to temporary
disabled some REST endpoints, which you can do using `disabled` as shown
in the following.
:::

:::: listingblock
::: content
``` highlight
rest("/customers/")
    .get("/{id}").to("direct:customerDetail")
    .get("/{id}/orders").to("direct:customerOrders").disabled("{{ordersEnabled}}")
    .post("/neworder").to("direct:customerNewOrder").disabled();
```
:::
::::

::: paragraph
And in XML:
:::

:::: listingblock
::: content
``` highlight
<rest>
  <get path="/customers/{id}">
    <to uri="direct:customerDetail"/>
  </get>
  <get path="/customers/{id}/orders" disabled="{{ordersEnabled}}">
    <to uri="direct:customerOrders"/>
  </get>
  <post path="/customers/neworder" disabled="true">
    <to uri="direct:customerNewOrder"/>
  </post>
</rest>
```
:::
::::

::: paragraph
In this example the last two REST endpoints are configured with
`disabled`. You can use [Property
Placeholder](manual:ROOT:using-propertyplaceholder.html) to let an
external configuration determine if the REST endpoint is disabled or
not. In this example the `/customers/{id}/orders` endpoint is disabled
via a placeholder. The last REST endpoint is hardcoded to be disabled.
:::
::::::::::
:::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Binding to POJOs using {#_binding_to_pojos_using}

:::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
The Rest DSL supports automatic binding json/xml contents to/from POJOs
using data formats. By default, the binding mode is off, meaning there
is no automatic binding happening for incoming and outgoing messages.
:::

::: paragraph
You may want to use binding if you develop POJOs that maps to your REST
services request and response types. This allows you as a developer to
work with the POJOs in Java code.
:::

::: paragraph
The binding modes are:
:::

+------+---------------------------------------------------------------+
| Bin  | Description                                                   |
| ding |                                                               |
| Mode |                                                               |
+======+===============================================================+
| `    | Binding is turned off. This is the default option.            |
| off` |                                                               |
+------+---------------------------------------------------------------+
| `a   | Binding is enabled, and Camel is relaxed and supports JSON,   |
| uto` | XML or both if the necessary data formats are included in the |
|      | classpath. Notice that if for example `camel-jaxb` is not on  |
|      | the classpath, then XML binding is not enabled.               |
+------+---------------------------------------------------------------+
| `j   | Binding to/from JSON is enabled, and requires a JSON capable  |
| son` | data format on the classpath. By default, Camel will use      |
|      | `jackson` as the data format.                                 |
+------+---------------------------------------------------------------+
| `xm` | Binding to/from XML is enabled, and requires `camel-jaxb` on  |
|      | the classpath.                                                |
+------+---------------------------------------------------------------+
| `j   | Binding to/from JSON and XML is enabled and requires both     |
| son_ | data formats to be on the classpath.                          |
| xml` |                                                               |
+------+---------------------------------------------------------------+

::: paragraph
When using camel-jaxb for XML bindings, then you can use the option
`mustBeJAXBElement` to relax the output message body must be a class
with JAXB annotations. You can use this in situations where the message
body is already in XML format, and you want to use the message body
as-is as the output type. If that is the case, then set the
dataFormatProperty option `mustBeJAXBElement` to `false` value.
:::

::: paragraph
The binding from POJO to JSon/JAXB will only happen if the
`content-type` header includes the word `json` or `xml`
representatively. This allows you to specify a custom content-type if
the message body should not attempt to be marshalled using the binding.
For example, if the message body is a custom binary payload, etc.
:::

::: paragraph
When automatic binding from POJO to JSON/JAXB takes place the existing
`content-type` header will by default be replaced with either
`application/json` or `application/xml`. To disable the default behavior
and be able to produce JSON/JAXB responses with custom `content-type`
headers (e.g. `application/user.v2+json`) you configure this in Java DSL
as shown below:
:::

:::: listingblock
::: content
``` highlight
restConfiguration().dataFormatProperty("contentTypeHeader", "false");
```
:::
::::

::: paragraph
To use binding you must include the necessary data formats on the
classpath, such as `camel-jaxb` and/or `camel-jackson`. And then enable
the binding mode. You can configure the binding mode globally on the
rest configuration, and then override per rest service as well.
:::

::: paragraph
To enable binding, you configure this in Java DSL as shown below:
:::

:::: listingblock
::: content
``` highlight
restConfiguration().component("netty-http").host("localhost").port(portNum).bindingMode(RestBindingMode.auto);
```
:::
::::

::: paragraph
And in XML DSL:
:::

:::: listingblock
::: content
``` highlight
<restConfiguration bindingMode="auto" component="netty-http" port="8080"/>
```
:::
::::

::: paragraph
When binding is enabled, Camel will bind the incoming and outgoing
messages automatic, accordingly to the content type of the message. If
the message is JSON, then JSON binding happens; and so if the message is
XML, then XML binding happens. The binding happens for incoming and
reply messages. The table below summaries what binding occurs for
incoming and reply messages.
:::

+-----------------+-----------------+-----------------+-----------------+
| Message Body    | Direction       | Binding Mode    | Message Body    |
+=================+=================+=================+=================+
| XML             | Incoming        | au              | POJO            |
|                 |                 | to,xml,json_xml |                 |
+-----------------+-----------------+-----------------+-----------------+
| POJO            | Outgoing        | auto,xml,       | XML             |
|                 |                 | json_xml        |                 |
+-----------------+-----------------+-----------------+-----------------+
| JSON            | Incoming        | aut             | POJO            |
|                 |                 | o,json,json_xml |                 |
+-----------------+-----------------+-----------------+-----------------+
| POJO            | Outgoing        | aut             | JSON            |
|                 |                 | o,json,json_xml |                 |
+-----------------+-----------------+-----------------+-----------------+

::: paragraph
When using binding, you must also configure what POJO type to map to.
This is mandatory for incoming messages, and optional for outgoing.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | When using binding mode `json`,   |
| Note                              | `xml` or `json_xml` then Camel    |
| :::                               | will automatically set            |
|                                   | `consumers` and `produces` on the |
|                                   | rest endpoint (according to the   |
|                                   | mode), if not already explicit    |
|                                   | configured. For example, with     |
|                                   | binding mode `json` and setting   |
|                                   | the outType as `UserPojo` then    |
|                                   | Camel will define this rest       |
|                                   | endpoint as producing             |
|                                   | `application/json`.               |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
For example, to map from xml/json to a pojo class `UserPojo` you do this
in Java DSL as shown below:
:::

:::: listingblock
::: content
``` highlight
// configure to use netty-http on localhost with the given port
// and enable auto binding mode
restConfiguration().component("netty-http").host("localhost").port(portNum).bindingMode(RestBindingMode.auto);

// use the rest DSL to define the rest services
rest("/users/")
    .post().type(UserPojo.class)
        .to("direct:newUser");
```
:::
::::

::: paragraph
Notice we use `type` to define the incoming type. We can optionally
define an outgoing type (which can be a good idea, to make it known from
the DSL and also for tooling and JMX APIs to know both the incoming and
outgoing types of the REST services). To define the outgoing type, we
use `outType` as shown below:
:::

:::: listingblock
::: content
``` highlight
// configure to use netty-http on localhost with the given port
// and enable auto binding mode
restConfiguration().component("netty-http").host("localhost").port(portNum).bindingMode(RestBindingMode.auto);

// use the rest DSL to define the rest services
rest("/users/")
    .post().type(UserPojo.class).outType(CountryPojo.class)
        .to("direct:newUser");
```
:::
::::

::: paragraph
And in XML DSL:
:::

:::: listingblock
::: content
``` highlight
<rest path="/users/">
  <post type="UserPojo" outType="CountryPojo">
    <to uri="direct:newUser"/>
  </post>
</rest>
```
:::
::::

::: paragraph
To specify input and/or output using an array, append `[]` to the end of
the canonical class name as shown in the following Java DSL:
:::

:::: listingblock
::: content
``` highlight
// configure to use netty-http on localhost with the given port
// and enable auto binding mode
restConfiguration().component("netty-http").host("localhost").port(portNum).bindingMode(RestBindingMode.auto);

// use the rest DSL to define the rest services
rest("/users/")
    .post().type(UserPojo[].class).outType(CountryPojo[].class)
        .to("direct:newUser");
```
:::
::::

::: paragraph
The `UserPojo` is just a plain pojo with getter/setter as shown:
:::

:::: listingblock
::: content
``` highlight
public class UserPojo {
    private int id;
    private String name;
    public int getId() {
        return id;
    }
    public void setId(int id) {
        this.id = id;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
}
```
:::
::::

::: paragraph
The `UserPojo` only supports JSON, as XML requires using JAXB
annotations, so we can add those annotations if we want to support XML
also
:::

:::: listingblock
::: content
``` highlight
@XmlRootElement(name = "user")
@XmlAccessorType(XmlAccessType.FIELD)
public class UserPojo {
    @XmlAttribute
    private int id;
    @XmlAttribute
    private String name;
    public int getId() {
        return id;
    }
    public void setId(int id) {
        this.id = id;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
}
```
:::
::::

::: paragraph
By having the JAXB annotations, the POJO supports both JSON and XML
bindings.
:::

:::::::::::::: sect2
### Camel Rest-DSL configurations {#_camel_rest_dsl_configurations}

::: paragraph
The Rest DSL supports the following options:
:::

+-------------+-----------------------------------+------+-------------+
| Name        | Description                       | Def  | Type        |
|             |                                   | ault |             |
+=============+===================================+======+=============+
| **api       | Sets the name of the Camel        |      | String      |
| Component** | component to use as the REST API  |      |             |
|             | (such as swagger or openapi)      |      |             |
+-------------+-----------------------------------+------+-------------+
| **apiCo     | Sets a leading API context-path   |      | String      |
| ntextPath** | the REST API services will be     |      |             |
|             | using. This can be used when      |      |             |
|             | using components such as          |      |             |
|             | camel-servlet where the deployed  |      |             |
|             | web application is deployed using |      |             |
|             | a context-path.                   |      |             |
+-------------+-----------------------------------+------+-------------+
| **apiHost** | To use a specific hostname for    |      | String      |
|             | the API documentation (such as    |      |             |
|             | swagger or openapi) This can be   |      |             |
|             | used to override the generated    |      |             |
|             | host with this configured         |      |             |
|             | hostname                          |      |             |
+-------------+-----------------------------------+------+-------------+
| **apiP      | Sets additional options on api    |      | Map         |
| roperties** | level                             |      |             |
+-------------+-----------------------------------+------+-------------+
| **apiVendor | Whether a vendor extension is     | f    | boolean     |
| Extension** | enabled in the Rest APIs. If      | alse |             |
|             | enabled, then Camel will include  |      |             |
|             | additional information as a       |      |             |
|             | vendor extension (e.g., keys      |      |             |
|             | starting with *`x-`*) such as     |      |             |
|             | route ids, class names etc. Not   |      |             |
|             | all third party API gateways and  |      |             |
|             | tools support vendor-extensions   |      |             |
|             | when importing your API docs.     |      |             |
+-------------+-----------------------------------+------+-------------+
| **bi        | Sets the binding mode to be used  | Res  | Rest        |
| ndingMode** | by the REST consumer              | tBin | BindingMode |
|             |                                   | ding |             |
|             |                                   | Mode |             |
|             |                                   | .off |             |
+-------------+-----------------------------------+------+-------------+
| **cli       | Whether to enable validation of   | f    | boolean     |
| entRequestV | the client request to check: 1)   | alse |             |
| alidation** | Content-Type header matches what  |      |             |
|             | the Rest DSL consumes; returns    |      |             |
|             | HTTP Status 415 if validation     |      |             |
|             | error. 2) Accept header matches   |      |             |
|             | what the Rest DSL produces;       |      |             |
|             | returns HTTP Status 406 if        |      |             |
|             | validation error. 3) Missing      |      |             |
|             | required data (query parameters,  |      |             |
|             | HTTP headers, body); returns HTTP |      |             |
|             | Status 400 if validation error.   |      |             |
|             | 4) Parsing error of the message   |      |             |
|             | body (JSON, XML or Auto binding   |      |             |
|             | mode must be enabled); returns    |      |             |
|             | HTTP Status 400 if validation     |      |             |
|             | error.                            |      |             |
+-------------+-----------------------------------+------+-------------+
| **          | Sets the name of the Camel        |      | String      |
| component** | component to use as the REST      |      |             |
|             | consumer                          |      |             |
+-------------+-----------------------------------+------+-------------+
| *           | Sets additional options on        |      | Map         |
| *componentP | component level                   |      |             |
| roperties** |                                   |      |             |
+-------------+-----------------------------------+------+-------------+
| **consumerP | Sets additional options on        |      | Map         |
| roperties** | consumer level                    |      |             |
+-------------+-----------------------------------+------+-------------+
| **co        | Sets a leading context-path the   |      | String      |
| ntextPath** | REST services will be using. This |      |             |
|             | can be used when using components |      |             |
|             | such as camel-servlet where the   |      |             |
|             | deployed web application is       |      |             |
|             | deployed using a context-path. Or |      |             |
|             | for components such as            |      |             |
|             | camel-jetty or camel-netty-http   |      |             |
|             | that includes a HTTP server.      |      |             |
+-------------+-----------------------------------+------+-------------+
| **co        | Sets the CORS headers to use if   |      | Map         |
| rsHeaders** | CORS has been enabled.            |      |             |
+-------------+-----------------------------------+------+-------------+
| **          | Sets additional options on data   |      | Map         |
| dataFormatP | format level                      |      |             |
| roperties** |                                   |      |             |
+-------------+-----------------------------------+------+-------------+
| **e         | To specify whether to enable      | f    | boolean     |
| nableCORS** | CORS, which means Camel will      | alse |             |
|             | automatically include CORS in the |      |             |
|             | HTTP headers in the response.     |      |             |
|             | This option is default false      |      |             |
+-------------+-----------------------------------+------+-------------+
| **ena       | To specify whether to return HTTP | f    | boolean     |
| bleNoConten | 204 with an empty body when a     | alse |             |
| tResponse** | response contains an empty JSON   |      |             |
|             | object or XML root object.        |      |             |
+-------------+-----------------------------------+------+-------------+
| **endpointP | Sets additional options on        |      | Map         |
| roperties** | endpoint level                    |      |             |
+-------------+-----------------------------------+------+-------------+
| **host**    | Sets the hostname to use by the   |      | String      |
|             | REST consumer                     |      |             |
+-------------+-----------------------------------+------+-------------+
| **hostNam   | Sets the resolver to use for      | Res  | RestHostN   |
| eResolver** | resolving hostname                | tHos | ameResolver |
|             |                                   | tNam |             |
|             |                                   | eRes |             |
|             |                                   | olve |             |
|             |                                   | r.al |             |
|             |                                   | lLoc |             |
|             |                                   | alIp |             |
+-------------+-----------------------------------+------+-------------+
| **inl       | Inline routes in rest-dsl which   | f    | boolean     |
| ineRoutes** | are linked using direct           | alse |             |
|             | endpoints. By default, each       |      |             |
|             | service in Rest DSL is an         |      |             |
|             | individual route, meaning that    |      |             |
|             | you would have at least two       |      |             |
|             | routes per service (rest-dsl, and |      |             |
|             | the route linked from rest-dsl).  |      |             |
|             | Enabling this allows Camel to     |      |             |
|             | optimize and inline this as a     |      |             |
|             | single route. However, this       |      |             |
|             | requires using direct endpoints,  |      |             |
|             | which must be unique per service. |      |             |
|             | This option is default false.     |      |             |
+-------------+-----------------------------------+------+-------------+
| **jsonD     | Sets a custom JSON data format to |      | String      |
| ataFormat** | be used Important: This option is |      |             |
|             | only for setting a custom name of |      |             |
|             | the data format, not to refer to  |      |             |
|             | an existing data format instance. |      |             |
+-------------+-----------------------------------+------+-------------+
| **port**    | Sets the port to use by the REST  |      | int         |
|             | consumer                          |      |             |
+-------------+-----------------------------------+------+-------------+
| **produ     | Sets the location of the api      |      | String      |
| cerApiDoc** | document (swagger api) the REST   |      |             |
|             | producer will use to validate the |      |             |
|             | REST uri and query parameters are |      |             |
|             | valid accordingly to the api      |      |             |
|             | document. This requires adding    |      |             |
|             | camel-openapi-java to the         |      |             |
|             | classpath, and any miss           |      |             |
|             | configuration will let Camel fail |      |             |
|             | on startup and report the         |      |             |
|             | error(s). The location of the api |      |             |
|             | document is loaded from classpath |      |             |
|             | by default, but you can use file: |      |             |
|             | or http: to refer to resources to |      |             |
|             | load from file or http url.       |      |             |
+-------------+-----------------------------------+------+-------------+
| **producer  | Sets the name of the Camel        |      | String      |
| Component** | component to use as the REST      |      |             |
|             | producer                          |      |             |
+-------------+-----------------------------------+------+-------------+
| **scheme**  | Sets the scheme to use by the     |      | String      |
|             | REST consumer                     |      |             |
+-------------+-----------------------------------+------+-------------+
| **sk        | Whether to skip binding output if | true | boolean     |
| ipBindingOn | there is a custom HTTP error      |      |             |
| ErrorCode** | code, and instead use the         |      |             |
|             | response body as-is. This option  |      |             |
|             | is default true.                  |      |             |
+-------------+-----------------------------------+------+-------------+
| **useXForwa | Whether to use X-Forward headers  | true | boolean     |
| rdHeaders** | to set host etc. for Swagger.     |      |             |
|             | This option is default true.      |      |             |
+-------------+-----------------------------------+------+-------------+
| **xmlD      | Sets a custom XML data format to  |      | String      |
| ataFormat** | be used. Important: This option   |      |             |
|             | is only for setting a custom name |      |             |
|             | of the data format, not to refer  |      |             |
|             | to an existing data format        |      |             |
|             | instance.                         |      |             |
+-------------+-----------------------------------+------+-------------+

::: paragraph
For example, to configure to use the jetty component on port 9091, then
we can do as follows:
:::

:::: listingblock
::: content
``` highlight
restConfiguration().component("jetty").port(9091).componentProperty("foo", "123");
```
:::
::::

::: paragraph
And with XML DSL:
:::

:::: listingblock
::: content
``` highlight
<restConfiguration component="jetty" port="9091">
  <componentProperty key="foo" value="123"/>
</restConfiguration>
```
:::
::::

::: paragraph
If no component has been explicitly configured, then Camel will look up
if there is a Camel component that integrates with the Rest DSL, or if a
`org.apache.camel.spi.RestConsumerFactory` is registered in the
registry. If either one is found, then that is being used.
:::

::: paragraph
You can configure properties on these levels.
:::

::: ulist
- component - Is used to set any options on the Component class. You can
  also configure these directly on the component.

- endpoint - Is used set any option on the endpoint level. Many of the
  Camel components has many options you can set on endpoint level.

- consumer - Is used to set any option on the consumer level.

- data format - Is used to set any option on the data formats. For
  example, to enable pretty print in the JSON data format.

- cors headers - If cors is enabled, then custom CORS headers can be
  set. See below for the default values which are in used. If a custom
  header is set then that value takes precedence over the default value.
:::

::: paragraph
You can set multiple options of the same level, so you can, for example,
configure two component options, and three endpoint options, etc.
:::
::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::: sect1
## Enabling or disabling Jackson JSON features {#_enabling_or_disabling_jackson_json_features}

::::::::::::::::: sectionbody
::: paragraph
When using JSON binding, you may want to turn specific Jackson features
on or off. For example, to disable failing on unknown properties (e.g.,
JSON input has a property which cannot be mapped to a POJO) then
configure this using the `dataFormatProperty` as shown below:
:::

:::: listingblock
::: content
``` highlight
restConfiguration().component("jetty").host("localhost").port(getPort()).bindingMode(RestBindingMode.json)
   .dataFormatProperty("json.in.disableFeatures", "FAIL_ON_UNKNOWN_PROPERTIES");
```
:::
::::

::: paragraph
You can disable more features by separating the values using comma, such
as:
:::

:::: listingblock
::: content
``` highlight
.dataFormatProperty("json.in.disableFeatures", "FAIL_ON_UNKNOWN_PROPERTIES,ADJUST_DATES_TO_CONTEXT_TIME_ZONE");
```
:::
::::

::: paragraph
Likewise, you can enable features using the enableFeatures such as:
:::

:::: listingblock
::: content
``` highlight
restConfiguration().component("jetty").host("localhost").port(getPort()).bindingMode(RestBindingMode.json)
   .dataFormatProperty("json.in.disableFeatures", "FAIL_ON_UNKNOWN_PROPERTIES,ADJUST_DATES_TO_CONTEXT_TIME_ZONE")
   .dataFormatProperty("json.in.enableFeatures", "FAIL_ON_NUMBERS_FOR_ENUMS,USE_BIG_DECIMAL_FOR_FLOATS");
```
:::
::::

::: paragraph
The values that can be used for enabling and disabling features on
Jackson are the names of the enums from the following three Jackson
classes
:::

::: ulist
- `com.fasterxml.jackson.databind.SerializationFeature`

- `com.fasterxml.jackson.databind.DeserializationFeature`

- `com.fasterxml.jackson.databind.MapperFeature`
:::

::: paragraph
The rest configuration is, of course, also possible using XML DSL:
:::

:::: listingblock
::: content
``` highlight
<restConfiguration component="jetty" host="localhost" port="9090" bindingMode="json">
  <dataFormatProperty key="json.in.disableFeatures" value="FAIL_ON_UNKNOWN_PROPERTIES,ADJUST_DATES_TO_CONTEXT_TIME_ZONE"/>
  <dataFormatProperty key="json.in.enableFeatures" value="FAIL_ON_NUMBERS_FOR_ENUMS,USE_BIG_DECIMAL_FOR_FLOATS"/>
</restConfiguration>
```
:::
::::
:::::::::::::::::
::::::::::::::::::

::::: sect1
## Default CORS headers {#_default_cors_headers}

:::: sectionbody
::: paragraph
If CORS is enabled, then the *\"follow headers\"* is in use by default.
You can configure custom CORS headers that take precedence over the
default value.
:::

+-----------------------------------+-----------------------------------+
| Key                               | Value                             |
+===================================+===================================+
| `Access-Control-Allow-Origin`     | \*                                |
+-----------------------------------+-----------------------------------+
| `Access-Control-Allow-Methods`    | GET, HEAD, POST, PUT, DELETE,     |
|                                   | TRACE, OPTIONS, CONNECT, PATCH    |
+-----------------------------------+-----------------------------------+
| `Access-Control-Allow-Headers`    | Origin, Accept, X-Requested-With, |
|                                   | Content-Type,                     |
|                                   | Access-Control-Request-Method,    |
|                                   | Access-Control-Request-Headers    |
+-----------------------------------+-----------------------------------+
| `Access-Control-Max-Age`          | 3600                              |
+-----------------------------------+-----------------------------------+
::::
:::::

::::::::::::::: sect1
## Defining a custom error message as-is {#_defining_a_custom_error_message_as_is}

:::::::::::::: sectionbody
::: paragraph
If you want to define custom error messages to be sent back to the
client with a HTTP error code (e.g., such as 400, 404 etc.) then you set
a header with the key `Exchange.HTTP_RESPONSE_CODE` to the error code
(must be 300+) such as 404. And then the message body with any reply
message, and optionally set the content-type header as well. There is a
little example shown below:
:::

:::: listingblock
::: content
``` highlight
restConfiguration().component("netty-http").host("localhost").port(portNum).bindingMode(RestBindingMode.json);
// use the rest DSL to define the rest services
rest("/users/")
    .post("lives").type(UserPojo.class).outType(CountryPojo.class)
    .to("direct:users-lives");

from("direct:users-lives")
    .choice()
        .when().simple("${body.id} < 100")
            .bean(new UserErrorService(), "idToLowError")
        .otherwise()
            .bean(new UserService(), "livesWhere");
```
:::
::::

::: paragraph
In this example, if the input id is a number that is below 100, we want
to send back a custom error message, using the UserErrorService bean,
which is implemented as shown:
:::

:::: listingblock
::: content
``` highlight
public class UserErrorService {
    public void idToLowError(Exchange exchange) {
        exchange.getIn().setBody("id value is too low");
        exchange.getIn().setHeader(Exchange.CONTENT_TYPE, "text/plain");
        exchange.getIn().setHeader(Exchange.HTTP_RESPONSE_CODE, 400);
    }
}
```
:::
::::

::: paragraph
In the *`UserErrorService`* bean, we build our custom error message, and
set the HTTP error code to 400. This is important, as that tells
rest-dsl that this is a custom error message, and the message should not
use the output pojo binding (e.g., would otherwise bind to
*`CountryPojo`*).
:::

:::::: sect2
### Catching JsonParserException and returning a custom error message {#_catching_jsonparserexception_and_returning_a_custom_error_message}

::: paragraph
You can return a custom message as-is (see previous section). So we can
leverage this with Camel error handler to catch `JsonParserException`,
handle that exception and build our custom response message. For
example, to return a HTTP error code 400 with a hardcoded message, we
can do as shown below:
:::

:::: listingblock
::: content
``` highlight
onException(JsonParseException.class)
    .handled(true)
    .setHeader(Exchange.HTTP_RESPONSE_CODE, constant(400))
    .setHeader(Exchange.CONTENT_TYPE, constant("text/plain"))
    .setBody().constant("Invalid json data");
```
:::
::::
::::::
::::::::::::::
:::::::::::::::

:::::::::: sect1
## Query/Header Parameter default Values {#_queryheader_parameter_default_values}

::::::::: sectionbody
::: paragraph
You can specify default values for parameters in the rest-dsl, such as
the verbose parameter below:
:::

:::: listingblock
::: content
``` highlight
  rest("/customers/")
      .get("/{id}").to("direct:customerDetail")
      .get("/{id}/orders")
        .param().name("verbose").type(RestParamType.query).defaultValue("false").description("Verbose order details").endParam()
          .to("direct:customerOrders")
      .post("/neworder").to("direct:customerNewOrder");
```
:::
::::

::: paragraph
The default value is automatic set as header on the incoming Camel
`Message`. So if the call to `/customers/id/orders` do not include a
query parameter with key `verbose` then Camel will now include a header
with key `verbose` and the value `false` because it was declared as the
default value. This functionality is only applicable for query
parameters. Request headers may also be defaulted in the same way.
:::

:::: listingblock
::: content
``` highlight
  rest("/customers/")
      .get("/{id}").to("direct:customerDetail")
      .get("/{id}/orders")
        .param().name("indicator").type(RestParamType.header).defaultValue("disabled").description("Feature Enabled Indicator").endParam()
          .to("direct:customerOrders")
      .post("/neworder").to("direct:customerNewOrder");
```
:::
::::
:::::::::
::::::::::

:::::::::: sect1
## Client Request Validation {#_client_request_validation}

::::::::: sectionbody
::: paragraph
It is possible to enable validation of the incoming client request. The
validation checks for the following:
:::

::: ulist
- Content-Type header matches what the Rest DSL consumes. (Returns HTTP
  Status 415)

- Accept header matches what the Rest DSL produces. (Returns HTTP Status
  406)

- Missing required data (query parameters, HTTP headers, body). (Returns
  HTTP Status 400)

- Checking if query parameters or HTTP headers has not-allowed values.
  (Returns HTTP Status 400)

- Parsing error of the message body (JSON, XML or Auto binding mode must
  be enabled). (Returns HTTP Status 400)
:::

::: paragraph
If the validation fails, then Rest DSL will return a response with an
HTTP error code.
:::

::: paragraph
The validation is by default turned off (to be backwards compatible). It
can be turned on via `clientRequestValidation` as shown below:
:::

:::: listingblock
::: content
``` highlight
restConfiguration().component("jetty").host("localhost")
    .clientRequestValidation(true);
```
:::
::::
:::::::::
::::::::::

::::::::::::::::::::::::::::: sect1
## OpenAPI / Swagger API {#_openapi_swagger_api}

:::::::::::::::::::::::::::: sectionbody
::: paragraph
The Rest DSL supports OpenAPI and Swagger by the `camel-openapi-java`
modules.
:::

::: paragraph
You can define each parameter fine-grained with details such as name,
description, data type, parameter type and so on, using the `param`. For
example, to define the id path parameter, you can do as shown below:
:::

:::: listingblock
::: content
``` highlight
<!-- this is a rest GET to view an user by the given id -->
<get path="/{id}" outType="org.apache.camel.example.rest.User">
  <description>Find user by id</description>
  <param name="id" type="path" description="The id of the user to get" dataType="int"/>
  <to uri="bean:userService?method=getUser(${header.id})"/>
</get>
```
:::
::::

::: paragraph
And in Java DSL
:::

:::: listingblock
::: content
``` highlight
.get("/{id}").description("Find user by id").outType(User.class)
    .param().name("id").type(path).description("The id of the user to get").dataType("int").endParam()
    .to("bean:userService?method=getUser(${header.id})")
```
:::
::::

::: paragraph
The body parameter type requires to use body as well for the name. For
example, a REST PUT operation to create/update an user could be done as:
:::

:::: listingblock
::: content
``` highlight
<!-- this is a rest PUT to create/update an user -->
<put type="org.apache.camel.example.rest.User">
  <description>Updates or create a user</description>
  <param name="body" type="body" description="The user to update or create"/>
  <to uri="bean:userService?method=updateUser"/>
</put>
```
:::
::::

::: paragraph
And in Java DSL:
:::

:::: listingblock
::: content
``` highlight
.put().description("Updates or create a user").type(User.class)
    .param().name("body").type(body).description("The user to update or create").endParam()
    .to("bean:userService?method=updateUser")
```
:::
::::

:::::::::::: sect2
### Vendor Extensions {#_vendor_extensions}

::: paragraph
The generated API documentation can be configured to include vendor
extensions
([https://swagger.io/specification/#specificationExtensions](https://swagger.io/specification/#specificationExtensions){.bare})
which document the operations and definitions with additional
information, such as class name of model classes, camel context id and
route id's. This information can be very helpful for developers,
especially during troubleshooting. However, at production usage you may
wish to not have this turned on to avoid leaking implementation details
into your API docs.
:::

::: paragraph
The vendor extension information is stored in the API documentation with
keys starting with `x-`.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | Not all third party API gateways  |
| Note                              | and tools support                 |
| :::                               | vendor-extensions when importing  |
|                                   | your API docs.                    |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
The vendor extensions can be turned on `RestConfiguration` via the
`apiVendorExtension` option:
:::

:::: listingblock
::: content
``` highlight
restConfiguration()
    .component("servlet")
    .bindingMode(RestBindingMode.json)
    .dataFormatProperty("prettyPrint", "true")
    .apiContextPath("api-doc")
    .apiVendorExtension(true)
        .apiProperty("api.title", "User API").apiProperty("api.version", "1.0.0")
        .apiProperty("cors", "true");
```
:::
::::

::: paragraph
And in XML DSL:
:::

:::: listingblock
::: content
``` highlight
 <restConfiguration component="servlet" bindingMode="json"
                       apiContextPath="api-docs"
                       apiVendorExtension="true">

      <!-- we want json output in pretty mode -->
      <dataFormatProperty key="prettyPrint" value="true"/>

      <!-- setup swagger api descriptions -->
      <apiProperty key="api.version" value="1.0.0"/>
      <apiProperty key="api.title" value="User API"/>

</restConfiguration>
```
:::
::::
::::::::::::

:::: sect2
### Supported API properties {#_supported_api_properties}

::: paragraph
The following table lists supported API properties and explains their
effect. To set them use `apiProperty(String, String)` in the Java DSL or
`<apiProperty>` when defining the REST API via XML configuration.
Properties in **bold** are required by the OpenAPI 2.0 specification.
Most of the properties affect the OpenAPI [Info
object](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#infoObject),
[License
object](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#licenseObject)
or [Contact
object](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#contact-object).
:::

+-----------------------------------+-----------------------------------+
| Property                          | Description                       |
+-----------------------------------+-----------------------------------+
| **api.version**                   | Version of the API                |
+-----------------------------------+-----------------------------------+
| **api.title**                     | Title of the API                  |
+-----------------------------------+-----------------------------------+
| api.description                   | Description of the API            |
+-----------------------------------+-----------------------------------+
| api.termsOfService                | API Terms of Service of the API   |
+-----------------------------------+-----------------------------------+
| api.license.name                  | License information of the API    |
+-----------------------------------+-----------------------------------+
| api.license.url                   | URL for the License of the API    |
+-----------------------------------+-----------------------------------+
| api.contact.name                  | The identifying name of the       |
|                                   | contact person/organization       |
+-----------------------------------+-----------------------------------+
| api.contact.url                   | The URL pointing to the contact   |
|                                   | information                       |
+-----------------------------------+-----------------------------------+
| api.contact.email                 | The email address of the contact  |
|                                   | person/organization               |
+-----------------------------------+-----------------------------------+
| a                                 | The Content-Type of the served    |
| pi.specification.contentType.json | OpenAPI JSON specification,       |
|                                   | `application/json` by default     |
+-----------------------------------+-----------------------------------+
| a                                 | The Content-Type of the served    |
| pi.specification.contentType.yaml | OpenAPI YAML specification,       |
|                                   | `text/yaml` by default            |
+-----------------------------------+-----------------------------------+
| externalDocs.url                  | The URI for the target            |
|                                   | documentation. This must be in    |
|                                   | the form of a URI                 |
+-----------------------------------+-----------------------------------+
| externalDocs.description          | A description of the target       |
|                                   | documentation                     |
+-----------------------------------+-----------------------------------+
::::
::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
