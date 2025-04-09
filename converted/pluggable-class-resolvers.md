::: {#header}
# Pluggable Class Resolvers
:::

::::::::::::: {#content}
:::::: {#preamble}
::::: sectionbody
::: paragraph
Camel provides pluggable class resolvers allowing third party platforms
and containers to provide their own resolvers in case the default ones
would not fit.
:::

::: paragraph
When running Camel on platforms such as Spring Boot, Quarkus, or Apache
Karaf, then Camel uses platform specific resolvers to support
classloading in these runtimes.
:::
:::::
::::::

:::::::: sect1
## Configuration of a custom class resolver {#_configuration_of_a_custom_class_resolver}

::::::: sectionbody
::: paragraph
To instruct Camel to use your own custom class resolver, you set the
resolver on the `CamelContext` using the appropriate setters; or
register a custom resolver in the [Registry](registry.html) then Camel
will automatically detect this during startup.
:::

::::: sect2
### SPI providers {#_spi_providers}

::: paragraph
Platform providers should look in the `org.apache.camel.spi` package for
the pluggable resolvers, such as:
:::

::: ulist
- `ClassResolver`

- `FactoryFinderResolver`

- `PackageScanClassResolver`

- `PackageScanResourceResolver`

- `ResourceResolver`
:::
:::::
:::::::
::::::::
:::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
