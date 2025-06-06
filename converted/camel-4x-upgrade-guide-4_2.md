::: {#header}
# Apache Camel 4.x Upgrade Guide
:::

:::::::::::::::::::::::::::::::::::::::::::: {#content}
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

:::::::::::::::::::::::::::::::::::::::: sect1
## Upgrading Camel 4.1 to 4.2 {#_upgrading_camel_4_1_to_4_2}

::::::::::::::::::::::::::::::::::::::: sectionbody
:::: sect2
### camel-api {#_camel_api}

::: paragraph
Added `boolean` parameter to `matchProcess` method on
`org.apache.camel.spi.Condition`.
:::
::::

:::: sect2
### camel-base-engine {#_camel_base_engine}

::: paragraph
The class `org.apache.camel.impl.DebuggerBacklogDebugger` has been
refactored to an interface `org.apache.camel.spi.BacklogDebugger` and a
default implementation
`org.apache.camel.impl.debugger.DefaultBacklogDebugger`.
:::
::::

:::: sect2
### camel-main {#_camel_main}

::: paragraph
The option `camel.main.debugger` has been renamed to
`camel.debug.enabled`.
:::
::::

::::: sect2
### camel-management-api {#_camel_management_api}

::: paragraph
The
`org.apache.camel.api.management.mbean.ManagedBacklogDebuggerMBean.dumpTracedMessagesAsXml(String, boolean)`
has been deprecated. The second parameter is ignored with default
implementation.
:::

::: paragraph
To continue to have access to exchange properties, call
`setIncludeExchangeProperties(true)` before messages are sent to have
access to exchange properties.
:::
:::::

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

::::: sect2
### camel-kafka {#_camel_kafka}

::: paragraph
The consumer will now pre-validate `hostname:port` and DNS resolution on
startup, and fail-fast in case of mis-configuration or invalid hostname.
:::

::: paragraph
This can be disabled by setting `preValidateHostAndPort=false`, which
will postpone validation to consumer is started, and will instead
re-connect endlessly (5 sec delay by default) until success.
:::
:::::

:::: sect2
### camel-observation / camel-opentelemetry {#_camel_observation_camel_opentelemetry}

::: paragraph
The `excludePatterns` option has changed from `Set<String>` to be
`String` where multiple patterns are separated by comma. This is more
configuration and tooling friendly and used by other components.
:::
::::

::::: sect2
### camel-saga, camel-lra {#_camel_saga_camel_lra}

::: paragraph
The `org.apache.camel.service.lra.LRAClient` can now access `Exchange`
to retrieve further context information. Therefore, there are following
changes in interface methods -
`org.apache.camel.saga.CamelSagaService.compensate()` changed to
`org.apache.camel.saga.CamelSagaService.compensate(Exchange exchange)` -
`org.apache.camel.saga.CamelSagaService.complete()` changed to
`org.apache.camel.saga.CamelSagaService.complete(Exchange exchange)` -
`org.apache.camel.saga.CamelSagaCoordinator.newSaga` is now
`org.apache.camel.saga.CamelSagaCoordinator.newSaga(Exchange exchange)`
to support the transport of `Exchange`.
:::

::: paragraph
As a result of interface changes, also the known implementation classes
and usages have been adopted.
:::
:::::

::::: sect2
### camel-ignite {#_camel_ignite}

::: paragraph
The Apache Ignite project (v2.15) is not yet Java 21 compatible and
therefore `camel-ignite` is not expected to function correctly if using
Java 21.
:::

::: paragraph
A new release of Apache Ignite is expected that will start to support
Java 21.
:::
:::::

:::::: sect2
### camel-dynamic-router EIP component (non-core) {#_camel_dynamic_router_eip_component_non_core}

::: paragraph
The Dynamic Router EIP component has been refactored to use the
Multicast Processor for sending messages to recipients, and this brought
some additional configuration options:
:::

::: ulist
- `parallelProcessing`, `parallelAggregate`, `stopOnException`,
  `ignoreInvalidEndpoints`, `streaming`, `timeout`, `onPrepare`,
  `shareUnitOfWork`, `executorService`, and `aggregationStrategy`. These
  configuration items are for configuring the Multicast Processor within
  the Dynamic Router EIP component, so their description, meaning, and
  effect are identical to that of the Multicast Processor.
:::

::: paragraph
When upgrading to this version, you should still be able to use this
component with the configuration from previous versions. The new options
allow more specific configuration if you need it.
:::
::::::

::::::::: sect2
### camel-spring & camel-spring-boot {#_camel_spring_camel_spring_boot}

:::::: sect3
#### Autowiring Primary beans {#_autowiring_primary_beans}

::: paragraph
Camel will now take into account `@Primary` beans from Spring when
autowiring by type. For example, a JDBC `DataSource` in the SQL
component will now use the `@Primary` data source when multiple data
sources are defined.
:::

::: paragraph
Previously, Camel would not autowire if there are two or more beans for
a given type.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | This is a change in behaviour,    |
| Note                              | that can affect your applications |
| :::                               | when upgrading.                   |
+-----------------------------------+-----------------------------------+
:::
::::::

:::: sect3
#### Properties {#_properties}

::: paragraph
The `initialProperties` and `overrideProperties` on Camel
`PropertiesComponent` will now take precedence over Spring Boot
properties. This can be used for testing purpose, to allow overriding
properties when using `CamelTestSupport` for unit testing.
:::
::::
:::::::::

:::: sect2
### camel-opentelemetry-starter {#_camel_opentelemetry_starter}

::: paragraph
The `excludePatterns` option has changed from `Set<String>` to be
`String` where multiple patterns are separated by comma. This is more
configuration and tooling friendly and used by other components.
:::
::::
:::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
