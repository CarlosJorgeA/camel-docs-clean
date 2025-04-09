::: {#header}
# Apache Camel 3.x Upgrade Guide
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
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

:::::: sect1
## Upgrading Camel 3.20.6 to 3.20.7 {#_upgrading_camel_3_20_6_to_3_20_7}

::::: sectionbody
::: paragraph
The option `replyTimeout` in `camel-spring-rabbitmq` has been fixed and
the default value from 5 to 30 seconds (this is the default used by
Spring).
:::

::: paragraph
In `camel-jbang` the *Placeholders substitutes* is changed to use
`#name` instead of `$name` syntax.
:::
:::::
::::::

::::: sect1
## Upgrading Camel 3.20.5 to 3.20.6 {#_upgrading_camel_3_20_5_to_3_20_6}

:::: sectionbody
::: paragraph
No changes
:::
::::
:::::

::::: sect1
## Upgrading Camel 3.20.4 to 3.20.5 {#_upgrading_camel_3_20_4_to_3_20_5}

:::: sectionbody
::: paragraph
In Camel JBang the `-dir` parameter for `init` and `run` goal has been
renamed to require 2 dashes `--dir` like all the other options.
:::
::::
:::::

::::: sect1
## Upgrading Camel 3.20.3 to 3.20.4 {#_upgrading_camel_3_20_3_to_3_20_4}

:::: sectionbody
::: paragraph
No changes
:::
::::
:::::

:::::::: sect1
## Upgrading Camel 3.20.2 to 3.20.3 {#_upgrading_camel_3_20_2_to_3_20_3}

::::::: sectionbody
::: paragraph
The option `backlogTracing=true` now automatic enabled the tracer on
startup. The previous behavior was *surprisingly* that the tracer was
only made available, and had to be manually enabled afterwards. The old
behavior can be archived by setting `backlogTracingStandby=true`.
:::

::: paragraph
The [Backlog Tracer](backlog-tracer.html) has been enhanced and *fixed*
to trace message headers (also streaming types). This means that
previously headers of type `InputStream` was not traced before, but is
now included. This could mean that the header stream is positioned at
the end, and logging the header afterward may appear as the header value
is empty.
:::

:::: sect2
### camel-micrometer-starter {#_camel_micrometer_starter}

::: paragraph
The `uri` tags are now static instead of dynamic (by default), as
potential too many tags generated due to URI with dynamic values. This
can be enabled again by setting `camel.metrics.uriTagDynamic=true`.
:::
::::
:::::::
::::::::

::::: sect1
## Upgrading Camel 3.20.1 to 3.20.2 {#_upgrading_camel_3_20_1_to_3_20_2}

:::: sectionbody
::: paragraph
No changes
:::
::::
:::::

::::::::::::::::::::: sect1
## Upgrading Camel 3.20.0 to 3.20.1 {#_upgrading_camel_3_20_0_to_3_20_1}

:::::::::::::::::::: sectionbody
:::: sect2
### XML DSL {#_xml_dsl}

::: paragraph
A route now have `<routeProperty>` in the top instead of the bottom.
:::
::::

:::::::::: sect2
### YAML DSL {#_yaml_dsl}

::: paragraph
Using *allowable values* in Rest with YAML DSL is fixed to use the
correct name as other DSLs.
:::

::: paragraph
Before:
:::

:::: listingblock
::: content
``` highlight
    value:
    - available
    - pending
    - sold
```
:::
::::

::: paragraph
After:
:::

:::: listingblock
::: content
``` highlight
    allowableValues:
    - available
    - pending
    - sold
```
:::
::::
::::::::::

:::::: sect2
### camel-fhir {#_camel_fhir}

::: paragraph
The following 4 advanced options in fhir data format has been made
available for all DSLs:
:::

::: ulist
- fhirContext

- parserErrorHandler

- parserOptions

- forceResourceId
:::

::: paragraph
Previously they were only possible to configure by manually creating a
bean instance of the model, and configure them via Java code or classic
Spring XML `<bean>` style.
:::
::::::

::::: sect2
### camel-kafka {#_camel_kafka}

::: paragraph
The following options have changed default value as they were mistakenly
out of sync with Apache Kafka. This caused camel-kafka producer to not
enable idempotence out of the box, that otherwise plain Apache Kafka
client would do:
:::

::: ulist
- `enableIdempotence`: changed from `false` to `true`

- `requestRequiredAcks`: changed from `1` to `all`

- `retries`: changed from `0` to *`unset`*
:::
:::::
::::::::::::::::::::
:::::::::::::::::::::

::::::: sect1
## Upgrading Camel 3.19 to 3.20 {#_upgrading_camel_3_19_to_3_20}

:::::: sectionbody
::::: sect2
### camel-api {#_camel_api}

::: paragraph
The `org.apache.camel.support.jsse.SSLContextParameters` is now using
`ResourceLoader` from `CamelContext` to load keystore and other
resources in a standard way. Therefore, the `SSLContextParameters` now
must have been pre-configured with a `CamelContext` otherwise an
exception is thrown. This also changes the default resource type from
file to classpath. If using a file resource for a keystore and the
keystore is not found, or entries/aliases aren't found, try explicitly
specifying the file resource type, .e.g `file:myKeystore.jks`.
:::

::: paragraph
The model class
`org.apache.camel.model.errorhandler.ErrorHandlerRefDefinition` has been
renamed to
`org.apache.camel.model.errorhandler.RefErrorHandlerDefinition`.
:::
:::::
::::::
:::::::

::::::::::::::::::::::::::::::: sect1
## camel-bom {#_camel_bom}

:::::::::::::::::::::::::::::: sectionbody
::: paragraph
The `camel-bom` BOM no longer includes dependencies with
`<type>test-jar</type>` as these are for internal testing of the Apache
Camel project.
:::

::: paragraph
The following Maven Plugins has been fixed to use correct GAV:
:::

::: ulist
- camel-debezium-maven-plugin

- camel-salesforce-maven-plugin

- camel-servicenow-maven-plugin
:::

::: paragraph
from `<groupdId>org.apache.camel</groupdId>` to
`<groupdId>org.apache.camel.maven</groupdId>`.
:::

::: paragraph
Dependencies not intended for end users has been removed, such as all
`-parent` JARs.
:::

::::::::: sect2
### camel-base64 {#_camel_base64}

::: paragraph
The `base64` property placeholder function will now look up the value as
a property key. For example
:::

:::: listingblock
::: content
``` highlight
{{base64:myKey}}
```
:::
::::

::: paragraph
Will now look up myKey as a property placeholder value, which then is
decoded. If you want to decode the value as-is, then use
`base64:decode:` as shown below:
:::

:::: listingblock
::: content
``` highlight
{{base64:decode:Q2FtZWw==}}
```
:::
::::
:::::::::

::::: sect2
### camel-log {#_camel_log}

::: paragraph
The log component now shows cached streams
(`org.apache.camel.StreamCache`) message bodies by default. Camel comes
with stream caching enabled out-of-the-box, and therefore using the log
component you would expect to see the message body. Setting
`showCachedStreams=false` to use old behaviour.
:::

::: paragraph
The log component now does not show MEP by default. You can turn this on
by `showExchangePattern=true`.
:::
:::::

:::: sect2
### camel-jsonpath {#_camel_jsonpath}

::: paragraph
There is a new option `unpackArray` that unpacks a single-element Json
array, matched by a Jsonpath, into an object. This option is disabled by
default (this behaviour was enabled by default in previous Camel
versions). There is a new expression
`jsonpathUnpack(String text, Class<?> resultType)` that makes use of
this new option.
:::
::::

::::: sect2
### camel-yaml-dsl {#_camel_yaml_dsl}

::: paragraph
The `error-handler` has been refactored to be aligned with
`errorHandler` from the `camel-core-model` DSL, meaning that it is now
the same, how error handlers are in other DSLs.
:::

::: ulist
- `none` is now called `no-error-handler`

- `log` has been removed, as you can use `dead-letter-channel` with a
  log endpoint as the `dead-letter-uri`.

- `ref` is now `ref-error-handler`.
:::
:::::

::::: sect2
### camel-mongodb {#_camel_mongodb}

::: paragraph
The Mongodb Driver core has been updated to version 4.8.1
:::

::: paragraph
This fully supports MongoDB 6.1
:::
:::::

:::::: sect2
### camel-google-pubsub {#_camel_google_pubsub}

::: paragraph
The Camel Google Pubsub headers have been renamed, since dotted keys are
not allowed.
:::

::: paragraph
This means all the headers will be \"CamelGooglePubsub\" prefixed
instead of \"CamelGooglePubsub.\"
:::

::: paragraph
For more information, have a look at CAMEL-18773
:::
::::::
::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
