::: {#header}
# Spring
:::

::::::::::::::::::::::::::: {#content}
:::::: {#preamble}
::::: sectionbody
::: paragraph
Apache Camel is designed to work first class with Spring in a number of
ways, such as:
:::

::: ulist
- Camel runs on Spring Boot with the Camel Spring Boot project

- Camel works with Spring XML files (classic Spring XML)

- Camel works with Spring dependency injection

- Camel works with Spring configuration and property placeholders

- Camel works with Spring transactions

- Camel works with Spring testing
:::
:::::
::::::

::::: sect1
## Using Camel on Spring Boot {#_using_camel_on_spring_boot}

:::: sectionbody
::: paragraph
See the Camel Spring Boot documentation.
:::
::::
:::::

:::::: sect1
## Using Camel with Spring XML files {#_using_camel_with_spring_xml_files}

::::: sectionbody
::: paragraph
Using Camel with Spring XML files, is a classic way, of using XML DSL
with Camel. Camel has historically been using Spring XML for a long
time. The Spring framework started with XML files as a popular and
common configuration for building Spring applications.
:::

::: paragraph
To use Camel with Spring XML files see the [Spring
XML](spring-xml-extensions.html) documentation.
:::
:::::
::::::

:::::: sect1
## Using Spring dependency injection {#_using_spring_dependency_injection}

::::: sectionbody
::: paragraph
Spring dependency injection is integrated first-class when using Spring
and Spring together.
:::

::: paragraph
For example when using Camel on Spring Boot, then you can use any kind
of Spring dependency and be able to inject Camel resources such as
\'CamelContext\', [Endpoint](endpoint.html) and many more.
:::
:::::
::::::

::::: sect1
## Using Camel with Spring configuration and property placeholders {#_using_camel_with_spring_configuration_and_property_placeholders}

:::: sectionbody
::: paragraph
See [Using Property Placeholder](using-propertyplaceholder.html)
documentation.
:::
::::
:::::

::::: sect1
## Using Camel with Spring transactions {#_using_camel_with_spring_transactions}

:::: sectionbody
::: paragraph
See [Transactional Client](components:eips:transactional-client.html)
EIP.
:::
::::
:::::

::::: sect1
## Using Camel with Spring testing {#_using_camel_with_spring_testing}

:::: sectionbody
::: paragraph
See
[camel-test-spring-junit5](components:others:test-spring-junit5.html)
documentation.
:::
::::
:::::
:::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
