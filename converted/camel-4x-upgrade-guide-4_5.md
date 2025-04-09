::: {#header}
# Apache Camel 4.x Upgrade Guide
:::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
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

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Upgrading Camel 4.4 to 4.5 {#_upgrading_camel_4_4_to_4_5}

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::::::::::: sect2
### camel-core {#_camel_core}

::: paragraph
Camel startup summary will now report total number of routes without
(internal routes created by Kamelets or Rest DSL). This ensures the
total number better reflect the total number of user created routes. The
summary now includes a separate number of Kamelets and Rest DSL routes.
See also the camel-kamelet section.
:::

::: paragraph
The following deprecated methods from the `AbstractCamelContext`,
deprecated on 4.2 as part of CAMEL-19998, were finally removed:
:::

::: ulist
- `setTypeConverter`

- `getOrCreateTypeConverter`

- `setManagementMBeanAssembler`

- `getRestRegistryFactory`

- `setRestRegistryFactory`

- `setTransformerRegistry`

- `setValidatorRegistry`

- `setName`

- `setDescription`

- `getBootstrapFactoryFinder`

- `getFactoryFinder`

- `addInterceptStrategy`

- `getStartupStepRecorder`

- `setStartupStepRecorder`

- `resolvePropertyPlaceholders`

- `getBasePackageScan`

- `setBasePackageScan`
:::

::: paragraph
Users of these methods should use the respective operations from the
`ExtendedCamelContext` (accessed via `getCamelContextExtension()`),
instead.
:::

::: paragraph
The following deprecated methods from the `CamelContext`, deprecated on
4.4 as part of CAMEL-20225, were finally removed and/or modified:
:::

::: ulist
- `getUptime`: modified to return a regular `Duration` instance.

- `getUptimeMillis`: removed.

- `getStartDate` removed.
:::

::: paragraph
Users of this method should proceed as following:
:::

::: ulist
- `getUptime`: use `ContextHelper.getUptime`.

- `getUptimeMillis`: use `getUptime().toMillis()`.

- `getStartDate`: use `ContextHelper.getStartDate`.
:::
:::::::::::

:::::::::::::::::::::: sect2
### Rest DSL {#_rest_dsl}

::: paragraph
Camel has changed the default value for `inlineRoutes=false` to
`inlineRoutes=true` in `restConfiguration`. It is very typical to define
Rest DSL and for each service api, then call a Camel route via `direct`
endpoints. By inlining these two, then you only have 1 route in Camel
instead of 2. This helps reduce the clutter of routes that otherwise is
in use when using Rest DSL and many services. You can restore to old
behaviour by setting the option back to `inlineRoutes=false`.
:::

::: paragraph
However, the inlining requires that each REST endpoint inlined with
`direct` endpoints must use unique direct names, ie.
:::

:::: listingblock
::: content
``` highlight
rest("/rest")
    .get("/").to("direct:printMethod")
    .post("/").to("direct:printMethod")
    .put("/").to("direct:printMethod");
```
:::
::::

::: paragraph
Should be changed to:
:::

:::: listingblock
::: content
``` highlight
rest("/rest")
    .get("/").to("direct:printMethod1")
    .post("/").to("direct:printMethod2")
    .put("/").to("direct:printMethod3");
```
:::
::::

::: paragraph
Or to keep old behaviour, you can turn off inlining.
:::

::: paragraph
Rest DSL will now eagerly resolve property placeholders that are used
during building the `rest:` endpoint.
:::

::: paragraph
For example with a Rest DSL using a placeholder
(`app.mypath = helloapp`) in the `path`:
:::

:::: listingblock
::: content
``` highlight
- rest:
    path: "{{app.mypath}}"
    post:
      - to: direct:demo
```
:::
::::

::: paragraph
Will not be resolved in the `rest` endpoint which can be seen during
startup logging:
:::

:::: listingblock
::: content
``` highlight
Routes startup (total:2)
Started demo (rest://post:%7B%7Bapp.mypath%7D%7D)
```
:::
::::

::: paragraph
The placeholder is now resolved eagerly, and you will see *nicer*
startup logs such as:
:::

:::: listingblock
::: content
``` highlight
Routes startup (total:2)
Started demo (rest://post:helloapp)
```
:::
::::

::: paragraph
The `restConfiguration` has changed default value in the
`useXForwardHeaders` option from `true` to `false`. Using X-Forward
headers is only used in special use-cases such as involving reverse
proxies.
:::
::::::::::::::::::::::

:::: sect2
### Avro Data Format {#_avro_data_format}

::: paragraph
The default library for the `avro` data format has changed from Apache
Avro to Jackson Avro. We also use Jackson as default for the JSon
dataformat.
:::
::::

:::::::::: sect2
### Intercept EIP {#_intercept_eip}

::: paragraph
The `interceptFrom` and `interceptSentToEndpoint` EIPs is now storing
the intercepted endpoint using key `Exchange.INTERCEPTED_ENDPOINT` as
exchange property instead of a header.
:::

::: paragraph
Before:
:::

:::: listingblock
::: content
``` highlight
String uri = exchange.getIn().getHeader(Exchange.INTERCEPTED_ENDPOINT, String.class);
```
:::
::::

::: paragraph
After:
:::

:::: listingblock
::: content
``` highlight
String uri = exchange.getProperty(Exchange.INTERCEPTED_ENDPOINT, String.class);
```
:::
::::
::::::::::

:::: sect2
### camel-bom / camel-spring-boot-bom {#_camel_bom_camel_spring_boot_bom}

::: paragraph
The Camel Maven BOMs (`camel-bom` and `camel-spring-boot-bom`) has been
changed to use hardcoded Camel release versions to ensure the BOM is
always correct.
:::
::::

::::: sect2
### camel-main {#_camel_main}

::: paragraph
The options `camel.main.backlogTracing`,
`camel.main.backlogTracingStandby`, and
`camel.main.backlogTracingTemplates` has been moved into a new group
`camel.trace` with more options to configure the backlog tracer.
:::

::: paragraph
To enable backlog tracing you should now set `camel.trace.enabled=true`
instead of `camel.main.backlogTracing=true`.
:::
:::::

::::::::::: sect2
### camel-console {#_camel_console}

::: paragraph
The `@DevConsole` annotation has been enhanced to include more
information.
:::

::: paragraph
Migrate from
:::

:::: listingblock
::: content
``` highlight
@DevConsole("stub")
```
:::
::::

::: paragraph
To
:::

:::: listingblock
::: content
``` highlight
@DevConsole(name = "stub", description = "Browse messages on stub endpoints")
```
:::
::::

::: paragraph
We also renamed the `route-curcuit-breaker` console to
`circuit-breaker`.
:::
:::::::::::

:::::::: sect2
### camel-jbang {#_camel_jbang}

::: paragraph
The `--profile` option on `export` command has been removed.
:::

::: paragraph
The `--profile` option on `run` command is now used by `camel-main` to
choose profile mode when running Camel with JBang, or standalone with
Camel Main. The default mode is `dev` for development which comes with
some additional features enabled in Camel to gather more information
that are relevant for development and the Camel JBang CLI.
:::

::: paragraph
You can run with `--profile=prod` to turn off all of this, which makes
Camel run more similar to a production situation.
:::

::: paragraph
The command `camel generate rest` have removed all the shorthand
arguments `` such as `-i -o `` instead use the long names
`--input --output`.
:::

::: paragraph
The shorthand `-p` option from `run` and `script` command has been
removed. Use `--prop` instead.
:::
::::::::

:::::::: sect2
### camel-jsonpath {#_camel_jsonpath}

::: paragraph
The `camel-jsonpath` will now work more similar as `camel-jq` when you
specify a `resultType` and have a list of entities. Before
`camel-jsonapath` would attempt to convert the `List` to the given
`restultType` which often is not usable. What users want is to be able
to convert each entry in the list to a given type such as a POJO.
:::

::: paragraph
For example, the snippet below selects all books from a JSON document,
which will be in a `List<Map>` object where each book is an entry as a
`Map`. Before Camel would attempt to convert `List` to `Book` which
would not be possible. From this release onwards, Camel will convert
each entry to a `Book` so the result is `List<Book>`.
:::

::: paragraph
This is also how `camel-jq` works.
:::

:::: listingblock
::: content
``` highlight
.transform().jsonpath(".book", Book.class)
```
:::
::::
::::::::

::::: sect2
### camel-kamelet {#_camel_kamelet}

::: paragraph
Routes created by Kamelets are no longer registered as JMX MBeans to
avoid cluttering up with unwanted MBeans, as a Kamelet is intended to
act like a Camel component, despite its implementation is Camel routes.
This means that the number of routes listed from JMX will no longer
include Kamelet routes.
:::

::: paragraph
The old behaviour can be enabled by setting
`registerRoutesCreateByKamelet=true` on the `ManagementAgent` object.
See more in the [JMX](jmx.html) documentation.
:::
:::::

:::::::::::: sect2
### camel-micrometer and camel-metrics {#_camel_micrometer_and_camel_metrics}

::: paragraph
The `camel-micrometer` have renamed tag `serviceName` to `kind` and use
naming that indicate that its from Camel:
:::

+-----------------------------------+-----------------------------------+
| **Before**                        | **After**                         |
+-----------------------------------+-----------------------------------+
| serviceName=\                     | kind=\"CamelExchangeEvent\"       |
| "MicrometerEventNotifierService\" |                                   |
+-----------------------------------+-----------------------------------+
| serviceName=\"                    | kind=\"CamelMessageHistory\"      |
| MicrometerMessageHistoryService\" |                                   |
+-----------------------------------+-----------------------------------+
| serviceName                       | kind=\"CamelRoute\"               |
| =\"MicrometerRoutePolicyService\" |                                   |
+-----------------------------------+-----------------------------------+

::: paragraph
Because the Kamelets were changed to act more like a Camel component,
and not expose internal details as JMX MBeans, then micrometer and
metrics no longer include statistics for those Kamelet routes.
:::

::: paragraph
The old behaviour can be enabled by setting
`registerRoutesCreateByKamelet=true` on the `ManagementAgent` object.
See more in the [JMX](jmx.html) documentation.
:::

::: paragraph
Added context level metrics to `camel-micrometer`. The metrics with key
`camel.route.policy` now include tag `eventType` that specifies if the
metrics is for a route or the entire camel context. You can turn off
context level metrics, by setting `contenxtEnabled=false` on the factory
such as:
:::

:::: listingblock
::: content
``` highlight
factory.getPolicyConfiguration().setContextEnabled(false);
```
:::
::::

::: paragraph
This can also be done easily from `application.properties` such as:
:::

:::: listingblock
::: content
``` highlight
camel.metrics.routePolicyLevel=route
```
:::
::::
::::::::::::

::::: sect2
### camel-openapi-java and camel-rest-openapi {#_camel_openapi_java_and_camel_rest_openapi}

::: paragraph
Dropped support for the old Swagger 2.0 spec. Only OpenAPI v3 specs is
supported now. Fixed maven dependencies to be JakartaEE compatible.
:::

::: paragraph
When using Rest DSL and have `api-doc` enabled via `camel-rest` and
`camel-openapi-java`, then the OpenAPI specification is now generated
once during startup instead of on-demand when a client calls the
`/api-doc` endpoint.
:::
:::::

:::: sect2
### camel-platform-http-vertx {#_camel_platform_http_vertx}

::: paragraph
Added a Cookie Handler allowing the addition, retrieval and expiry of
Cookies.
:::
::::

:::: sect2
### camel-shiro {#_camel_shiro}

::: paragraph
Upgraded Apache Shiro from 1.13 to 2.0.
:::
::::

:::: sect2
### camel-twilio {#_camel_twilio}

::: paragraph
Upgraded to Twilio 10.1.0 which removed `call-feedback` and
`call-feedback-summary` from the available APIs, to use from Camel.
:::
::::

:::: sect2
### camel-elasticsearch / camel-opensearch {#_camel_elasticsearch_camel_opensearch}

::: paragraph
The class
`org.apache.camel.component.opensearch.aggregation.BulkRequestAggregationStrategy`
has been renamed to
`org.apache.camel.component.opensearch.aggregation.OpensearchBulkRequestAggregationStrategy`
The class
`org.apache.camel.component.es.aggregation.BulkRequestAggregationStrategy`
has been renamed to
`org.apache.camel.component.es.aggregation.ElastichsearchBulkRequestAggregationStrategy`
:::
::::

:::: sect2
### camel-spring-redis {#_camel_spring_redis}

::: paragraph
The class
`org.apache.camel.component.redis.processor.idempotent.RedisIdempotentRepository`
has been renamed to
`org.apache.camel.component.redis.processor.idempotent.SpringRedisIdempotentRepository`
The class
`org.apache.camel.component.redis.processor.idempotent.RedisStringIdempotentRepository`
has been renamed to
`org.apache.camel.component.redis.processor.idempotent.SpringRedisStringIdempotentRepository`
:::
::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

::::::: sect1
## Camel Spring Boot {#_camel_spring_boot}

:::::: sectionbody
::: paragraph
The autoconfiguration with `camel.springboot.xxx` properties has been
harmonized to use same naming for all the Camel runtimes (`camel-main`,
`camel-quarkus`, and `camel-spring-boot`). These options have been
marked as deprecated, and you can migrate to use `camel.main.xxx` naming
instead.
:::

::: paragraph
For example, `camel.springboot.name = Foo` to `camel.main.name = Foo`.
:::

::: paragraph
Only the special Spring Boot options are still named
`camel.springboot.xxx`.
:::
::::::
:::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
