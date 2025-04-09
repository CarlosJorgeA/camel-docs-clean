::: {#header}
# Apache Camel 4.x Upgrade Guide
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
::::: {#preamble}
:::: sectionbody
::: paragraph
This document is for helping you upgrade your Apache Camel application
from Camel 4.x to 4.y. For example, if you are upgrading Camel 4.0 to
4.2, then you should follow the guides from both 4.0 to 4.1 and 4.1 to
4.2.
:::
::::
:::::

:::::: sect1
## Upgrading from 4.4.4 to 4.4.5 {#_upgrading_from_4_4_4_to_4_4_5}

::::: sectionbody
:::: sect2
### camel-mina {#_camel_mina}

::: paragraph
If using object codec, then you should configure the
`objectCodecPattern` configuration to specify which java classes (FQN)
to allow for Object serialization. You can use `*` to accept all
patterns.
:::
::::
:::::
::::::

::::: sect1
## Upgrading from 4.4.3 to 4.4.4 {#_upgrading_from_4_4_3_to_4_4_4}

:::: sectionbody
::: paragraph
The `camel-opentelemetry` component has had significant bug fixes to
handle span activation/deactivations better when Camel route messages
synchronously and asynchronously. This component should also work better
on Spring Boot.
:::
::::
:::::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Upgrading Camel 4.3 to 4.4 {#_upgrading_camel_4_3_to_4_4}

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::::::::::::::::::::::::::::::::::: sect2
### camel-core {#_camel_core}

::: paragraph
The `org.apache.camel.spi.DataFormat` has changed exception thrown from
`IOException` to `Exception` in the unmarshal(Exchange exchange,
InputStream stream)\` method.
:::

::: paragraph
Removed the deprecated constructor from the internal class
`org.apache.camel.util.StopWatch`. Users of this class are advised to
use the default constructor if necessary.
:::

::: paragraph
The method `getCreated` from the `org.apache.camel.Exchange` is now
deprecated. Access to the time-related information from the exchange
should be done via `getClock`.
:::

::: paragraph
We standardized and consolidated code computing durations so that they
use a monotonic clock.
:::

::: paragraph
Durations and some time-related information were consolidated in a new
internal Clock API.
:::

::: paragraph
The `lookup` method in
`org.apache.camel.component.properties.PropertiesLookup` now has a 2nd
parameter for the default value.
:::

::: paragraph
Some of the Java DSL for `tokenize`, `xmlTokenize`, `xpath`, `xquery`
and `jsonpath` has been removed as part of making the DSL model
consistent.
:::

::: paragraph
Here are a few examples of migration before vs after:
:::

:::: listingblock
::: content
``` highlight
 from("direct:in")
    .choice()
        .when().xpath("/invoice/@orderType = 'premium'", "invoiceDetails")
            .to("mock:premium")
        .when() .xpath("/invoice/@orderType = 'standard'", "invoiceDetails")
            .to("mock:standard")
        .otherwise()
            .to("mock:unknown")
    .end();
```
:::
::::

::: paragraph
You can use the *fluent expression* builder to configure all the
options:
:::

:::: listingblock
::: content
``` highlight
// use fluent builder expression to create the languages
var premium = expression().xpath().expression("/invoice/@orderType = 'premium'").source("header:invoiceDetails").end();
var standard = expression().xpath().expression("/invoice/@orderType = 'standard'").source("header:invoiceDetails").end();

from("direct:in")
    .choice()
        .when(premium)
            .to("mock:premium")
        .when(standard)
            .to("mock:standard")
        .otherwise()
            .to("mock:unknown")
    .end();
```
:::
::::

::: paragraph
In the example above notice how we use `source` to specify the input to
use, in this case a header named invoiceDetails. The `source` can also
be variable, or exchange property.
:::

::: paragraph
And another example with `tokenize`:
:::

:::: listingblock
::: content
``` highlight
from("direct:start")
    .split().tokenize("\r\n|\n", true, 2, "\n", true)
        .log("${body}")
        .to("mock:line");
```
:::
::::

::: paragraph
You can use the *fluent expression* builder to configure all the
options:
:::

:::: listingblock
::: content
``` highlight
// use fluent builder expression to create the languages
var token = expression().tokenize().token("\r\n|\n").regex(true).group(2).groupDelimiter("\n").skipFirst(true).end();

from("direct:start")
    .split(token)
        .log("${body}")
        .to("mock:line");
```
:::
::::

:::::: sect3
#### Languages {#_languages}

::: paragraph
The way languages are created and configured by Camel has been
refactored to be aligned and avoid a thread-safety issues when using
Java DSL. The setter/getter on the `Language` classes for options that
are not general has been removed (such as in `TokenizeLanguage`).
:::

::: paragraph
In XML and YAML DSL the `type` option in `<xquery>` has been renamed to
`resultType` to be aligned with the other languages.
:::

::: paragraph
In XML and YAML DSL The `headerName` on `<xpath>`, `<xquery>` has been
renamed to `source` and you should prefix the value with `header:name`
to declare it is a header, because other kind of sources can be
specified also. The same change has applied to `@XPath` and `@XQuery`
language annotations.
:::
::::::

:::: sect3
#### WireTap EIP {#_wiretap_eip}

::: paragraph
The copied exchange is no longer having exchange property CORRELATION_ID
set that links to the original exchange. The reason is that this link
should only be for EIPs with sub exchanges such as Splitter and
Multicast.
:::
::::

:::::: sect3
#### Throttle EIP {#_throttle_eip}

::: paragraph
Previously, Camel used a throttler based on the total number of requests
over a period of time (\"TotalRequests\" mode). That was the default on
Camel up to version 4.2.0. On Camel 4.3.0 we introduced a new one based
on the number of concurrent requests and replaced the former.
:::

::: paragraph
With Camel 4.4.0 we refactored the [Throttle
EIP](components:eips:throttle-eip.html) implementation so that Camel can
support two different modes of throttling.
:::

::: paragraph
Check the component documentation for details about how to use each
mode.
:::
::::::

:::: sect3
#### MDC logging {#_mdc_logging}

::: paragraph
When using custom MDC keys (need to configure `MDCLoggingKeysPattern`)
then these custom keys are cleared at the end of routing. Also, custom
keys is allowed to be changed during routing, using the
`MDC.set(myKey, …​)` Java API.
:::
::::
:::::::::::::::::::::::::::::::::::

:::: sect2
### camel-main {#_camel_main}

::: paragraph
The route controller configuration has been moved from general main to
its own group. All keys started with `camel.main.routesController`
should be renamed to `camel.routecontroller.`, for example
`camel.main.routeControllerBackOffDelay` should be renamed to
`camel.routecontroller.backOffDelay`. And the option
`camel.main.routeControllerSuperviseEnabled` has been renamed to
`camel.routecontroller.enabled`.
:::
::::

:::: sect2
### camel-kamelet {#_camel_kamelet}

::: paragraph
All Kamelets are now turned off error handling in the route templates.
This is done to make using Kamelets the same as calling a regular Camel
component endpoint. Backwards mode can be enabled by setting
`noErrorHandler=false` on the `KameletComponent` or as URI parameter
`to("kamelet:http-sink?noErrorHandler=false&uri=xxxxx")`.
:::
::::

:::: sect2
### camel-azure-cosmosdb {#_camel_azure_cosmosdb}

::: paragraph
The `useDefaultIdentity` parameter has been removed in favor of the
credentialType parameter. Now user should select between
`SHARED_ACCOUNT_KEY` and `AZURE_IDENTITY`. This is part of the effort
explained in CAMEL-18590.
:::
::::

:::: sect2
### camel-azure-eventhubs {#_camel_azure_eventhubs}

::: paragraph
The credentialType parameter has been introduced with three possible
values: `AZURE_IDENTITY`, `CONNECTION_STRING` and `TOKEN_CREDENTIAL`.
With the `CONNECTION_STRING` mode the user could explicitly set the
`connectionString` parameters or use the `sharedAccessName` and
`sharedAccessKey` to automatically build the connection string. With the
`TOKEN_CREDENTIAL` mode the user could pass a `TokenCredential`
instance. With the `AZURE_IDENTITY` mode the user will be able to use
the Default Azure Credentials Chain. This is part of the effort
explained in CAMEL-18590.
:::
::::

:::: sect2
### camel-azure-servicebus {#_camel_azure_servicebus}

::: paragraph
The credentialType parameter has been introduced with three possible
values: `AZURE_IDENTITY`, `CONNECTION_STRING` and `TOKEN_CREDENTIAL`.
With the `CONNECTION_STRING` mode the user could explicitly set the
`connectionString` parameter. With the `TOKEN_CREDENTIAL` mode the user
could pass a `TokenCredential` instance. With the `AZURE_IDENTITY` mode
the user will be able to use the Default Azure Credentials Chain. This
is part of the effort explained in CAMEL-18590.
:::
::::

:::: sect2
### camel-azure-files {#_camel_azure_files}

::: paragraph
The credentialType parameter has been introduced with three possible
values: `AZURE_IDENTITY`, `SHARED_ACCOUNT_KEY` and `AZURE_SAS`. With the
`SHARED_ACCOUNT_KEY` mode the user could explicitly set the `sharedKey`
parameter. With the `AZURE_IDENTITY` mode the user will be able to use
the Default Azure Credentials Chain. With the `AZURE_SAS` mode the user
could explicitly set the token parameter. This is part of the effort
explained in CAMEL-18590.
:::
::::

:::: sect2
### camel-azure-storage-datalake {#_camel_azure_storage_datalake}

::: paragraph
The useDefaultIdentity parameter has been removed in favor of the
`credentialType` parameter. Now user should select between
`AZURE_IDENTITY`, `CLIENT_SECRET`, `SHARED_KEY_CREDENTIAL`, `AZURE_SAS`
and `SERVICE_CLIENT_INSTANCE`. With the `SHARED_KEY_CREDENTIAL` mode the
user could explicitly set the `sharedKey` parameter or a
`SharedKeyCredential` instance. With the `AZURE_IDENTITY` mode the user
will be able to use the Default Azure Credentials Chain. With the
`AZURE_SAS` mode the user could explicitly set the `sasSignature` or
`sasCredential` parameter. With the `CLIENT_SECRET` mode the user could
explicitly set `clientId`, `clientSecret` and `tenantId` or specify a
`ClientSecretCredential` instance. With the `SERVICE_CLIENT_INSTANCE`
the user could explicitly set a `serviceClient` parameter by passing a
`DataLakeServiceClient` instance. This is part of the effort explained
in CAMEL-18590.
:::
::::

:::: sect2
### camel-azure-storage-queue {#_camel_azure_storage_queue}

::: paragraph
The `useDefaultIdentity` parameter has been removed in favor of the
`credentialType` parameter. Now user should select between
`AZURE_IDENTITY`, `SHARED_KEY_CREDENTIAL` and `SHARED_ACCOUNT_KEY`. With
the `SHARED_KEY_CREDENTIAL` mode the user could explicitly set the
`SharedKeyCredential` instance. With the `AZURE_IDENTITY` mode the user
will be able to use the Default Azure Credentials Chain. With the
`SHARED_ACCOUNT_KEY` mode the user could explicitly set the `accessKey`
parameter. This is part of the effort explained in CAMEL-18590.
:::
::::

:::: sect2
### camel-cassandraql {#_camel_cassandraql}

::: paragraph
The `NamedCassandraAggregationRepository` now provides a
`deserializationFilter` parameter. The default value for it is allowing
all java packages and subpackages and all `org.apache.camel` packages
and subpackages. If you plan to use particular classes, and you want to
expand the filter, you should change the value according to your needs.
More details in CAMEL-20306.
:::
::::

:::: sect2
### camel-coap {#_camel_coap}

::: paragraph
Upgraded from `org.eclipse.californium` v2 to v3 which was a painful
upgrade. Removed the `"CamelCoapUri` header that would allow a producer
to create the dynamic client to send to another URL. Use Camel's
existing `toD` EIP for that instead.
:::
::::

:::: sect2
### camel-consul {#_camel_consul}

::: paragraph
This component has migrated from `com.orbitz.consul:consul-client` to
`org.kiwiproject:consul-client` as the former is no longer maintained,
and kiwiproject took over.
:::
::::

:::: sect2
### camel-dynamic-router {#_camel_dynamic_router}

::: paragraph
The dynamic router EIP component now handles control messages through a
separate control component: `dynamic-router-control`. These changes were
made after a user reported a bug that resulted in query-parameter-based
subscriptions being ignored after the first URI control message was
processed. All control message parameters can be submitted as query
parameters when subscribers are within the same JVM as the dynamic
router instance. Users must use \"dynamic to\", or `toD` when sending
these control message properties as query parameters. Predicates that
are not string expressions may be specified in the message body, or
bound to the registry and specified as a reference with the
`predicateBean` parameter. Control messages can still be sent in the
message body, as in the previous version. The
`DynamicRouterControlMessage` no longer has separate builders for
`subscribe` and `unsubscribe` messages, so there is only one builder
that you can use for any type of control message. The control channel
will now report subscription details for a routing channel if a message
with a control action of `list` is submitted, along with the dynamic
router channel of interest. Please see the `dynamic-router-eip` module
in the `camel-spring-boot-examples` repository for useful examples of
how you might need to change your code to be compatible with the changes
in this version.
:::
::::

:::: sect2
### camel-hdfs {#_camel_hdfs}

::: paragraph
The component has been removed after deprecation in 4.3.0
:::
::::

:::: sect2
### camel-jms {#_camel_jms}

::: paragraph
The header with key `JMSCorrelationIDAsBytes` has changed value from
`String` to `byte[]`.
:::
::::

:::: sect2
### camel-jsonata {#_camel_jsonata}

::: paragraph
Replaced the previous JSONata library with a new one that offers
complete compatibility with the JSONata reference implementation's
features.
:::
::::

:::: sect2
### camel-sql {#_camel_sql}

::: paragraph
The JdbcAggregationRepository now provides a deserializationFilter
parameter. The default value for it is allowing all java packages and
subpackages and all org.apache.camel packages and subpackages. If you
plan to use particular classes and you want to expand the filter, you
should change the value according to your needs. More details in
CAMEL-20303.
:::
::::

:::: sect2
### camel-facebook {#_camel_facebook}

::: paragraph
The component was removed without deprecation. The library supporting
this component has been unmaintained for a long time. We found no
indications that the library itself nor the component are working with
modern Facebook, along with the absence of community interest, which
lead us to decide to remove this component without deprecation.
:::
::::

:::: sect2
### camel-kafka {#_camel_kafka}

::: paragraph
The component now has support for batch processing.
:::
::::

:::: sect2
### camel-json-validator {#_camel_json_validator}

::: paragraph
Removed deprecated
`org.apache.camel.component.jsonvalidator.DefaultJsonSchemaLoader`, use
`org.apache.camel.component.jsonvalidator.DefaultJsonUriSchemaLoader`
instead.
:::
::::

:::: sect2
### camel-splunk-hec {#_camel_splunk_hec}

::: paragraph
Removed `token` from the URI's path in favor of setting it through a
`token` query parameter. While the `token` was in the URI path, it could
potentially be leaked within the logs.
:::
::::

:::: sect2
### camel-openapi-java and camel-rest-openapi {#_camel_openapi_java_and_camel_rest_openapi}

::: paragraph
Deprecated support for the old Swagger 2.0 spec. Only OpenAPI v3 specs
is supported from Camel 4.5 onwards.
:::
::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::: sect1
## Camel Spring Boot {#_camel_spring_boot}

:::::::::::::: sectionbody
::::::: sect2
### Ordering of BOM imports {#_ordering_of_bom_imports}

::: paragraph
When using Camel on Spring Boot, it's recommended to use BOMs to import
Camel and Spring dependencies. In Camel 4.4 onwards we changed the order
to let Camel be first as shown below:
:::

:::: listingblock
::: content
``` highlight
<dependencyManagement>
    <dependencies>
        <!-- Camel BOM -->
        <dependency>
            <groupId>org.apache.camel.springboot</groupId>
            <artifactId>camel-spring-boot-bom</artifactId>
            <version>${camel-version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
        <!-- Spring Boot BOM -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>${spring-boot-version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```
:::
::::

::: paragraph
In previous versions, then we would have `spring-boot-dependencies`
before `camel-spring-boot-bom`. However, to better align and ensure
Camel starters are using supported dependencies, then it's recommended
to let Camel be first.
:::
:::::::

:::: sect2
### Auto Configuration {#_auto_configuration}

::: paragraph
The route controller configuration has been moved from general main to
its own group. All keys started with `camel.springboot.routesController`
should be renamed to `camel.routecontroller.`, for example
`camel.springboot.routeControllerBackOffDelay` should be renamed to
`camel.routecontroller.backOffDelay`. And the option
`camel.springboot.routeControllerSuperviseEnabled` has been renamed to
`camel.routecontroller.enabled`.
:::
::::

:::: sect2
### Routes Collector {#_routes_collector}

::: paragraph
Camel Spring Boot will not honor the
`camel.springboot.includeNonSingletons` option (default false). This
means that only singleton `RouteBuilder` beans is added by default.
Previously then prototype scoped beans would be added as well.
:::
::::

:::: sect2
### camel-platform-http-vertx {#_camel_platform_http_vertx}

::: paragraph
Added configuration to enable Vert.x session handling. Sessions are
disabled by default, but can be enabled by setting the `enabled`
property on `VertxPlatformHttpServerConfiguration.SessionConfig` to
`true`. Other properties include `sessionCookieName`,
`sessionCookiePath`, `sessionTimeout`, `cookieSecure`, `cookieHttpOnly`
`cookieSameSite` and `storeType`. The session `storeType` defaults to
the Vert.x `LocalSessionStore` and `cookieSameSite` to `Strict`. The
remainder of the properties are configured with Vert.x defaults if not
set.
:::
::::
::::::::::::::
:::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
