::: {#header}
# Apache Camel 3.x to 4.0 Migration Guide
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
:::::: {#preamble}
::::: sectionbody
::: paragraph
This document is intended to help you migrate your Apache Camel
applications from version 3.20 or higher to 4.0. If you are upgrading
from an older Camel 3.x release, such as 3.14, then make sure to read
the individual [Camel 3.x Upgrade Guide](camel-3x-upgrade-guide.html) to
upgrade to the 3.20 release, prior to upgrade to Camel 4.
:::

::: {.admonitionblock .important}
+-----------------------------------+-----------------------------------+
| ::: title                         | If you are upgrading Camel 4.x to |
| Important                         | 4.y then use the [Camel 4.x       |
| :::                               | Upgrade                           |
|                                   | Gui                               |
|                                   | de](camel-4x-upgrade-guide.html). |
+-----------------------------------+-----------------------------------+
:::
:::::
::::::

::::: sect1
## Java versions {#_java_versions}

:::: sectionbody
::: paragraph
Camel 4 supports Java 17. Support for Java 11 is dropped.
:::
::::
:::::

::::: sect1
## Removed Components {#_removed_components}

:::: sectionbody
::: paragraph
The following components have been removed:
:::

+-----------------------------------+-----------------------------------+
| Component                         | Alternative component(s)          |
+===================================+===================================+
| camel-any23                       | none                              |
+-----------------------------------+-----------------------------------+
| camel-atlasmap                    | none                              |
+-----------------------------------+-----------------------------------+
| camel-atmos                       | none                              |
+-----------------------------------+-----------------------------------+
| camel-caffeine-lrucache           | camel-cache, camel-ignite,        |
|                                   | camel-infinispan                  |
+-----------------------------------+-----------------------------------+
| camel-cdi                         | camel-spring-boot, camel-quarkus  |
+-----------------------------------+-----------------------------------+
| camel-corda                       | none                              |
+-----------------------------------+-----------------------------------+
| camel-directvm                    | camel-direct                      |
+-----------------------------------+-----------------------------------+
| camel-dozer                       | camel-mapstruct                   |
+-----------------------------------+-----------------------------------+
| camel-elasticsearch-rest          | camel-elasticsearch               |
+-----------------------------------+-----------------------------------+
| camel-gora                        | none                              |
+-----------------------------------+-----------------------------------+
| camel-hbase                       | none                              |
+-----------------------------------+-----------------------------------+
| camel-hyperledger-aries           | none                              |
+-----------------------------------+-----------------------------------+
| camel-iota                        | none                              |
+-----------------------------------+-----------------------------------+
| camel-ipfs                        | none                              |
+-----------------------------------+-----------------------------------+
| camel-jbpm                        | none                              |
+-----------------------------------+-----------------------------------+
| camel-jclouds                     | none                              |
+-----------------------------------+-----------------------------------+
| camel-johnzon                     | camel-jackson, camel-fastjson,    |
|                                   | camel-gson                        |
+-----------------------------------+-----------------------------------+
| camel-microprofile-metrics        | camel-micrometer,                 |
|                                   | camel-opentelemetry               |
+-----------------------------------+-----------------------------------+
| camel-milo                        | none                              |
+-----------------------------------+-----------------------------------+
| camel-opentracing                 | camel-micrometer,                 |
|                                   | camel-opentelemetry               |
+-----------------------------------+-----------------------------------+
| camel-rabbitmq                    | spring-rabbitmq-component         |
+-----------------------------------+-----------------------------------+
| camel-rest-swagger                | camel-openapi-rest                |
+-----------------------------------+-----------------------------------+
| camel-restdsl-swagger-plugin      | camel-restdsl-openapi-plugin      |
+-----------------------------------+-----------------------------------+
| camel-resteasy                    | camel-cxf, camel-rest             |
+-----------------------------------+-----------------------------------+
| camel-spark                       | none                              |
+-----------------------------------+-----------------------------------+
| camel-spring-integration          | none                              |
+-----------------------------------+-----------------------------------+
| camel-swagger-java                | camel-openapi-java                |
+-----------------------------------+-----------------------------------+
| camel-websocket                   | camel-vertx-websocket             |
+-----------------------------------+-----------------------------------+
| camel-websocket-jsr356            | camel-vertx-websocket             |
+-----------------------------------+-----------------------------------+
| camel-vertx-kafka                 | camel-kafka                       |
+-----------------------------------+-----------------------------------+
| camel-vm                          | camel-seda                        |
+-----------------------------------+-----------------------------------+
| camel-weka                        | none                              |
+-----------------------------------+-----------------------------------+
| camel-xstream                     | camel-jacksonxml                  |
+-----------------------------------+-----------------------------------+
| camel-zipkin                      | camel-micrometer,                 |
|                                   | camel-opentelemetry               |
+-----------------------------------+-----------------------------------+
::::
:::::

::::: sect1
## Logging {#_logging}

:::: sectionbody
::: paragraph
Camel 4 has upgraded logging facade API `slf4j-api` from 1.7 to 2.0.
:::
::::
:::::

::::: sect1
## JUnit 4 {#_junit_4}

:::: sectionbody
::: paragraph
All the `camel-test` modules that were JUnit 4.x based has been removed.
All test modules now use JUnit 5.
:::
::::
:::::

:::::::::::: sect1
## API Changes {#_api_changes}

::::::::::: sectionbody
+----------------------+----------------------+-----------------------+
| Type of Change       | API                  | Alternative           |
+======================+======================+=======================+
| Removed              | `InOptionalOut` from | `InOut`               |
|                      | `org.apache.ca       |                       |
|                      | mel.ExchangePattern` |                       |
+----------------------+----------------------+-----------------------+
| Removed              | `@FallbackConverter` | `@Conver              |
|                      |                      | ter(fallback = true)` |
+----------------------+----------------------+-----------------------+
| Removed              | `getEndpointMap()`   |                       |
+----------------------+----------------------+-----------------------+
| Removed              | `uri` attribute on   | Use `value` (default) |
|                      | `@EndpointInject`,   | instead. For example  |
|                      | `@Produce`, and      | `@Produce(u           |
|                      | `@Consume`           | ri = "kafka:cheese")` |
|                      |                      | should be changed to  |
|                      |                      | `@Pro                 |
|                      |                      | duce("kafka:cheese")` |
+----------------------+----------------------+-----------------------+
| Removed              | `label` on           | use `category`        |
|                      | `@UriEndpoint`       | instead.              |
+----------------------+----------------------+-----------------------+
| Removed              | `consumerClass` on   |                       |
|                      | `@UriEndpoint`       |                       |
+----------------------+----------------------+-----------------------+
| Removed              | all `asyncCallback`  | `asyncSend` or        |
|                      | methods on           | `asyncRequest`.       |
|                      | `ProducerTemplate`   |                       |
+----------------------+----------------------+-----------------------+
| Removed              | `o                   | `or                   |
|                      | rg.apache.camel.spi. | g.apache.camel.spi.On |
|                      | OnCamelContextStart` | CamelContextStarting` |
+----------------------+----------------------+-----------------------+
| Removed              | `                    | `or                   |
|                      | org.apache.camel.spi | g.apache.camel.spi.On |
|                      | .OnCamelContextStop` | CamelContextStopping` |
+----------------------+----------------------+-----------------------+
| Removed              | `Discard` and        |                       |
|                      | `DiscardOldest` from |                       |
|                      | `org.apache.camel.u  |                       |
|                      | til.concurrent.Threa |                       |
|                      | dPoolRejectedPolicy` |                       |
+----------------------+----------------------+-----------------------+
| Removed              | `org.apache.camel.bu | This API was mostly   |
|                      | ilder.SimpleBuilder` | used internally in    |
|                      |                      | Camel with the Java   |
|                      |                      | DSL in some           |
|                      |                      | situations.           |
+----------------------+----------------------+-----------------------+
| Removed              | `ar                  |                       |
|                      | chetypeCatalogAsXml` |                       |
|                      | method from          |                       |
|                      | `org.apache.camel.c  |                       |
|                      | atalog.CamelCatalog` |                       |
+----------------------+----------------------+-----------------------+
| Removed              | `configure` from the |                       |
|                      | interface            |                       |
|                      | `org.apache.         |                       |
|                      | camel.main.Listener` |                       |
+----------------------+----------------------+-----------------------+
| Removed              | `getExtension` from  | Use                   |
|                      | the interface        | `getC                 |
|                      | `CamelContext`       | amelContextExtension` |
|                      |                      | instead. For example  |
|                      |                      | `Manag                |
|                      |                      | edCamelContext manage |
|                      |                      | d = context.getCamelC |
|                      |                      | ontextExtension().get |
|                      |                      | ContextPlugin(Managed |
|                      |                      | CamelContext.class);` |
+----------------------+----------------------+-----------------------+
| Moved                | Moved                | End users should use  |
|                      | `org.ap              | `org.apache.camel.s   |
|                      | ache.camel.support.I | pi.BeanIntrospection` |
|                      | ntrospectionSupport` | instead.              |
|                      | to                   |                       |
|                      | `camel-core-engine`  |                       |
|                      | for internal use     |                       |
|                      | only                 |                       |
+----------------------+----------------------+-----------------------+
| Moved                | Exchange failure     |                       |
|                      | handling status has  |                       |
|                      | moved from being a   |                       |
|                      | property defined as  |                       |
|                      | `ExchangeProperty    |                       |
|                      | Key.FAILURE_HANDLED` |                       |
|                      | to a member of the   |                       |
|                      | ExtendedExchange,    |                       |
|                      | accessible via       |                       |
|                      | \`isFailu            |                       |
|                      | reHandled()\`method. |                       |
+----------------------+----------------------+-----------------------+
| Replaced             | Replaced `adapt()`   |                       |
|                      | from                 |                       |
|                      | `org.apache          |                       |
|                      | .camel.CamelContext` |                       |
|                      | with                 |                       |
|                      | `getCa               |                       |
|                      | melContextExtension` |                       |
+----------------------+----------------------+-----------------------+
| Replaced             | Replaced `adapt()`   |                       |
|                      | from                 |                       |
|                      | `org.apache.cam      |                       |
|                      | el.ExtendedExchange` |                       |
|                      | with                 |                       |
|                      | `g                   |                       |
|                      | etExchangeExtension` |                       |
+----------------------+----------------------+-----------------------+
| Added                | Added `position`     |                       |
|                      | method to            |                       |
|                      | `org.apach           |                       |
|                      | e.camel.StreamCache` |                       |
+----------------------+----------------------+-----------------------+
| Decoupled            | Decoupled the        |                       |
|                      | `org.apache.camel.E  |                       |
|                      | xtendedCamelContext` |                       |
|                      | from the             |                       |
|                      | `org.apache          |                       |
|                      | .camel.CamelContext` |                       |
+----------------------+----------------------+-----------------------+
| Decoupled            | Decoupled the        |                       |
|                      | `org.apache.cam      |                       |
|                      | el.ExtendedExchange` |                       |
|                      | from the             |                       |
|                      | `org.apa             |                       |
|                      | che.camel.Exchange`. |                       |
+----------------------+----------------------+-----------------------+
| Changed              | The type for         |                       |
|                      | `dumpRoutes` on      |                       |
|                      | `CamelContext` has   |                       |
|                      | changed from         |                       |
|                      | `boolean` to         |                       |
|                      | `String` to allow    |                       |
|                      | specifying either    |                       |
|                      | xml or yaml.         |                       |
+----------------------+----------------------+-----------------------+
| Changed              | The                  |                       |
|                      | `org.apache.camel    |                       |
|                      | .health.HealthCheck` |                       |
|                      | method `isLiveness`  |                       |
|                      | is now default       |                       |
|                      | `false` instead of   |                       |
|                      | `true`.              |                       |
+----------------------+----------------------+-----------------------+
| Added                | Added `position`     |                       |
|                      | method to            |                       |
|                      | `org.apach           |                       |
|                      | e.camel.StreamCache` |                       |
+----------------------+----------------------+-----------------------+
| Added                | The                  |                       |
|                      | `org.ap              |                       |
|                      | ache.camel.support.E |                       |
|                      | ventNotifierSupport` |                       |
|                      | abstract class now   |                       |
|                      | implements           |                       |
|                      | `CamelContextAware`. |                       |
+----------------------+----------------------+-----------------------+

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | The                               |
| Tip                               | `org.a                            |
| :::                               | pache.camel.support.PluginHelper` |
|                                   | gives easy access to various      |
|                                   | extensions and context plugins,   |
|                                   | that was available previously in  |
|                                   | Camel v3 directly from            |
|                                   | `CamelContext`.                   |
+-----------------------------------+-----------------------------------+
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | You can get access to the         |
| Note                              | advanced APIs in `CamelContext`   |
| :::                               | known as `ExtendedCamelContext`   |
|                                   | via                               |
|                                   | `con                              |
|                                   | text.getCamelContextExtension()`. |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
To get hold of `ManagedCamelContext` then you should use the following
way:
:::

:::: listingblock
::: content
``` highlight
ManagedCamelContext managed = camelContext.getCamelContextExtension().getContextPlugin(ManagedCamelContext.class);
```
:::
::::

::: paragraph
This can be done by many other advanced Camel features such as
`RoutesLoader` or `ModelToXMLDumper`:
:::

:::: listingblock
::: content
``` highlight
RoutesLoader loader = camelContext.getCamelContextExtension().getContextPlugin(RoutesLoader.class);
```
:::
::::
:::::::::::
::::::::::::

:::::::: sect1
## EIP Changes {#_eip_changes}

::::::: sectionbody
::: paragraph
Removed `lang` attribute for the `<description>` on every EIPs.
:::

::: paragraph
The `InOnly` and `InOut` EIPs has been removed. Instead, use
`SetExchangePattern` or `To` where you can specify exchange pattern to
use.
:::

:::: sect2
### Poll Enrich EIP {#_poll_enrich_eip}

::: paragraph
The polled endpoint URI is now stored as property on the `Exchange`
(with key `CamelToEndpoint`) like all other EIPs. Before the URI was
stored as a message header.
:::
::::
:::::::
::::::::

::::::::::: sect1
## CircuitBreaker EIP {#_circuitbreaker_eip}

:::::::::: sectionbody
::: paragraph
The following options in `camel-resilience4j` was mistakenly not defined
as attributes:
:::

+-----------------------------------------------------------------------+
| **Option**                                                            |
+-----------------------------------------------------------------------+
| bulkheadEnabled                                                       |
+-----------------------------------------------------------------------+
| bulkheadMaxConcurrentCalls                                            |
+-----------------------------------------------------------------------+
| bulkheadMaxWaitDuration                                               |
+-----------------------------------------------------------------------+
| timeoutEnabled                                                        |
+-----------------------------------------------------------------------+
| timeoutExecutorService                                                |
+-----------------------------------------------------------------------+
| timeoutDuration                                                       |
+-----------------------------------------------------------------------+
| timeoutCancelRunningFuture                                            |
+-----------------------------------------------------------------------+

::: paragraph
These options were not exposed in YAML DSL, and in XML DSL you need to
migrate from:
:::

:::: listingblock
::: content
``` highlight
<circuitBreaker>
    <resilience4jConfiguration>
        <timeoutEnabled>true</timeoutEnabled>
        <timeoutDuration>2000</timeoutDuration>
    </resilience4jConfiguration>
...
</circuitBreaker>
```
:::
::::

::: paragraph
To use attributes instead:
:::

:::: listingblock
::: content
``` highlight
<circuitBreaker>
    <resilience4jConfiguration timeoutEnabled="true" timeoutDuration="2000"/>
...
</circuitBreaker>
```
:::
::::
::::::::::
:::::::::::

::::::::::: sect1
## XML DSL {#_xml_dsl}

:::::::::: sectionbody
::: paragraph
The `<description>` to set a description on a route or node, has been
changed from an element to an attribute.
:::

::: paragraph
Before:
:::

:::: listingblock
::: content
``` highlight
<route id="myRoute">
  <description>Something that this route do</description>
  <from uri="kafka:cheese"/>
  ...
</route>
```
:::
::::

::: paragraph
After:
:::

:::: listingblock
::: content
``` highlight
<route id="myRoute" description="Something that this route do">
  <from uri="kafka:cheese"/>
  ...
</route>
```
:::
::::
::::::::::
:::::::::::

::::: sect1
## Type Converter {#_type_converter}

:::: sectionbody
::: paragraph
The `String` → `java.io.File` converter has been removed.
:::
::::
:::::

:::::: sect1
## Tracing {#_tracing}

::::: sectionbody
::: paragraph
The [Tracer](tracer.html) and [Backlog Tracer](backlog-tracer.html) no
longer includes internal tracing events from routes that was created by
Rest DSL or route templates or Kamelets. You can turn this on, by
setting `traceTemplates=true` in the tracer.
:::

::: paragraph
The [Backlog Tracer](backlog-tracer.html) has been enhanced and *fixed*
to trace message headers (also streaming types). This means that
previously headers of type `InputStream` was not traced before, but is
now included. This could mean that the header stream is positioned at
the end, and logging the header afterward may appear as the header value
is empty.
:::
:::::
::::::

::::: sect1
## UseOriginalMessage / UseOriginalBody {#_useoriginalmessage_useoriginalbody}

:::: sectionbody
::: paragraph
When `useOriginalMessage` or `useOriginalBody` is enabled in
`OnException`, `OnCompletion` or error handlers, then the original
message body is defensively copied and if possible converted to
`StreamCache` to ensure the body can be re-read when accessed.
Previously the original body was not converted to `StreamCache` which
could lead to the body not able to be read or the stream has been
closed.
:::
::::
:::::

:::::::::::::: sect1
## Camel Health {#_camel_health}

::::::::::::: sectionbody
::: paragraph
Health checks are now by default only readiness checks out of the box.
:::

::: paragraph
Camel provides the `CamelContextCheck` as both readiness and liveness
checks, so there is at least one of each out of the box.
:::

::: paragraph
Only consumer-based health checks are enabled by default.
:::

::::::::: sect2
### Producer Health Checks {#_producer_health_checks}

::: paragraph
The option `camel.health.components-enabled` has been renamed to
`camel.health.producers-enabled`.
:::

::: paragraph
Some components, in particular AWS, also provide health checks for
producers. In Camel 3.x these health checks did not work properly and
has been disabled in the source. To continue this behaviour in Camel 4,
then producer-based health checks are disabled.
:::

::: paragraph
Notice that `camel-kafka` comes with producer based health-check that
worked in Camel 3, and therefore this change in Camel 4, means that this
health-check is disabled.
:::

::: paragraph
You **MUST** enable producer health-checks globally, such as in
`application.properties`:
:::

:::: listingblock
::: content
``` highlight
camel.health.producers-enabled = true
```
:::
::::
:::::::::
:::::::::::::
::::::::::::::

::::::: sect1
## JMX {#_jmx}

:::::: sectionbody
::: paragraph
Camel now also include MBeans for `doCatch` and `doFinally` in the tree
of processor MBeans.
:::

::: paragraph
The `ManagedChoiceMBean` have renamed `choiceStatistics` to
`extendedInformation`. The `ManagedFailoverLoadBalancerMBean` have
renamed `exceptionStatistics` to `extendedInformation`.
:::

::: paragraph
The `CamelContextMBean` and `CamelRouteMBean` has removed method
`dumpRouteAsXml(boolean resolvePlaceholders, boolean resolveDelegateEndpoints)`.
:::
::::::
:::::::

::::::::::: sect1
## YAML DSL {#_yaml_dsl}

:::::::::: sectionbody
::: paragraph
The backwards compatible mode Camel 3.14 or older, which allowed to have
*steps* as child to *route* has been removed.
:::

::: paragraph
The old syntax:
:::

:::: listingblock
::: content
``` highlight
- route:
    from:
      uri: "direct:info"
    steps:
    - log: "message"
```
:::
::::

::: paragraph
should be changed to:
:::

:::: listingblock
::: content
``` highlight
- route:
    from:
      uri: "direct:info"
      steps:
      - log: "message"
```
:::
::::
::::::::::
:::::::::::

::::::: sect1
## Backlog Tracing {#_backlog_tracing}

:::::: sectionbody
::: paragraph
The option `backlogTracing=true` now automatic enabled the tracer on
startup. The previous behavior was *surprisingly* that the tracer was
only made available, and had to be manually enabled afterward. The old
behavior can be archived by setting `backlogTracingStandby=true`.
:::

::: paragraph
Move the following class from
`org.apache.camel.api.management.mbean.BacklogTracerEventMessage` in
`camel-management-api` JAR to
`org.apache.camel.spi.BacklogTracerEventMessage` in `camel-api` JAR.
:::

::: paragraph
The `org.apache.camel.impl.debugger.DefaultBacklogTracerEventMessage`
has been refactored into an interface
`org.apache.camel.spi.BacklogTracerEventMessage` with some additional
details about traced messages. For example Camel now captures a *first*
and *last* trace that contains the input and outgoing (if `InOut`)
messages.
:::
::::::
:::::::

::::: sect1
## XML serialization {#_xml_serialization}

:::: sectionbody
::: paragraph
The default XML serialization using `ModelToXMLDumper` has been improved
and now uses a generated XML serializer located in the `camel-xml-io`
module instead of the JAXB based one from `camel-jaxb`.
:::
::::
:::::

::::: sect1
## OpenAPI Maven Plugin {#_openapi_maven_plugin}

:::: sectionbody
::: paragraph
The `camel-restdsl-openapi-plugin` Maven plugin now uses `platform-http`
as the default rest component in the generated Rest DSL code.
Previously, the default was servlet. However, platform-http is a better
default that works out of the box with Spring Boot and Quarkus.
:::
::::
:::::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Component changes {#_component_changes}

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
:::: sect2
### Category {#_category}

::: paragraph
The number of enums for `org.apache.camel.Category` has been reduced
from 83 to 37, which means custom components that are using removed
values need to choose one of the remainder values. We have done this to
consolidate the number of categories of components in the Camel
community.
:::
::::

:::: sect2
### camel-openapi-rest-dsl-generator {#_camel_openapi_rest_dsl_generator}

::: paragraph
This dsl-generator has updated the underlying model classes
(`apicurio-data-models`) from 1.1.27 to 2.0.3.
:::
::::

:::: sect2
### camel-atom {#_camel_atom}

::: paragraph
The `camel-atom` component has changed the third party atom client from
Apache Abdera to RSSReader. This means the feed object is changed from
`org.apache.abdera.model.Feed` to
`com.apptasticsoftware.rssreader.Item`.
:::
::::

:::: sect2
### camel-azure-cosmosdb {#_camel_azure_cosmosdb}

::: paragraph
The `itemPartitionKey` has been updated. It's now a String a not a
PartitionKey anymore. More details in CAMEL-19222.
:::
::::

:::::::::: sect2
### camel-bean {#_camel_bean}

::: paragraph
When using the `method` option to refer to a specific method, and using
parameter types and values, such as:
`"bean:myBean?method=foo(com.foo.MyOrder, true)"` then any class types
must now be using `.class` syntax, i.e. `com.foo.MyOrder` should now be
`com.foo.MyOrder.class`.
:::

::: paragraph
The example from above should now be as follows:
:::

:::: literalblock
::: content
    "bean:myBean?method=foo(com.foo.MyOrder.class, true)"
:::
::::

::: paragraph
This also applies to Java types such as String, int, etc.:
:::

:::: literalblock
::: content
    "bean:myBean?method=bar(String.class, int.class)"
:::
::::
::::::::::

:::: sect2
### camel-box {#_camel_box}

::: paragraph
Upgraded from Box Java SDK v2 to v4, which have some method signature
changes. The method to get a file thumbnail is no longer available.
:::
::::

:::: sect2
### camel-caffeine {#_camel_caffeine}

::: paragraph
The `keyType` parameter has been removed. The Key for the cache will now
be only `String` type. More information in CAMEL-18877.
:::
::::

:::: sect2
### camel-fhir {#_camel_fhir}

::: paragraph
The underlying `hapi-fhir` library has been upgraded from 4.2.0 to
6.2.4. Only the `Delete` API method has changed and now returns
`ca.uhn.fhir.rest.api.MethodOutcome` instead of
`org.hl7.fhir.instance.model.api.IBaseOperationOutcome`. See
[https://hapifhir.io/hapi-fhir/blog/](https://hapifhir.io/hapi-fhir/blog/){.bare}
for a more detailed list of underlying changes (only the hapi-fhir
client is used in Camel).
:::
::::

:::: sect2
### camel-google {#_camel_google}

::: paragraph
The API-based components `camel-google-drive`, `camel-google-calendar`,
`camel-google-sheets` and `camel-google-mail` has been upgraded from
Google Java SDK v1 to v2 and to latest API revisions. The
`camel-google-drive` and `camel-google-sheets` have some API methods
changes, but the others are identical as before.
:::
::::

:::::: sect2
### camel-http {#_camel_http}

::: paragraph
The component has been upgraded to use Apache HttpComponents v5 which
has an impact on how the underlying client is configured. There are 4
different timeouts (`connectionRequestTimeout`, `connectTimeout`,
`soTimeout` and `responseTimeout`) instead of initially 3
(`connectionRequestTimeout`, `connectTimeout` and `socketTimeout`) and
the default value of some of them has changed so please refer to the
documentation for more details.
:::

::: paragraph
Please note that the `socketTimeout` has been removed from the possible
configuration parameters of `HttpClient`, use `responseTimeout` instead.
:::

::: paragraph
Finally, the option `soTimeout` along with any parameters included into
`SocketConfig`, need to be prefixed by `httpConnection.`, the rest of
the parameters including those defined into `HttpClientBuilder` and
`RequestConfig` still need to be prefixed by `httpClient.` like before.
:::
::::::

:::: sect2
### camel-http-common {#_camel_http_common}

::: paragraph
The API in `org.apache.camel.http.common.HttpBinding` has changed
slightly to be more reusable. The `parseBody` method now takes in
`HttpServletRequest` as input parameter. And all `HttpMessage` has been
changed to generic `Message` types.
:::
::::

::::: sect2
### camel-kubernetes {#_camel_kubernetes}

::: paragraph
The `io.fabric8:kubernetes-client` library has been upgraded and some
deprecated API usage has been removed. Operations previously prefixed
with `replace` are now prefixed with `update`.
:::

::: paragraph
For example `replaceConfigMap` is now `updateConfigMap`, `replacePod` is
now `updatePod` etc. The corresponding constants in class
`KubernetesOperations` are also renamed. `REPLACE_CONFIGMAP_OPERATION`
is now `UPDATE_CONFIGMAP_OPERATION`, `REPLACE_POD_OPERATION` is now
`UPDATE_POD_OPERATION` etc.
:::
:::::

:::: sect2
### camel-web3j {#_camel_web3j}

::: paragraph
The `camel-web3j` has upgraded the `web3j` JAR from 3.x to 5.0 which has
many API changes, and so some previous API calls are no long provided.
:::
::::

:::: sect2
### camel-main {#_camel_main}

::: paragraph
The following constants has been moved from `BaseMainSupport` / `Main`
to `MainConstants`:
:::

+-----------------------------------+-----------------------------------+
| Old Name                          | New Name                          |
+-----------------------------------+-----------------------------------+
| Main.DEFA                         | MainConstants.DEFA                |
| ULT_PROPERTY_PLACEHOLDER_LOCATION | ULT_PROPERTY_PLACEHOLDER_LOCATION |
+-----------------------------------+-----------------------------------+
| Main.INITIAL_PROPERTIES_LOCATION  | MainCons                          |
|                                   | tants.INITIAL_PROPERTIES_LOCATION |
+-----------------------------------+-----------------------------------+
| Main.OVERRIDE_PROPERTIES_LOCATION | MainConst                         |
|                                   | ants.OVERRIDE_PROPERTIES_LOCATION |
+-----------------------------------+-----------------------------------+
| M                                 | MainConsta                        |
| ain.PROPERTY_PLACEHOLDER_LOCATION | nts.PROPERTY_PLACEHOLDER_LOCATION |
+-----------------------------------+-----------------------------------+
::::

:::: sect2
### camel-micrometer {#_camel_micrometer}

::: paragraph
The metrics has been renamed to follow Micrometer naming convention
[Naming Meters](https://micrometer.io/docs/concepts#_naming_meters).
:::

+-----------------------------------+-----------------------------------+
| Old Name                          | New Name                          |
+-----------------------------------+-----------------------------------+
| CamelExchangeEventNotifier        | camel.exchange.event.notifier     |
+-----------------------------------+-----------------------------------+
| CamelExchangesFailed              | camel.exchanges.failed            |
+-----------------------------------+-----------------------------------+
| CamelExchangesFailuresHandled     | camel.exchanges.failures.handled  |
+-----------------------------------+-----------------------------------+
| CamelExchangesInflight            | came                              |
|                                   | l.exchanges.external.redeliveries |
+-----------------------------------+-----------------------------------+
| CamelExchangesSucceeded           | camel.exchanges.succeeded         |
+-----------------------------------+-----------------------------------+
| CamelExchangesTotal               | camel.exchanges.total             |
+-----------------------------------+-----------------------------------+
| CamelMessageHistory               | camel.message.history             |
+-----------------------------------+-----------------------------------+
| CamelRoutePolicy                  | camel.route.policy                |
+-----------------------------------+-----------------------------------+
| CamelRoutePolicyLongTask          | camel.route.policy.long.task      |
+-----------------------------------+-----------------------------------+
| CamelRoutesAdded                  | camel.routes.added                |
+-----------------------------------+-----------------------------------+
| CamelRoutesRunning                | camel.routes.running              |
+-----------------------------------+-----------------------------------+
::::

::::::: sect2
### camel-jbang {#_camel_jbang}

::: paragraph
The command `camel dependencies` has been renamed to `camel dependency`.
:::

::: paragraph
In Camel JBang the `-dir` parameter for `init` and `run` goal has been
renamed to require 2 dashes `--dir` like all the other options.
:::

::: paragraph
The `camel stop` command will now by default stop all running
integrations (the option `--all` has been removed).
:::

::: paragraph
The *Placeholders substitutes* is changed to use `#name` instead of
`$name` syntax.
:::
:::::::

:::: sect2
### camel-jpa {#_camel_jpa}

::: paragraph
The option `transactionManager` has been removed, and a new option named
`transactionStrategy` has been added, that acts as vendor neutral
abstraction to make it easier to configure Spring Transaction or Quarkus
Transaction.
:::
::::

:::: sect2
### camel-openapi-java {#_camel_openapi_java}

::: paragraph
The `camel-openapi-java` component has been changed to use
`io.swagger.v3` libraries instead of `io.apicurio.datamodels`. As a
result, the return type of the public method
org.apache.camel.openapi.RestOpenApiReader.read() is now
`io.swagger.v3.oas.models.OpenAPI` instead of
`io.apicurio.datamodels.openapi.models.OasDocument`. When an OpenAPI 2.0
(swagger) specification is parsed, it is automatically upgraded to
OpenAPI 3.0.x by the swagger parser. This version also supports OpenAPI
3.1.x specifications. The related spring-boot starter components have
been modified to use the new return type.
:::
::::

:::::::::::::: sect2
### camel-optaplanner {#_camel_optaplanner}

::: paragraph
The `camel-optaplanner` component has been change to use
`SolverManager`. If you were using `SoverManager` in Camel 3, you don't
need anymore the boolean useSolverManager in the Route. Deprecated
`ProblemFactChange` has been replaced by `ProblemChange`.
:::

::: paragraph
The new URI path is:
:::

:::: listingblock
::: content
``` highlight
from("optaplanner:myProblemName")
  .to("...")
```
:::
::::

::: paragraph
You can pass the OptaPlanner SolverManager in 2 ways:
:::

::: ulist
- as #parameter

- as header
:::

::: paragraph
When running `camel-optaplanner` on Spring Boot or Quarkus, it is
preferable to use the Spring Boot or Quarkus way of creating the
SolverManager.
:::

::: paragraph
It is possible to migrate legacy Camel OptaPlanner Routes, by putting
the XML config file, as show in the code below. Camel OptaPlanner will
handle creating the SolverManager for those legacy Routes:
:::

:::: listingblock
::: content
``` highlight
from("optaplanner:myProblemName?configFile=PATH/TO/CONFIG.FILE.xml")
  .to("...")
```
:::
::::

::: paragraph
Solver Daemon solutions should be migrated to use SolverManager.
:::
::::::::::::::

:::: sect2
### camel-platform-http-vertx {#_camel_platform_http_vertx}

::: paragraph
If the route or consumer is suspended, then http status 503 is now
returned instead of 404.
:::
::::

:::: sect2
### camel-salesforce {#_camel_salesforce}

::: paragraph
Property names of blob fields on generated DTOs no longer have \'Url\'
affixed. E.g., the `ContentVersionUrl` property is now just
`ContentVersion`.
:::
::::

:::: sect2
### camel-slack {#_camel_slack}

::: paragraph
The default delay (on Slack consumer) is changed from 0.5s to 10s to
avoid being rate limited to often by Slack.
:::
::::

:::: sect2
### camel-spring-rabbitmq {#_camel_spring_rabbitmq}

::: paragraph
The option `replyTimeout` in `camel-spring-rabbitmq` has been fixed and
the default value from 5 to 30 seconds (this is the default used by
Spring).
:::
::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::: sect1
## Camel Spring Boot {#_camel_spring_boot}

::::::::::::::::::: sectionbody
::: paragraph
The `camel-spring-boot` dependency no longer includes
`camel-spring-xml`. To use legacy Spring XML files `<beans>` with Camel
on Spring Boot, then include the `camel-spring-boot-xml-starter`
dependency.
:::

::::: sect2
### Graceful Shutdown {#_graceful_shutdown}

::: paragraph
Apache Camel shutdowns a bit later during Spring Boot shutdown. This
allows Spring Boot graceful shutdown to complete first (stopping Spring
Boot HTTP server gracefully), and then afterward Camel is doing its own
[graceful-shutdown.html](graceful-shutdown.html).
:::

::: paragraph
Technically `camel-spring` has changed `getPhase()` from returning
`Integer.MAX_VALUE` to `Integer.MAX_VALUE - 2049`. This gives room for
Spring Boot services to shut down first.
:::
:::::

:::: sect2
### camel-micrometer-starter {#_camel_micrometer_starter}

::: paragraph
The `uri` tags are now static instead of dynamic (by default), as
potential too many tags generated due to URI with dynamic values. This
can be enabled again by setting `camel.metrics.uriTagDynamic=true`.
:::
::::

:::::::::: sect2
### camel-platform-http-starter {#_camel_platform_http_starter}

::: paragraph
The `platform-http-starter` has been changed from using `camel-servlet`
to use Spring HTTP server directly. Therefore, all the HTTP endpoints
are no longer prefixed with the servlet context-path (default is
`camel`).
:::

::: paragraph
For example:
:::

:::: listingblock
::: content
``` highlight
from("platform-http:myservice")
  .to("...")
```
:::
::::

::: paragraph
Then calling *myservice* would before require to include the
context-path, such as
[`http://localhost:8080/camel/myservice`](http://localhost:8080/camel/myservice){.bare}.
Now the context-path is not in use, and the endpoint can be called with
[`http://localhost:8080/myservice`](http://localhost:8080/myservice){.bare}.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | The `platform-http-starter` can   |
| Note                              | also be used with Rest DSL.       |
| :::                               |                                   |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
If the route or consumer is suspended, then http status 503 is now
returned instead of 404.
:::
::::::::::

:::: sect2
### camel-twitter {#_camel_twitter}

::: paragraph
The component was updated to use Twitter4j version 4.1.2, which [has
moved the packages](https://twitter4j.org/2022/10/21/264) used by a few
of its classes. If accessing certain twitter-related data, such as the
Tweet status, you need to update the packages used from
`twitter4j.Status` to `twitter4j.v1.Status`.
:::
::::
:::::::::::::::::::
::::::::::::::::::::

::::::: sect1
## Upgrading Camel 4.0.1 to 4.0.2 {#_upgrading_camel_4_0_1_to_4_0_2}

:::::: sectionbody
::::: sect2
### camel-file {#_camel_file}

::: paragraph
The `readLock=changed` with using `readLockMinAge` has been restored to
same behaviour as 3.x.
:::

::: paragraph
For example, using `readLockMinAge=5s` would pick up files that are
older than 5 seconds from startup time. If you have many existing files
on startup that are old, then Camel will now again be fast, and pick up
these files immediately.
:::
:::::
::::::
:::::::

:::::::::::: sect1
## Upgrading Camel 4.0.0 to 4.0.1 {#_upgrading_camel_4_0_0_to_4_0_1}

::::::::::: sectionbody
:::::::::: sect2
### camel-aws2-sns {#_camel_aws2_sns}

::: paragraph
The `queueUrl` parameter has been replaced by the `queueArn` parameter
:::

::: paragraph
For Example before
:::

:::: listingblock
::: content
    from("direct:start")
      .to("aws2-sns://mytopic?subject=mySubject&autoCreateTopic=true&subscribeSNStoSQS=true&queueUrl=https://xxxx")
:::
::::

::: paragraph
Should be changed to
:::

:::: listingblock
::: content
    from("direct:start")
      .to("aws2-sns://mytopic?subject=mySubject&autoCreateTopic=true&subscribeSNStoSQS=true&queueArn=arn:aws:sqs:xxxxx")
:::
::::
::::::::::
:::::::::::
::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
