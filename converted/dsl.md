::: {#header}
# DSL
:::

:::::::::: {#content}
:::::: {#preamble}
::::: sectionbody
::: paragraph
Camel uses a Java *Domain Specific Language* or DSL for creating
[Enterprise Integration
Patterns](components:eips:enterprise-integration-patterns.html) or
[Routes](routes.html) in a variety of domain-specific languages (DSL) as
listed below:
:::

::: ulist
- [Java DSL](java-dsl.html): a Java-based DSL using the fluent builder
  style.

- [XML DSL](components:others:java-xml-io-dsl.html): an XML-based DSL in
  Camel XML files only.

- [Spring XML](components::spring-summary.html): an XML-based DSL in
  classic Spring XML files.

- [YAML DSL](components:others:yaml-dsl.html): for creating routes using
  YAML format.

- [Rest DSL](rest-dsl.html): a DSL to define REST services using REST
  verbs.

  ::: ulist
  - [Rest DSL contract first](rest-dsl-openapi.html): rest DSL using
    *contract-first* when OpenAPI specs.
  :::

- [Annotation DSL](bean-integration.html): Use annotations in Java
  beans.
:::
:::::
::::::

::::: sect1
## See Also {#_see_also}

:::: sectionbody
::: ulist
- [CamelContext](camelcontext.html) the main entry for Camel is the
  `CamelContext`

- [Routes](routes.html) for general information about a Camel route

- [RouteBuilder](route-builder.html) for creating routes using the Java
  DSL style.

- [LambdaRouteBuilder](lambda-route-builder.html) for creating routes
  using Java lambda style.

- [Endpoint DSL](Endpoint-dsl.html) for creating routes using type-safe
  Camel endpoints in Java.

- [DataFormat DSL](dataformat-dsl.html) for type-safe Camel data formats
  in Java.

- [Route Template](route-template.html) for creating reusable route
  templates.

- [Route Reload](route-reload.html) for hot-reloading routes in a
  running Camel application.
:::
::::
:::::
::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
