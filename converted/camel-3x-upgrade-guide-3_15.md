::: {#header}
# Apache Camel 3.x Upgrade Guide
:::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
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

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Upgrading Camel 3.14 to 3.15 {#_upgrading_camel_3_14_to_3_15}

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::: {.admonitionblock .important}
+-----------------------------------+-----------------------------------+
| ::: title                         | Java 8 is no longer supported.    |
| Important                         | Camel 3.15 onwards requires Java  |
| :::                               | 11 or 17.                         |
+-----------------------------------+-----------------------------------+
:::

:::: sect2
### Removed components {#_removed_components}

::: paragraph
The `camel-kamelet-reify` and `camel-spring-javaconfig` has been
removed.
:::
::::

:::: sect2
### Removed Maven Archetypes {#_removed_maven_archetypes}

::: paragraph
The `camel-archetype-endpointdsl` has been removed.
:::
::::

:::: sect2
### API changes {#_api_changes}

::: paragraph
Added `getScheme` method to `org.apache.camel.spi.Resource`.
:::
::::

::::::: sect2
### Core Dependency changes {#_core_dependency_changes}

::: paragraph
As part of CAMEL-17429 and CAMEL-17435, the following dependencies are
no longer included by default on all the Camel modules:
:::

::: ulist
- com.sun.xml.messaging.saaj:saaj-impl

- jakarta.xml.bind:jakarta.xml.bind-api

- javax.annotation:javax.annotation-api

- javax.xml.soap:javax.xml.soap-api

- javax.xml.ws:jaxws-api

- org.apache.geronimo.specs:geronimo-jta_1.1_spec

- org.apache.geronimo.specs:geronimo-ws-metadata_2.0_spec

- org.glassfish.jaxb:jaxb-runtime
:::

::: paragraph
These dependencies were included on the specific modules and components
that required them on a per-case basis.
:::

::: paragraph
Applications with dependency on any of those, but using them
transitively, should be modified to include them.
:::
:::::::

::::: sect2
### camel-core {#_camel_core}

::: paragraph
Removed the deprecated `Classic` value from `startupSummaryLevel` enums.
:::

::: paragraph
The deprecated `propertyName` on `removeProperty` EIP has been removed,
use `name` instead.
:::
:::::

::::::::::: sect2
### Data Formats {#_data_formats}

::: paragraph
The data formats naming has been made consistent to use Camel Case:
:::

+-----------------------------------+-----------------------------------+
| Old Name                          | New Name                          |
+===================================+===================================+
| `bindy-csv`                       | `bindyCsv`                        |
+-----------------------------------+-----------------------------------+
| `bindy-fixed`                     | `bindyFixed`                      |
+-----------------------------------+-----------------------------------+
| `bindy-kvp`                       | `bindyKvp`                        |
+-----------------------------------+-----------------------------------+
| `customDataFormat`                | `custom`                          |
+-----------------------------------+-----------------------------------+
| `gzip-deflater`                   | `gzipDeflater`                    |
+-----------------------------------+-----------------------------------+
| `jacksonxml`                      | `jacksonXml`                      |
+-----------------------------------+-----------------------------------+
| `json-fastjson`                   | `fastjson`                        |
+-----------------------------------+-----------------------------------+
| `json-gson`                       | `gson`                            |
+-----------------------------------+-----------------------------------+
| `json-jackson`                    | `jackson`                         |
+-----------------------------------+-----------------------------------+
| `json-johnzon`                    | `johnzon`                         |
+-----------------------------------+-----------------------------------+
| `json-jsonb`                      | `jsonb`                           |
+-----------------------------------+-----------------------------------+
| `json-xstream`                    | `xstreamJson`                     |
+-----------------------------------+-----------------------------------+
| `mime-multipart`                  | `mimeMultipart`                   |
+-----------------------------------+-----------------------------------+
| `protobuf-jackson`                | `protobufJackson`                 |
+-----------------------------------+-----------------------------------+
| `secureXML`                       | `xmlSecurity`                     |
+-----------------------------------+-----------------------------------+
| `soapjaxb`                        | `soap`                            |
+-----------------------------------+-----------------------------------+
| `tarfile`                         | `tarFile`                         |
+-----------------------------------+-----------------------------------+
| `univociy-csv`                    | `univociyCsv`                     |
+-----------------------------------+-----------------------------------+
| `univociy-fixed`                  | `univociyFixed`                   |
+-----------------------------------+-----------------------------------+
| `univociy-tsv`                    | `univociyTsv`                     |
+-----------------------------------+-----------------------------------+
| `yaml-snakeyaml`                  | `snakeYaml`                       |
+-----------------------------------+-----------------------------------+
| `zip-deflater`                    | `zipDeflater`                     |
+-----------------------------------+-----------------------------------+
| `zipfile`                         | `zipFile`                         |
+-----------------------------------+-----------------------------------+

::: paragraph
The Spring Boot auto-configuration names has also been renamed (uses
dash case as this is Spring Boot naming style):
:::

+-----------------------------------+-----------------------------------+
| Old Name                          | New Name                          |
+===================================+===================================+
| `camel.dataformat.fhirjson`       | `camel.dataformat.fhir-json`      |
+-----------------------------------+-----------------------------------+
| `camel.dataformat.fhirxml`        | `camel.dataformat.fhir-xml`       |
+-----------------------------------+-----------------------------------+
| `camel.dataformat.gzipdeflater`   | `camel.dataformat.gzip-deflater`  |
+-----------------------------------+-----------------------------------+
| `camel.dataformat.jacksonxml`     | `camel.dataformat.jackson-xml`    |
+-----------------------------------+-----------------------------------+
| `camel.dataformat.jsonapi`        | `camel.dataformat.json-api`       |
+-----------------------------------+-----------------------------------+
| `camel.dataformat.mimemultipart`  | `camel.dataformat.mime-multipart` |
+-----------------------------------+-----------------------------------+
| `camel.dataformat.securexml`      | `camel.dataformat.xml-security`   |
+-----------------------------------+-----------------------------------+
| `camel.dataformat.tarfile`        | `camel.dataformat.tar-file`       |
+-----------------------------------+-----------------------------------+
| `camel.dataformat.tidymarkup`     | `camel.dataformat.tidy-markup`    |
+-----------------------------------+-----------------------------------+
| `camel.dataformat.univocitycsv`   | `camel.dataformat.univocity-csv`  |
+-----------------------------------+-----------------------------------+
| `camel.dataformat.univocityfixed` | `                                 |
|                                   | camel.dataformat.univocity-fixed` |
+-----------------------------------+-----------------------------------+
| `camel.dataformat.univocitytsv`   | `camel.dataformat.univocity-tsv`  |
+-----------------------------------+-----------------------------------+
| `camel.dataformat.yaml-snakeyaml` | `camel.dataformat.snake-yaml`     |
+-----------------------------------+-----------------------------------+
| `camel.dataformat.zipdeflater`    | `camel.dataformat.zip-deflater`   |
+-----------------------------------+-----------------------------------+
| `camel.dataformat.zipfile`        | `camel.dataformat.zip-file`       |
+-----------------------------------+-----------------------------------+

:::::::: sect3
#### Message History {#_message_history}

::: paragraph
The message history output has changed to include source column, which
refers to the Java class/XML file from the DSL the message was at the
given time. (line precise error reporting).
:::

::: paragraph
This default formatting are (Source, ID, Processor, Elapsed):
:::

:::: listingblock
::: content
``` highlight
MESSAGE_HISTORY_HEADER = "%-40s %-30s %-50s %-12s";
MESSAGE_HISTORY_OUTPUT = "%-40.40s %-30.30s %-50.50s %12.12s";
```
:::
::::

::: paragraph
If you are using custom sizes then you need to adjust accordingly.
:::
::::::::
:::::::::::

::::: sect2
### camel-endpointdsl {#_camel_endpointdsl}

::: paragraph
The Endpoint DSL is now more type-safe, where previously an option would
be declared as `Object` type, is now declared using its actual type,
such as `javax.sql.DataSource`.
:::

::: paragraph
Option that are enums is now using the real enum FQN class name, instead
of a duplicate enum class that was embedded inside the Endpoint DSL. For
example when using `aws2sq` the namingStrategy enum is changed from
`org.apache.camel.builder.endpoint.dsl.AWS2S3EndpointBuilderFactory` to
`org.apache.camel.component.aws2.s3.stream.AWSS3NamingStrategyEnum`
class name.
:::
:::::

::::: sect2
### camel-management {#_camel_management}

::: paragraph
The route controller MBeans are moved from `routecontrollers` to the
existing `services` node in the JMX MBean tree.
:::

::: paragraph
Removed deprecated operations on `CamelContextMBean` and
`CamelRouteMBean`.
:::
:::::

:::: sect2
### camel-util {#_camel_util}

::: paragraph
Deleted the `replaceAll` method of `org.apache.camel.util.StringHelper`.
Please use the `replace` method of `java.lang.String` instead, as it is
much faster from Java 11 onward.
:::
::::

::::::::::: sect2
### camel-yaml-dsl {#_camel_yaml_dsl}

::: paragraph
Removed `steps` from `route` because steps should only be configured on
`from` making the DSL consistent and similar to Java DSL.
:::

::: paragraph
Before it was possible to do:
:::

:::: listingblock
::: content
``` highlight
- route:
    id: demo-route
    from:
      uri: "timer:info"
    steps:
      - log: "message"
```
:::
::::

::: paragraph
This should correctly be done with `steps` as child of `from`:
:::

:::: listingblock
::: content
``` highlight
- route:
    id: demo-route
    from:
      uri: "timer:info"
      steps:
        - log: "message"
```
:::
::::

::: paragraph
The `tod` EIP name has been removed, the correct names to use is `toD`.
:::
:::::::::::

:::: sect2
### camel-jbang {#_camel_jbang}

::: paragraph
The JBang app that was previously named `CamelJBang` is now named
`camel`. It is still possible to use the older name by installing it
using `CamelJBang@apache/camel` but this approach is deprecated and
should not be used in the future.
:::
::::

:::: sect2
### camel-debezium {#_camel_debezium}

::: paragraph
Upgraded to Debezium 1.8 which requires Java 11 and Kafka Client 3.0
JARs.
:::
::::

:::::: sect2
### camel-cdi {#_camel_cdi}

::: paragraph
The legacy XML in `camel-cdi` with `<camelContext>` is deprecated,
instead the XML DSL route loader should be used.
:::

::: paragraph
JTA support is moved out of `camel-cdi` to its own `camel-cdi-jta`
module.
:::

::: paragraph
The `org.apache.camel.cdi.Main` class has moved from `camel-cdi` to
`camel-cdi-main` JAR.
:::
::::::

:::::: sect2
### camel-kafka {#_camel_kafka}

::: paragraph
The following classes were moved from `org.apache.camel.component.kafka`
to `org.apache.camel.component.kafka.consumer`:
:::

::: ulist
- `DefaultKafkaManualAsyncCommit`

- `DefaultKafkaManualAsyncCommitFactory`

- `DefaultKafkaManualSyncCommit`

- `DefaultKafkaManualSyncCommitFactory`

- `KafkaAsyncManualCommit`

- `KafkaManualCommit`

- `KafkaManualCommitFactory`
:::

::: paragraph
The signature for `KafkaManualCommitFactory` and its classes has been
adjusted to abstract some runtime parameters. The old signature is still
available but will be removed in 3.16.0.
:::
::::::

::::: sect2
### camel-karaf {#_camel_karaf}

::: paragraph
The `camel-endpointdsl` and `camel-componentdsl` is no longer supported
on Apache Karaf.
:::

::: paragraph
The `camel-osg-activator`, `camel-google-calendar`,
`camel-google-drive`, `camel-google-mail`, `camel-google-sheets`, and
`camel-jooq` has been removed.
:::
:::::

:::: sect2
### camel-netty / camel-netty-http {#_camel_netty_camel_netty_http}

::: paragraph
The netty producer has migrated from commons-poll v1 to v2. The option
`producerPoolMaxActive` is renamed to `producerPoolMaxTotal`.
:::
::::

:::: sect2
### camel-quartz {#_camel_quartz}

::: paragraph
Removed the option `startDelaySeconds` as this does not work correctly
and causes problems if in use.
:::
::::

:::: sect2
### camel-rabbitmq {#_camel_rabbitmq}

::: paragraph
The rabbitmq producer has migrated from commons-poll v1 to v2.
:::
::::

:::: sect2
### camel-openapi-java {#_camel_openapi_java}

::: paragraph
The contextPath specified in the REST configuration is no longer added
to the paths of the operations in the generated openapi specification.
:::
::::

:::: sect2
### camel-google-drive {#_camel_google_drive}

::: paragraph
The `camel-google-drive` component has upgraded to the latest version of
`google-api-services-drive`. This means that support for access to the
Google Drive Realtime APIs via URIs like `google-drive:drive-realtime`
has been removed. Google deprecated and retired the realtime APIs some
time ago.
:::
::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
