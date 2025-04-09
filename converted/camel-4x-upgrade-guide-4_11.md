::: {#header}
# Apache Camel 4.x Upgrade Guide
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
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

::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Upgrading Camel 4.10 to 4.11 {#_upgrading_camel_4_10_to_4_11}

:::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
:::::::: sect2
### EIPs {#_eips}

::::: sect3
#### Recipient List, Split and Multicast EIP {#_recipient_list_split_and_multicast_eip}

::: paragraph
In parallel processing mode, you can also enable `synchronous=true` to
force these EIPs to process the sub-tasks using the upper bounds of the
thread-pool. If using `synchronous=false` then Camel will allow its
reactive routing engine to use as many threads as possible, which may be
available due to sub-tasks using other thread-pools such as
`CompletableFuture.runAsync` or others.
:::

::: paragraph
Setting `synchronous=true` is the same behaviour is in Camel 2 which did
not have the reactive routing engine.
:::
:::::

:::: sect3
#### WireTap and OnCompletion EIP {#_wiretap_and_oncompletion_eip}

::: paragraph
When MDC is enabled, then the WireTap and OnCompletion (in parallel
mode) will now propagate MDC context when creating threads to process
the exchanges. This makes these EIPs similar to how other EIPs such as
Multicast EIP already does this.
:::
::::
::::::::

::::: sect2
### camel-api {#_camel_api}

::: paragraph
Added `removeTraits` method to `org.apache.camel.Message`.
:::

::: paragraph
Added `bind` method that accepts `Supplier` for the bean and also
accepts init and destroy methods, to the `org.apache.camel.spi.Registry`
interface.
:::
:::::

::::::: sect2
### camel-attachments {#_camel_attachments}

::: paragraph
The attachments have been refactored to be stored internally as a
*message trait*, and the `org.apache.camel.attachment.AttachmentMessage`
is only a facade to provide end user access to the fine-grained
Attachment APIs. The underlying message implementation such as
`DefaultMessage` in the `Exchange` is un-affected when converting from
`Message` to `AttachmentMessage` via:
:::

:::: listingblock
::: content
``` highlight
AttachmentMessage am = exchange.getMessage(AttachmentMessage.class);
am.addAttachment("message1.xml", new DataHandler(new FileDataSource(new File("src/test/data/message1.xml"))));
```
:::
::::

::: paragraph
The class `org.apache.camel.attachment.AttachmentMap` has been removed.
Removed `getDelegateMessage` method from
`org.apache.camel.attachment.AttachmentMessage`.
:::
:::::::

:::::: sect2
### camel-bean {#_camel_bean}

::: paragraph
The header `Exchange.BEAN_METHOD_NAME` with constant value
`CamelBeanMethodName` has been deprecated, and support for using this
header has been removed. Instead, you can specify the `method` option
directly as shown, or using any other header of your choosing as
follows.
:::

:::: listingblock
::: content
``` highlight
    toD("bean:myBean?method=${header.myMethodName}");
```
:::
::::
::::::

:::: sect2
### camel-main {#_camel_main}

::: paragraph
Remove the deprecated `camel.main.lightweight` option that was not in
use.
:::
::::

:::::: sect2
### file based components {#_file_based_components}

::: paragraph
The file based component such as `camel-file`, `camel-ftp`, `camel-smb`,
and `camel-azure-files` has been improved to allow optimized dynamic
poll when using dynamic `fileName` header.
:::

::: paragraph
This change means that there is a new `DynamicPollingConsumer` API the
consumer implements. And as such some APIs inside these components has
been changed.
:::

::: paragraph
This will only affect if you have built your own custom Camel component
on top of `camel-file`. And if so, your custom code may need to be
changed slightly as well.
:::
::::::

:::: sect2
### camel-ftp {#_camel_ftp}

::: paragraph
The file name header `Exchange.FILE_NAME` now includes the relative path
such as `subdir/hello.txt` when using `recursive=true`, to be similar to
how `camel-file` component behaves.
:::
::::

:::::: sect2
### camel-as2 {#_camel_as2}

::: paragraph
The `camel-as2` component has been made more tooling friendly to
configure options.
:::

::: paragraph
The option `signedReceiptMicAlgorithms` is changed from a `String[]` to
a single `String` using comma to separate algorithm names.
:::

::: paragraph
The option `ediMessageType` is changed from `ContentType` into two
options as `String` fields. This allows to define the content-type and
charset separately and more user-friendly.
:::
::::::

:::: sect2
### camel-kafka {#_camel_kafka}

::: paragraph
The camel-kafka option `recordMetadata` has changed default from `true`
to `false`.
:::
::::

:::::: sect2
### camel-jbang {#_camel_jbang}

::: paragraph
The option `lazy-bean` has changed to be default `true` when exporting
to make the export work in more situations out of the box.
:::

::: paragraph
From this version onward the `export` command add the
`camel-observability-services` dependency (which includes telemetry,
metrics, health and JMX management services out of the box).
:::

::: paragraph
The `--health` and `--metrics` flags of `run` command have been
deprecated in favor of the newly `--observe` flag which add the
`camel-observability-services` dependency (hence including telemetry,
metrics, health and JMX management out of the box). For the run command,
this has to be explicitly enabled (ie, `camel run …​ --observe`).
:::
::::::

:::: sect2
### camel-sql {#_camel_sql}

::: paragraph
When inserting or updating many rows in `batch=true` mode (producer)
then this component has been optimized to execute the entire batch
operation in a single transaction; by turning off auto-commit on the SQL
Connection, and doing a manual `commit` or `rollback`. This can
dramatically improve performance on some databases. The old behaviour
can be restored by setting the `batchAutoCommitDisabled=false` on the
component or endpoint.
:::
::::

:::: sect2
### camel-etcd3 {#_camel_etcd3}

::: paragraph
This deprecated component was removed in this release.
:::
::::

:::: sect2
### camel-platform-http {#_camel_platform_http}

::: paragraph
The
`org.apache.camel.component.platform.http.PlatformHttpHeaderFilterStrategy`
class has been removed. Use the
`org.apache.camel.http.base.HttpHeaderFilterStrategy`
:::
::::

:::: sect2
### camel-undertow {#_camel_undertow}

::: paragraph
The `org.apache.camel.component.undertow.UndertowHeaderFilterStrategy`
class has been deprecated. The default header filter strategy is now the
`org.apache.camel.http.base.HttpHeaderFilterStrategy`
:::
::::

::::::: sect2
### camel-metrics {#_camel_metrics}

::: paragraph
A new gauge metric, `app.info` is available by default. This is
providing a few information related to the runtime provider such as:
:::

:::: listingblock
::: content
``` highlight
# HELP app_info
# TYPE app_info gauge
app_info{camel_context="camel-1",camel_runtime_provider="Spring-Boot",camel_runtime_version="3.4.3",camel_version="4.10.2"} 1
```
:::
::::

::: paragraph
This information is evaluated at runtime startup and available through
the usual metrics endpoint. To disable this metric you need to use the
`camel.metrics.skipCamelInfo=true` property.
:::
:::::::

:::: sect2
### camel-observability-services {#_camel_observability_services}

::: paragraph
In this version we're moving the telemetry component from
`camel-opentelemetry` to `camel-opentelemetry2`.
:::
::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
