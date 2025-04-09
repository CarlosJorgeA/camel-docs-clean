::: {#header}
# Apache Camel 3.x Upgrade Guide
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
::::: {#preamble}
:::: sectionbody
::: paragraph
This document is for helping you upgrade your Apache Camel application
from Camel 3.x to 3.y. For example, if you are upgrading Camel 3.0 to
3.2, then you should follow the guides from both 3.0 to 3.1 and 3.1 to
3.2.
:::
::::
:::::

::::::::: sect1
## Upgrading Camel 3.21 to 3.21.3 {#_upgrading_camel_3_21_to_3_21_3}

:::::::: sectionbody
::::::: sect2
### camel-kafka {#_camel_kafka}

::: paragraph
The behavior for `breakOnFirstError` was altered as numerous issues were
fixed. The behavior related to committing the offset is now determined
by the `CommitManager` that is configured.
:::

::: paragraph
When the default `CommitManager` is used (`NoopCommitManager`) then no
commit is performed. The route implementation will be responsible for
managing the offset using `KafkaManualCommit` to manage the retrying of
the payload.
:::

::: paragraph
When using the `SyncCommitManager` then the offset will be committed so
that the payload is continually retried. This was the behavior described
in the documentation.
:::

::: paragraph
When using the `AsyncCommitManager` then the offset will be committed so
that the payload is continually retried. This was the behavior described
in the documentation.
:::
:::::::
::::::::
:::::::::

:::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Upgrading Camel 3.20 to 3.21 {#_upgrading_camel_3_20_to_3_21}

::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
:::::::::: sect2
### camel-core {#_camel_core}

::: paragraph
XML parsers & XML transformers has been made more secure by disabling
DOCTYPE and/or access to external DTD/Schema.
:::

::: paragraph
The `org.apache.camel.support.EventNotifierSupport` abstract class now
implements `CamelContextAware`.
:::

::::: sect3
#### Tracing {#_tracing}

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
end, and logging the header afterward, may appear as the header value is
empty.
:::
:::::

:::: sect3
#### UseOriginalMessage / UseOriginalBody {#_useoriginalmessage_useoriginalbody}

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
::::::::::

::::: sect2
### camel-console {#_camel_console}

::: paragraph
The class `org.apache.camel.impl.console.AbstractDevConsole` has moved
from `camel-console` to `camel-support` and moved to package
`org.apache.camel.support.console.AbstractDevConsole`.
:::

::: paragraph
Component developers that has plugins for Camel Developer Console,
should just use `camel-support` and `camel-util-json` as dependencies,
and then extend `AbstractDevConsole` for custom console implementations.
:::
:::::

::::: sect2
### camel-health {#_camel_health}

::: paragraph
The `org.apache.camel.health.HealthCheck` method `isLiveness` is now
default `false` instead of `true`.
:::

::: paragraph
Camel provides the `CamelContextCheck` as both readiness and liveness
checks, so there is at least one of each out of the box.
:::
:::::

:::: sect2
### camel-main {#_camel_main}

::: paragraph
The option `camel.main.routesReloadRestartDuration` has changed its
default value from `true` to `false`.
:::
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

:::: sect2
### camel-http-common {#_camel_http_common}

::: paragraph
The API in `org.apache.camel.http.common.HttpBinding` has changed
slightly to be more reusable. The `parseBody` method now takes in
`HttpServletRequest` as input parameter. And all `HttpMessage` has been
changed to generic `Message` types.
:::
::::

:::: sect2
### camel-jaxb {#_camel_jaxb}

::: paragraph
When using schema validation, then access to external DTD/Schema is
disabled by default. To enable access, then set
`accessExternalSchemaProtocols=all` or specify allowed protocols, such
as `accessExternalSchemaProtocols=file`.
:::
::::

:::: sect2
### camel-platform-http-vertx {#_camel_platform_http_vertx}

::: paragraph
If the route or consumer is suspended, then http status 503 is now
returned instead of 404.
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

:::: sect2
### camel-stax {#_camel_stax}

::: paragraph
XML parser in `StAXJAXBIteratorExpression` has been made more secure by
disabling DOCTYPE and/or access to external DTD/Schema.
:::
::::

:::: sect2
### camel-cm-sms {#_camel_cm_sms}

::: paragraph
XML parsers have been made more secure by disabling `DOCTYPE` and/or
access to external DTD/Schema.
:::
::::

:::: sect2
### camel-schematron {#_camel_schematron}

::: paragraph
XML parsers have been made more secure by disabling `DOCTYPE` and/or
access to external DTD/Schema.
:::
::::

:::: sect2
### camel-xmlsecurity {#_camel_xmlsecurity}

::: paragraph
XML parsers have been made more secure by disabling `DOCTYPE` and/or
access to external DTD/Schema.
:::
::::

:::::: sect2
### camel-jbang {#_camel_jbang}

::: paragraph
The command `camel dependencies` has been renamed to `camel dependency`.
:::

::: paragraph
In Camel JBang the `-dir` parameter for `init` and `run` goal has been
renamed to require 2 dashes `--dir` like all the other options.
:::

::: paragraph
The *Placeholders substitutes* is changed to use `#name` instead of
`$name` syntax.
:::
::::::

:::: sect2
### camel-java-joor-dsl {#_camel_java_joor_dsl}

::: paragraph
The `camel-java-joor-dsl` cannot anymore load routes defined in class
files as we consider it no more needed, consequently the ability to
configure the compile directory and the compile load first flag using
the corresponding `camel-main` properties is no longer possible.
:::
::::

:::: sect2
### camel-elasticsearch {#_camel_elasticsearch}

::: paragraph
The `certificatePath` parameter can now be specified as resource: for
user pointing to a local certificate, the file should be now prefixed
with `file:`. It's also possible to use the classic resource helper
prefixes, like `classpath`, `https` etc.
:::
::::

:::: sect2
### camel-rest {#_camel_rest}

::: paragraph
The deprecated option `componentName` was removed. Please use either
`producerComponentName` or `consumerComponentName`.
:::
::::

::::: sect2
### Deprecated Components {#_deprecated_components}

::: paragraph
The following components that had been marked as deprecated, were
removed in this release:
:::

::: ulist
- camel-dozer

- camel-cmis

- camel-vertx-kafka
:::
:::::

::::: sect2
### Backlog Tracing {#_backlog_tracing}

::: paragraph
The option `backlogTracing=true` now automatic enabled the tracer on
startup. The previous behavior was *surprisingly* that the tracer was
only made available, and had to be manually enabled afterwards. The old
behavior can be archived by setting `backlogTracingStandby=true`.
:::

::: paragraph
Move the following class from
`org.apache.camel.api.management.mbean.BacklogTracerEventMessage` in
`camel-management-api` JAR to
`org.apache.camel.spi.BacklogTracerEventMessage` in `camel-api` JAR.
:::
:::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::: sect1
## Camel Spring Boot {#_camel_spring_boot}

::::::::::::::::::: sectionbody
::: paragraph
The `camel-spring-boot` dependency no longer includes
`camel-spring-xml`. To use legacy Spring XML files `<beans>` with Camel
on Spring Boot, then include the `camel-spring-boot-xml-starter`
dependency.
:::

::::::: sect2
### Health Check {#_health_check}

::: paragraph
The health-check has aligned to be more similar to microprofile-health
in the JSon output. Spring Boot now also includes additional data per
check, when using full exposure level.
:::

::: paragraph
For example, as shown below for the context health check:
:::

:::: listingblock
::: content
``` highlight
{
  "status": "UP",
  "components": {
    "camelHealth": {
      "status": "UP",
      "details": {
        "name": "camel-health-check",
        "context": "UP",
        "context.data": {
          "invocation.count": "1",
          "context.phase": "5",
          "invocation.time": "2022-12-21T09:12:03.307871+01:00[Europe/Oslo]",
          "check.group": "camel",
          "context.name": "MyCamel",
          "success.time": "2022-12-21T09:12:03.307871+01:00[Europe/Oslo]",
          "success.count": "1",
          "check.id": "context",
          "context.version": "3.21.0",
          "context.status": "Started",
          "success.start.time": "2022-12-21T09:12:03.307871+01:00[Europe/Oslo]",
          "check.kind": "READINESS",
          "failure.count": "0"
        }
      }
    }
  }
}
```
:::
::::
:::::::

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
If the route or consumer is suspended, then http status 503 is now
returned instead of 404.
:::

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
Then calling *`myservice`* would before require including the
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
::::::::::
:::::::::::::::::::
::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
