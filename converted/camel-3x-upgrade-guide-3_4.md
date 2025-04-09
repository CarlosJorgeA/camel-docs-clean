::: {#header}
# Apache Camel 3.x Upgrade Guide
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
::::: {#preamble}
:::: sectionbody
::: paragraph
This document is for helping you upgrade your Apache Camel application
from Camel 3.x to 3.y. For example if you are upgrading Camel 3.0 to
3.2, then you should follow the guides from both 3.0 to 3.1 and 3.1 to
3.2.
:::
::::
:::::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Upgrading Camel 3.3 to 3.4 {#_upgrading_camel_3_3_to_3_4}

:::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::::::: sect2
### camel-test and JMX {#_camel_test_and_jmx}

::: paragraph
The `camel-test` module no longer has dependency on `camel-management`
out of the box. In Camel JMX is optional and to use JMX then
`camel-management` must be added as dependency.
:::

::: paragraph
For example to use JMX during testing you the following dependency as
test scope:
:::

:::: listingblock
::: content
``` highlight
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-management</artifactId>
    <scope>test</scope>
</dependency>
```
:::
::::
:::::::

:::::: sect2
### Health Check {#_health_check}

::: paragraph
The default health check implementation has been moved out of
`camel-base` into `camel-health` as a separate module. The health check
has been refactored and there are some APIs and classes that has been
changed. The health check in `camel-consul` has been removed.
:::

::: paragraph
The old health check was not fully implemented and was only targeting
Spring Boot. The new system is more generic and supports all Camel
runtimes.
:::

::: paragraph
An example is provided in `camel-example-main-health`.
:::
::::::

:::::: sect2
### Template components {#_template_components}

::: paragraph
The template components which allows access to the current `Exchange`
and `CamelContext` API from the context map available for templating has
now been restricted to only the message body and headers.
:::

::: paragraph
This option can be enabled (`allowContextMapAll=true`) for full access
to the current Exchange and CamelContext. Doing so impose a potential
security risk as this opens access to the full power of CamelContext
API.
:::

::: paragraph
This applies to the following templating components: camel-freemarker,
camel-velocity, camel-mvel, camel-mustache, camel-string-template,
camel-chunk, camel-robotframework.
:::
::::::

::::::::: sect2
### Using custom language in RouteBuilder {#_using_custom_language_in_routebuilder}

::: paragraph
The Java DSL `RouteBuilder` allows referring to a custom language as
shown below:
:::

:::: listingblock
::: content
``` highlight
from("direct:start")
    .filter(language("foo", "Bla bla bla"))
      .to("mock:camel");
```
:::
::::

::: paragraph
This functionality is seldom in use, as you would use the provided
languages from Camel. If using, then the `language` method now requires
a static import as shown below:
:::

:::: listingblock
::: content
``` highlight
import static org.apache.camel.builder.Builder.language;
```
:::
::::
:::::::::

::::::::::::: sect2
### Using custom type converters {#_using_custom_type_converters}

::: paragraph
If you are using custom type converters, you can now generate loader
classes and have Camel discover them automatically. To generate loader
class, Change `@Converter` to `@Converter(generateLoader = true)` at
class level and implement maven plugin as described in
[here](camel-component-maven-plugin.html).
:::

::: paragraph
If a loader class is not to be used, Camel needs to be instructed to
scan for custom converters. This can be done as shown:
:::

:::: listingblock
::: content
``` highlight
context.setLoadTypeConverters(true);
```
:::
::::

::: paragraph
And in XML:
:::

:::: listingblock
::: content
``` highlight
<camelContext loadTypeConverters="true">

</camelContext>
```
:::
::::

::: paragraph
If you are using Camel on Spring Boot, Quarkus or via Camel Main this
can also be configured in the `application.properties`:
:::

:::: listingblock
::: content
``` highlight
camel.main.loadTypeConverters=true
```
:::
::::
:::::::::::::

:::: sect2
### camel-spring-boot {#_camel_spring_boot}

::: paragraph
The `/actuator/camelroutes` HTTP endpoint has been removed from provided
Spring Boot actuators in `camel-spring-boot`. This actuator was becoming
problematic to maintain during Spring Boot upgrades due to changes in
Spring Boot.
:::
::::

::::: sect2
### camel-servlet and camel-http-common {#_camel_servlet_and_camel_http_common}

::: paragraph
`HttpRegistry` and `DefaultHttpRegistry` classes from camel-servlet are
moved from camel-servlet into camel-http-common. `HttpRegistryProvider`
is added and used in `DefaultHttpRegistry` instead of `CamelServlet`.
:::

::: paragraph
These changes had effects on camel-atmosphere-websocket and
camel-servlet and also camel-resteasy. Users of these components where
they would have custom implemetations on `DefaultHttpRegistry` and using
`CamelServlet` class should take this change into account.
:::
:::::

:::::: sect2
### camel-sql {#_camel_sql}

::: paragraph
The `JdbcAggregationRepository` optimistic locking feature has been
fixed to work on a distributed environment and every database. There is
a new `version` column that is required and must be added to the
repository:
:::

:::: listingblock
::: content
``` highlight
CREATE TABLE aggregation (
 id varchar(255) NOT NULL,
 exchange blob NOT NULL,
 version BIGINT NOT NULL,
 constraint aggregation_pk PRIMARY KEY (id)
);
CREATE TABLE aggregation_completed (
 id varchar(255) NOT NULL,
 exchange blob NOT NULL,
 version BIGINT NOT NULL,
 constraint aggregation_completed_pk PRIMARY KEY (id)
);
```
:::
::::
::::::

::::: sect2
### Thread Pool default changed {#_thread_pool_default_changed}

::: paragraph
The thread pools managed and created by Camels
\'ExecutorServiceManager\' or `ThreadPoolFactory` is now set to default
created thread pools with `allowCoreThreadTimeOut=true`. This means that
the thread pool can shrink and terminate also the core threads if the
pool is idle.
:::

::: paragraph
In older versions this was `allowCoreThreadTimeOut=false` and the
default core size was 10, which means thread pools would typically have
10 threads as minimum. With this change those thread pools can shrink to
lower value when core threads become idle as well.
:::
:::::

:::: sect2
### camel-lra-starter {#_camel_lra_starter}

::: paragraph
The Spring Boot LRA starter component have changed its auto
configuration keys to be similar to using camel-lra with camel-main or
camel-quarkus. Change the keys from `camel.service.lra` to `camel.lra`,
eg `camel.service.lra.coordinator-url` to `camel.lra.coordinator-url`.
:::
::::

:::: sect2
### Maven Archetypes {#_maven_archetypes}

::: paragraph
The `camel-archetype-java8` has been removed, as you can just use
`camel-archetype-java` instead.
:::
::::

:::: sect2
### Maven Plugins {#_maven_plugins}

::: paragraph
A new maven plugin called `camel-component-maven-plugin` has been added
which intents to help third party component developers to generate all
required metadata and configurations Java files. For more info on how to
use it in your project, please take a look at plugin's documentation
[here](camel-component-maven-plugin.html).
:::
::::

:::: sect2
### Camel-Kafka {#_camel_kafka}

::: paragraph
From a lot of time, the headerFilterStrategy application in consumer and
producer was done in an interchanged way. You need to have a look at the
following issue for more information:
[https://issues.apache.org/jira/browse/CAMEL-15121](https://issues.apache.org/jira/browse/CAMEL-15121){.bare}
:::
::::

:::: sect2
### Camel-Karaf {#_camel_karaf}

::: paragraph
Removed `camel-hdfs` and `camel-pulsar` Karaf features.
:::
::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
