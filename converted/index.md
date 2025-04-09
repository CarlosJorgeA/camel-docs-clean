::: {#header}
# Apache Camel user manual
:::

:::::::::::::::::::::::::: {#content}
::::::: {#preamble}
:::::: sectionbody
::: paragraph
Apache Camel™ is a versatile open-source integration framework based on
known [Enterprise Integration
Patterns](components:eips:enterprise-integration-patterns.html).
:::

::: paragraph
Camel empowers you to define routing and mediation rules in a variety of
domain-specific languages ([DSL](manual::dsl.html), such as Java, XML,
Groovy, and YAML). This means you get smart completion of routing rules
in your IDE, whether in a Java or XML editor.
:::

::: paragraph
For a deeper and better understanding of Apache Camel, an
[Introduction](faq:what-is-camel.html) is provided.
:::
::::::
:::::::

::::: sect1
## Summary {#_summary}

:::: sectionbody
::: ulist
- [Overview](#_overview)

- [Documentation](#_documentation)

- [Developers](#_developers)
:::
::::
:::::

::::: sect1
## Overview {#_overview}

:::: sectionbody
::: ulist
- [Getting Started](getting-started.html)

- [Longer Getting Started Guide](book-getting-started.html)

- [FAQ](faq:index.html)

- [Legal Notice](https://github.com/apache/camel/blob/main/NOTICE.txt)
:::
::::
:::::

::::::::::: sect1
## Documentation {#_documentation}

:::::::::: sectionbody
::: ulist
- [User Guide](#_user_guide)

- [Books](/community/books/)

- [Examples](examples.html)

- [Architecture](architecture.html)

- [Enterprise Integration
  Patterns](components:eips:enterprise-integration-patterns.html)

- [Component DSL](component-dsl.html)

- [Endpoint DSL](Endpoint-dsl.html)

- [DataFormat DSL](dataformat-dsl.html)

- [Language DSL](language-dsl.html)

- [DSL](dsl.html)

- [Components](components::index.html)

- [Data Formats](components:dataformats:index.html)

- [Languages](languages.html)
:::

:::: sect2
### User Guide {#_user_guide}

::: ulist
- [Getting Started](getting-started.html)

- [Longer Getting Started Guide](book-getting-started.html)

- [Working with Camel and Spring](spring.html)

- [How do I configure endpoints?](faq:how-do-i-configure-endpoints.html)

- [Auto Configuration](camelcontext-autoconfigure.html)

- [Bean Integration](bean-integration.html)

- [Configuring route startup ordering and
  autostartup](configuring-route-startup-ordering-and-autostartup.html)

- [Graceful Shutdown](graceful-shutdown.html)

- [Error handler](error-handler.html)

- [How to use Camel property
  placeholders](using-propertyplaceholder.html)

- [How to use Variables](variables.html)

- [Examples](examples.html)

- [Testing](testing.html)

- [Camel Maven Plugin](camel-maven-plugin.html) for running Camel from a
  Maven goal

- [Camel Component Maven Plugin](camel-component-maven-plugin.html) to
  generate all the necessary metadata and Java file for your custom
  component.

- [Camel Maven Report Plugin](camel-report-maven-plugin.html) to
  validate your source code and report the code coverage of your camel
  routes

- [Camel Maven Archetypes](camel-maven-archetypes.html)

- [Camel JMX](components::jmx-component.html)

- [Working with REST and Rest DSL](rest-dsl.html)

  ::: ulist
  - [Rest DSL contract first with OpenAPI](rest-dsl-openapi.html)
  :::

- [Writing Custom Camel Components](writing-components.html)
:::
::::

:::: sect2
### [Architecture](architecture.html) {#_architecture}

::: ulist
- [BacklogDebugger](backlog-debugger.html)

- [BacklogTracer](backlog-tracer.html)

- [Batch Consumer](batch-consumer.html)

- [BrowsableEndpoint](browsable-endpoint.html)

- [CamelContext](camelcontext.html)

- [Clustering and loadbalancing](clustering.html)

- [Component](components::index.html)

- [Data Format](data-format.html)

- [Debugger](debugger.html)

- [Delayer](delay-interceptor.html)

- [DSL](dsl.html)

- [Endpoint](endpoint.html)

- [Endpoint Annotations](endpoint-annotations.html)

- [Error Handler](error-handler.html)

- [Exchange](exchange.html)

- [Exchange Pattern](exchange-pattern.html)

- [Exchange Pooling](exchange-pooling.html)

- [Expression](expression.html)

- [Event Notifier](event-notifier.html)

- [HTTP-Session Handling](http-session-handling.html)

- [Injector](injector.html)

- [Intercept](components:eips:intercept.html)

- [Languages](languages.html)

- [Lifecycle](lifecycle.html)

- [OnCompletion](oncompletion.html)

- [Pluggable Class Resolvers](pluggable-class-resolvers.html)

- [Predicate](predicate.html)

- [Processor](processor.html)

- [Registry](registry.html)

- [RouteBuilder](route-builder.html)

- [LambdaRouteBuilder](lambda-route-builder.html)

- [RouteController](route-controller.html)

- [RoutePolicy](route-policy.html)

- [RouteConfiguration](route-configuration.html)

- [ContextReload](context-reload.html)

- [RouteReload](route-reload.html)

- [RouteTemplate](route-template.html)

- [Routes](routes.html)

- [Stream caching](stream-caching.html)

- [Threading Model](threading-model.html)

- [Tracer](tracer.html)

- [Type Converter](type-converter.html)

- [URIs](uris.html)

- [UuidGenerator](uuidgenerator.html)
:::
::::

:::: sect2
### [DSL](dsl.html) {#_dsl}

::: ulist
- [Java DSL](java-dsl.html)

- [Java Annotation DSL](bean-integration.html)

- [Spring XML DSL](components::spring-summary.html)

- [Rest DSL](rest-dsl.html)
:::
::::
::::::::::
:::::::::::

::::: sect1
## Developers {#_developers}

:::: sectionbody
::: ulist
- [Building Camel from Source](building.html)
:::
::::
:::::
::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
