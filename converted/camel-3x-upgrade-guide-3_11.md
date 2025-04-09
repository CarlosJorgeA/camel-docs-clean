::: {#header}
# Apache Camel 3.x Upgrade Guide
:::

::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
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

::::::::::::::::::::::::::::::::::::::::::::: sect1
## Upgrading Camel 3.10 to 3.11 {#_upgrading_camel_3_10_to_3_11}

:::::::::::::::::::::::::::::::::::::::::::: sectionbody
:::: sect2
### Bean Language {#_bean_language}

::: paragraph
When using `beanType` (full qualified class name) with the bean
language, then the bean language will **now** look up in the registry,
and if there is a single instance of the same type, then the existing
bean is used. If not, then a new bean is created (same behavior as
before).
:::
::::

::::: sect2
### Aggregate EIP {#_aggregate_eip}

::: paragraph
The aggregate EIP will now always use a worker thread pool for
processing outgoing messages. Previously a worker pool was only created
when `parallelProcessing=true`. When having `parallelProcessing=false`
then a single-threaded worker pool is created and used by the
aggregator.
:::

::: paragraph
Camels routing engine works better when a dedicated worker pool that
eliminates a *hack* that otherwise was necessary in other EIPs that was
affected if using the aggregator in some special configurations.
:::
:::::

:::: sect2
### RemoveHeader EIP {#_removeheader_eip}

::: paragraph
In XML and YAML DSL the `headerName` attribute has been deprecated, and
you should use `name` instead. This is to make the EIP consistent with
the naming used by other similar EIPs.
:::
::::

::::::::::: sect2
### Routes loader {#_routes_loader}

::: paragraph
The default pattern for discovering Camel routes et all from the
classpath has changed from only including XML files to now include all
files.
:::

::: paragraph
The option `routesIncludePattern` is changed from:
:::

:::: listingblock
::: content
    classpath:camel/*.xml,classpath:camel-template/*.xml,classpath:camel-rest/*.xml`
:::
::::

::: paragraph
to:
:::

:::: listingblock
::: content
    classpath:camel/*,classpath:camel-template/*,classpath:camel-rest/*
:::
::::

::: paragraph
This is from the `camel-main` module which is for running Camel
standalone, but also reused for Camel on Spring Boot, or Camel on
Quarkus.
:::
:::::::::::

:::: sect2
### camel-apns {#_camel_apns}

::: paragraph
The `camel-apns` component has been removed as the APNS (Apple Push
Notification Service) online service has been retired y Apple.
:::
::::

:::: sect2
### camel-mongodb {#_camel_mongodb}

::: paragraph
The `streamFilter` option should now be configured as endpoint uri
parameter, instead of a route property.
:::
::::

:::: sect2
### camel-saxon {#_camel_saxon}

::: paragraph
The camel-saxon component no longer depends on camel-xslt-saxon. Any
applications in need of XSLT transformation with saxon should now
declare an explicit dependency upon camel-xslt-saxon.
:::
::::

:::::::::::::: sect2
### camel-maven-plugin with OSGi blueprint {#_camel_maven_plugin_with_osgi_blueprint}

::: paragraph
The `run` goal of the `camel-maven-plugin` has moved its OSGi Blueprint
support out to its own `camel-karaf-maven-plugin`.
:::

::: paragraph
This means if you use Camel on OSGi Blueprint then you need to migrate
from:
:::

:::: listingblock
::: content
``` highlight
<plugin>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-maven-plugin</artifactId>
    <version>3.11.0</version>
</plugin>
```
:::
::::

::: paragraph
to
:::

:::: listingblock
::: content
``` highlight
<plugin>
    <groupId>org.apache.camel.karaf</groupId>
    <artifactId>camel-karaf-maven-plugin</artifactId>
    <version>3.11.0</version>
</plugin>
```
:::
::::

::: paragraph
In the `pom.xml` file.
:::

::: paragraph
Also remember to execute the `run` goal, you should use
`camel-karaf:run` instead of `camel:run` as shown:
:::

:::: listingblock
::: content
``` highlight
mvn camel-karaf:run
```
:::
::::
::::::::::::::

::::: sect2
### camel-sql {#_camel_sql}

::: paragraph
Support for deprecated use of named dataSource in the URI has been
removed.
:::

::: paragraph
You have to use
`sql:select * from table where id=# order by name?dataSource=#myDS`
instead of
`sql:select * from table where id=# order by name?dataSource=myDS`.
:::
:::::

:::: sect2
### Spring Boot Starters {#_spring_boot_starters}

::: paragraph
Some of the Camel Spring Boot starters have additional autoconfiguration
options that clashed with component. Therefore, those configurations
have renamed their configuration keys:
:::

+-----------------------------------+-----------------------------------+
| **Old Key prefix**                | **New key prefix**                |
+-----------------------------------+-----------------------------------+
| camel.                            | camel.cloud.consul                |
| component.consul.service-registry |                                   |
+-----------------------------------+-----------------------------------+
| ca                                | camel.cloud.consul                |
| mel.cloud.consul.service-registry |                                   |
+-----------------------------------+-----------------------------------+
::::

:::: sect2
### Apache Karaf {#_apache_karaf}

::: paragraph
The `camel-grpc` feature has been removed.
:::
::::
::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
