::: {#header}
# Apache Camel 3.x Upgrade Guide
:::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
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

:::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Upgrading Camel 3.12 to 3.13 {#_upgrading_camel_3_12_to_3_13}

::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::::::: sect2
### API changes {#_api_changes}

::: paragraph
Added method `getStartedEventNotifiers` to
`org.apache.camel.spi.ManagementStrategy`.
:::

::: paragraph
Added method `scheduleQueue` to `org.apache.camel.spi.ReactiveExecutor`.
:::

::: paragraph
The `org.apache.camel.support.ObjectHelper#createIterable` now supports
`java.util.Map` returning an `Iterable<Map.Entry>` iterating each row in
the map.
:::

::: paragraph
The `org.apache.camel.spi.RestApiProcessorFactory` has removed the
following two parameters
(`String contextIdPattern, boolean contextIdListing`) from the
`createApiProcessor` method.
:::
:::::::

:::: sect2
### Split EIP {#_split_eip}

::: paragraph
The Split EIP now supports splitting message bodies that are of type
`java.util.Map`, which now splits that into a `Set<Map.Entry>` - eg a
set of each map entry as key/value pairs.
:::
::::

:::::::::: sect2
### SimpleBuilder {#_simplebuilder}

::: paragraph
The `org.apache.camel.builder.SimpleBuilder` has been deprecated. This
builder was mostly used internally in Camel with the Java DSL in some
situations.
:::

::: paragraph
End users that may use `SimpleBuilder` in Camel Java DSL can migrate
from:
:::

:::: listingblock
::: content
``` highlight
simple("1").resultType(Integer.class))
```
:::
::::

::: paragraph
to
:::

:::: listingblock
::: content
``` highlight
simple("1", Integer.class)
```
:::
::::
::::::::::

:::: sect2
### CamelContextInitializing and CamelContextInitialized events {#_camelcontextinitializing_and_camelcontextinitialized_events}

::: paragraph
These two events are fired during bootstrap of `CamelContext` even if a
custom `EventNotifier` may not have been fully started. This is due to
that these events happen very early in the bootstrap process, but are of
importance for event notifiers to be able to react upon.
:::
::::

:::: sect2
### Using custom Debugger {#_using_custom_debugger}

::: paragraph
Previously when setting a custom `org.apache.camel.spi.Debugger`, then
Camel would automatically enable debug mode. Now debugging must be
enabled by setting `setDebugging(true)` on `CamelContext`.
:::
::::

:::: sect2
### Using Transactions {#_using_transactions}

::: paragraph
The routing engine has been changed to route exchanges in a different
order when using transactions (`.transacted()`). When an `Exchange` is
continued routed a task is scheduled to the `ReactiveExecutor`. This
fixed issues with could lead to `StackOverflowException`.
:::
::::

:::: sect2
### camel-couchdb {#_camel_couchdb}

::: paragraph
The `since` configuration was replaced by the `resumeStrategy`.
Integrations can now manage the last update sequence for tracking the
changes directly. Check the samples section on the component
documentation page for a sample.
:::
::::

::::: sect2
### camel-file {#_camel_file}

::: paragraph
In order to reduce memory pressure, the component was modified to avoid
using Lists for certain file operations (CAMEL-17059). This affected the
APIs of some the APIs and interfaces provided by the component.
:::

::: ulist
- `GenericFileOperations`: the return type for the methods `listFiles`
  and `listFiles(String)` was modified to return an array of (generic)
  objects instead of a List

- `GenericFileConsumer`: the signature of the methods `isValidFile` and
  `isMatched` was modified to receive an array of files.
:::
:::::

:::: sect2
### camel-ftp {#_camel_ftp}

::: paragraph
The `FtpConsumer`, `FtpOperations`, `SftConsumer` and `SftpOperations`
classes were modified to comply with the API changes on the `camel-file`
component.
:::
::::

::::::: sect2
### camel-hazelcast-starter {#_camel_hazelcast_starter}

::: paragraph
The `camel-hazelcast-starter` for Spring Boot no longer has *customer*
auto configuration options for all its components
:::

::: paragraph
eg the following configuration keys have been removed
:::

::: ulist
- `…​customizer.hazelcast-instance.enabled`

- `…​customizer.hazelcast-instance.override`
:::

::: paragraph
Any Camel component can be customized by using the
`org.apache.camel.spi.ComponentCustomizer` SPI.
:::
:::::::

:::: sect2
### camel-jsch {#_camel_jsch}

::: paragraph
The `ScpOperations` class were modified to comply with the API changes
on the `camel-file` component.
:::
::::

:::: sect2
### camel-kafka {#_camel_kafka}

::: paragraph
The `KafkaManualCommit` class were modified in order to support async
manual commit. Please use the new function `commit()` instead of the old
deprecated one `commitSync()`.
:::
::::

:::: sect2
### camel-mllp {#_camel_mllp}

::: paragraph
This component has been refactored to be similar to other Camel
components. The old component had an unusual static configuration of the
`MllpComponent` which now is refactored to be Camel standard with
regular getter/setters. The component is now also configured *reflection
free*. We also cleaned up how the component dealt with which charset to
use when processing HL7 messages. Users using `camel-mllp` is
recommended to test their applications when upgrading to ensure this
continues to work.
:::
::::

:::::: sect2
### camel-openapi-java / camel-swagger-java {#_camel_openapi_java_camel_swagger_java}

::: paragraph
When using Rest DSL and the `restConfiguration` has a `contextPath`
configured, then this value is used as leading base path for all API
services that are in the generated api-docs.
:::

::: paragraph
This is i.e. needed when using `camel-servlet` which runs under a
specific `context-path`.
:::

::: paragraph
Support for rendering api docs by discovering other CamelContext via JMX
in the same JVM has been removed. Rendering of api docs is now only
supported for the same CamelContext.
:::
::::::

:::: sect2
### camel-quartz {#_camel_quartz}

::: paragraph
The `fireNow` parameter was removed because have no effect. The
`triggerStartDelay` parameter supports negative value to shift trigger
start time in the past. It allows to fire the trigger immediately by
configured `MISFIRE_INSTRUCTION_FIRE_ONCE_NOW`.
:::
::::

:::: sect2
### camel-aws2-sqs {#_camel_aws2_sqs}

::: paragraph
Since the maximum number of attributes per SQS message is 10, we are
limiting the number of headers to be converted to attributes to 10.
:::
::::

::::: sect2
### When using muteException {#_when_using_muteexception}

::: paragraph
HTTP based components that are using `muteException=true` will return an
empty message body, instead of the text `Exception`.
:::

::: paragraph
This applies to camel-jetty, camel-undertow, camel-servlet,
camel-netty-http
:::
:::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
