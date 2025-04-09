::: {#header}
# Apache Camel 4.x Upgrade Guide
:::

:::::::::::::::::::::::::::::::::::::::::::::::: {#content}
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

:::::: sect1
## Upgrading from 4.8.2 to 4.8.3 {#_upgrading_from_4_8_2_to_4_8_3}

::::: sectionbody
:::: sect2
### camel-mina {#_camel_mina}

::: paragraph
If using object codec, then you should configure the
`objectCodecPattern` configuration to specify which java classes (FQN)
to allow for Object serialization. You can use `*` to accept all
patterns.
:::
::::
:::::
::::::

::::::::: sect1
## Upgrading from 4.8.1 to 4.8.2 {#_upgrading_from_4_8_1_to_4_8_2}

:::::::: sectionbody
::::: sect2
### camel-management {#_camel_management}

:::: sect3
#### Using Route Templates {#_using_route_templates}

::: paragraph
Camel will now ensure the created routes via route templates always use
unique *nodePrefixId* to ensure there are no duplicate id clashes, when
the template is used for creating multiple routes.
:::
::::
:::::

:::: sect2
### camel-debezium {#_camel_debezium}

::: paragraph
To avoid split package that can be a problem in environments like OSGI,
each camel-debezium module has its own sub package corresponding to the
database type. So for example, all the classes of the module
`camel-debezium-postgres` have been moved to a dedicated package which
is `org.apache.camel.component.debezium.postgres` instead of having
everything under the root package `org.apache.camel.component.debezium`.
:::
::::
::::::::
:::::::::

:::::::: sect1
## Upgrading from 4.8.0 to 4.8.1 {#_upgrading_from_4_8_0_to_4_8_1}

::::::: sectionbody
::: paragraph
The `camel-opentelemetry` component has had significant bug fixes to
handle span activation/deactivations better when Camel route messages
synchronously and asynchronously. This component should also work better
on Spring Boot.
:::

::: paragraph
In order to fix the problems reported we had to align the Opentelemetry
dependencies to a version which is different from the one used in Spring
Boot 3.3.x BOM. If you're using such BOM for your **Camel Spring Boot
runtime** application, you will need to make sure to force the usage of
the Opentelemetry dependencies below:
:::

:::: listingblock
::: content
``` highlight
<dependency>
    <groupId>io.opentelemetry</groupId>
    <artifactId>opentelemetry-sdk</artifactId>
    <version>1.43.0</version>
</dependency>
<dependency>
    <groupId>io.opentelemetry</groupId>
    <artifactId>opentelemetry-api</artifactId>
    <version>1.43.0</version>
</dependency>
<dependency>
    <groupId>io.opentelemetry</groupId>
    <artifactId>opentelemetry-context</artifactId>
    <version>1.43.0</version>
</dependency>
<dependency>
    <groupId>io.opentelemetry</groupId>
    <artifactId>opentelemetry-logs</artifactId>
    <version>1.43.0</version>
</dependency>
<dependency>
    <groupId>io.opentelemetry</groupId>
    <artifactId>opentelemetry-metrics</artifactId>
    <version>1.43.0</version>
</dependency>
<dependency>
    <groupId>io.opentelemetry</groupId>
    <artifactId>opentelemetry-sdk-common</artifactId>
    <version>1.43.0</version>
</dependency>
<dependency>
    <groupId>io.opentelemetry</groupId>
    <artifactId>opentelemetry-sdk-metrics</artifactId>
    <version>1.43.0</version>
</dependency>
<dependency>
    <groupId>io.opentelemetry</groupId>
    <artifactId>opentelemetry-sdk-logs</artifactId>
    <version>1.43.0</version>
</dependency>
<dependency>
    <groupId>io.opentelemetry</groupId>
    <artifactId>opentelemetry-sdk-trace</artifactId>
    <version>1.43.0</version>
</dependency>
<dependency>
    <groupId>io.opentelemetry</groupId>
    <artifactId>opentelemetry-api-incubator</artifactId>
    <version>1.43.0-alpha</version>
</dependency>
```
:::
::::
:::::::
::::::::

::::::::::::::::::::::::::: sect1
## Upgrading Camel 4.7 to 4.8 {#_upgrading_camel_4_7_to_4_8}

:::::::::::::::::::::::::: sectionbody
:::: sect2
### camel-api {#_camel_api}

::: paragraph
Added
`void bind(String id, Class<?> type, Object bean, String initMethod, String destroyMethod)`
method to `org.apache.camel.spi.Registry` to support init and destroy
method on beans.
:::
::::

::::::: sect2
### camel-core {#_camel_core}

::: paragraph
The `UseOriginalAggregationStrategy` class will now propagate the caught
exception stored in the exchange property `Exchange.EXCEPTION_CAUGHT` as
well. For example, when using the Splitter EIP with this, then any
caught exception during splitting would be stored as well, which allows
access to this information afterward, for example, in an `onCompletion`
where the caught exception can be used to know some error happened
during splitting.
:::

::: paragraph
Internally, Camel will now use Java's own `InputStream.transferTo` for
copying data between streams whenever such copies don't require
customized buffer sizes or data flushing policies. Additionally, the
size of the data buffer used by Camel when performing these copies has
increased from 4096 bytes to 16384 bytes (the default buffer size used
by Java 21).
:::

::: paragraph
The tracer (`BacklogTracer`) has changed the `backlogSize` default value
from `1000` to `100`, and `maxBodySize` from `128kb` to `32kb`. This
reduces the amount of data captured and stored and helps reduce the
tracing overhead.
:::

::: paragraph
The `org.apache.camel.support.DefaultExchangeHolder` will now include
both exchange variables and properties, if the parameter
`includeProperties` is set to true.
:::
:::::::

::::: sect2
### camel-jbang {#_camel_jbang}

::: paragraph
The `camel trace` command has changed to show tracing status (by
default). To dump traced messages use `camel trace --action=dump`.
:::

::: paragraph
**Breaking changes only in 4.8.0** The parameter `--repos` has been
renamed `--repository` for `run` and `export` commands. It requires to
be updated when using from command-line and in `application.properties`.
In 4.8.1, the `--repos` has been set back and `--repository` removed.
:::
:::::

::::: sect2
### Deprecated Components {#_deprecated_components}

::: paragraph
The following components that were marked as deprecated:
:::

::: ulist
- camel-univocity-parsers
:::
:::::

:::: sect2
### camel-as2 {#_camel_as2}

::: paragraph
The header prefixes have been corrected from `CamelAS2.` → `CamelAs2.`
to be consistent with naming convention used by other API-based
components. The documentation uses the correct naming prefix as
`CamelAs2.`.
:::
::::

:::: sect2
### camel-kafka {#_camel_kafka}

::: paragraph
The `KafkaIdempotentRepository` will now continue to sync cache updates
after Camel has been started. You can configure `startupOnly=true` to
only sync the cache once on startup, (however, then the cache is not
synced with other Camel nodes in a cluster).
:::
::::

:::: sect2
### camel-langchain4j-chat {#_camel_langchain4j_chat}

::: paragraph
The chat-with-tools feature was deprecated. Use the new
`camel-langchain4j-tool` component.
:::
::::

:::: sect2
### camel-tests {#_camel_tests}

::: paragraph
Continuing the multi-release tests cleanups, on this one, restricted
methods from the `CamelTestSupport` class have been marked as final and
cannot be extended.
:::
::::

:::: sect2
### Preferred JAX-B implementation: `org.glassfish.jaxb:jaxb-runtime` {#_preferred_jax_b_implementation_org_glassfish_jaxbjaxb_runtime}

::: paragraph
We stopped relying on `com.sun.xml.bind:jaxb-impl` in favor of
`org.glassfish.jaxb:jaxb-runtime`. This change should have no impact on
existing code, because recent versions of the two artifacts bring the
same classes. The main motivation for this change is to allow projects
that still require classes from `javax.xml.bind` package to be able to
depend on pre-3.x versions of `com.sun.xml.bind:jaxb-impl` together with
the recent version of `org.glassfish.jaxb:jaxb-runtime` brought by
Camel.
:::
::::
::::::::::::::::::::::::::
:::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
