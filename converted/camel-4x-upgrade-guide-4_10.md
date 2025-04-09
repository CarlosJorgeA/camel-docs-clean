::: {#header}
# Apache Camel 4.x Upgrade Guide
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
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

:::::::::::::::: sect1
## Upgrading from 4.10.1 to 4.10.2 {#_upgrading_from_4_10_1_to_4_10_2}

::::::::::::::: sectionbody
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
:::::::::::::::
::::::::::::::::

:::::::::::::::::: sect1
## Upgrading from 4.10.0 to 4.10.1 {#_upgrading_from_4_10_0_to_4_10_1}

::::::::::::::::: sectionbody
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

:::: sect2
### camel-ftp {#_camel_ftp}

::: paragraph
The file name header `Exchange.FILE_NAME` now includes the relative path
such as `subdir/hello.txt` when using `recursive=true`, to be similar to
how `camel-file` component behaves.
:::
::::

:::: sect2
### camel-kafka {#_camel_kafka}

::: paragraph
The camel-kafka option `recordMetadata` has changed default from `true`
to `false`.
:::
::::

:::: sect2
### camel-jbang {#_camel_jbang}

::: paragraph
The option `lazy-bean` has changed to be default `true` when exporting
to make the export work in more situations out of the box.
:::
::::
:::::::::::::::::
::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Upgrading Camel 4.9 to 4.10 {#_upgrading_camel_4_9_to_4_10}

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
:::::::::::::::::::::::: sect2
### XML DSL changes {#_xml_dsl_changes}

::: paragraph
In `<intercept`\> and `<interceptSendToEndpoint>` then `<when>` has been
renamed to `<onWhen>`.
:::

::: paragraph
For example:
:::

:::: listingblock
::: content
``` highlight
<intercept>
  <when>...</when>
  ...
</intercept>
```
:::
::::

::: paragraph
Should now be:
:::

:::: listingblock
::: content
``` highlight
<intercept>
  <onWhen>...</onWhen>
  ...
</intercept>
```
:::
::::

::: paragraph
In `<circuitBreaker` the `<onFallback>` section must be configured last.
:::

::: paragraph
For example:
:::

:::: listingblock
::: content
``` highlight
  <circuitBreaker>
    <resilience4jConfiguration timeoutEnabled="true" timeoutDuration="2000"/>
    <onFallback>
      <transform>
        <constant>Fallback message</constant>
      </transform>
    </onFallback>
    <to uri="direct:foo"/>
    ...
  </circuitBreaker>
```
:::
::::

::: paragraph
Should now be:
:::

:::: listingblock
::: content
``` highlight
  <circuitBreaker>
    <resilience4jConfiguration timeoutEnabled="true" timeoutDuration="2000"/>
    <to uri="direct:foo"/>
    ...
    <onFallback>
      <transform>
        <constant>Fallback message</constant>
      </transform>
    </onFallback>
  </circuitBreaker>
```
:::
::::

::: paragraph
And `inheritErrorHandler` has been moved from `<loadBalance>` to
`<failoverLoadBalancer` which is the only load balancer support this
option.
:::

::: paragraph
For example:
:::

:::: listingblock
::: content
``` highlight
  <loadBalance inheritErrorHandler="true">
    <failoverLoadBalancer maximumFailoverAttempts="3" roundRobin="true"/>
    ...
  </loadBalance>
```
:::
::::

::: paragraph
Should now be:
:::

:::: listingblock
::: content
``` highlight
  <loadBalance>
    <failoverLoadBalancer maximumFailoverAttempts="3" roundRobin="true" inheritErrorHandler="true"/>
    ...
  </loadBalance>
```
:::
::::
::::::::::::::::::::::::

::::::::::::::: sect2
### camel-kamelet {#_camel_kamelet}

::: paragraph
The error handling when using kamelets has been refactored to let
Kamelets re-use the same error handling that are from the route where
the kamelets are being used. Previously Kamelets did not have any error
handling.
:::

::: paragraph
Suppose you have kamelets that would cause an exception during
processing, such as the source below. Now because the route has been
configured with a *dead letter channel* as the error handler, then the
exception from the kamelet will be handled by the route error handler.
Which means you will se a WARN being logged.
:::

::: paragraph
Previously the exception would **not** be handled by the route error
handler, and the kamelet source would always fail internally and cause a
WARN being logged. Meaning that you did not have any power to handle
these errors.
:::

::: paragraph
Now the kamelets are *first class* and gives users the full power to
handle errors as they see fit.
:::

:::: listingblock
::: content
``` highlight
- route:
    errorHandler:
      deadLetterChannel:
        deadLetterUri: log:dead?level=WARN
    id: myRoute
    from:
      uri: "kamelet:my-error-source/source"
      steps:
        - log: "${body}"
```
:::
::::

::: paragraph
This change has most an effect on source Kamelets. For sink or action
Kamelets, then any error would be propagated back to the route, that
could still handle the error. However, if the error handler is
configured to perform retries, then the retry would be starting all over
again calling the sink Kamelet. This change will let the error handler
perform retries at the original of the error (also inside the Kamelet),
the same as regular Camel routes.
:::

::: paragraph
So suppose you have the following route:
:::

:::: listingblock
::: content
``` highlight
- route:
    errorHandler:
      deadLetterChannel:
        deadLetterUri: log:dead?level=WARN
        redeliveryPolicy:
          maximumRedeliveries: 5
          redeliveryDelay: "5000"
    id: myRoute
    from:
      uri: "direct:start"
      steps:
        - to:
            uri: "kamelet:my-error-sink/sink"
        - log: "${body}"
```
:::
::::

::: paragraph
Then notice the error handler has been configured to do redeliveries up
till 5 times with 5 sec delay between. Suppose the sink kamelet is
throwing an exception, then Camel will now perform the redelivery
attempt at the point of origin, which means inside the Kamelet.
Previously the redelivery will only happen at the route level, calling
the kamelet all over again.
:::

::: paragraph
The option `noErrorHandler` has changed default from `true` to `false`.
You should only use this option if you want to turn on error handling
inside Kamelets all together. However, this should only be used in
advanced/rare use-cases. This option may in the future be deprecated and
removed.
:::
:::::::::::::::

:::: sect2
### camel-azure-files {#_camel_azure_files}

::: paragraph
The class `org.apache.camel.component.file.azure.FilesHeaders` has been
renamed to `org.apache.camel.component.file.azure.FilesConstants`.
:::
::::

:::: sect2
### camel-aws2-s3 {#_camel_aws2_s3}

::: paragraph
The header `CamelAwsS3BucketName` for setting a bucket to write to, on
the producer side, cannot be used anymore: the header
`CamelAwsS3OverrideBucketName` must be used instead. This was done to
avoid situation in which you're moving a file from a bucket to a
different one, and the header coming from the S3 consumer is used as
bucket name for S3 Producer. You can find more information on
CAMEL-21680.
:::
::::

:::::: sect2
### camel-file {#_camel_file}

::: paragraph
The `camel-file` consumer has been optimized when filtering file names
using name matching only, to avoid creating an `GenericFile` object that
represent the file. This is unnessasary if the file is to be excluded
due the filtering.
:::

::: paragraph
This optimization has changed APIs in the `camel-file` component to let
methods that accept `GenericFile` as parameter, has been changed to use
a `Supplier<GenericFile>` to lazy create the wrapper.
:::

::: paragraph
Camel users who have created 3rd party component extending `camel-file`
may need to migrate your components.
:::
::::::

:::: sect2
### camel-google-storage {#_camel_google_storage}

::: paragraph
The header `CamelGoogleCloudStorageBucketName` for setting a bucket to
write to, on the producer side, cannot be used anymore: the header
`CamelGoogleCloudStorageOverrideBucketName` must be used instead. This
was done to avoid situation in which you're moving a file from a bucket
to a different one, and the header coming from the Google Storage
consumer is used as bucket name for Google Storage Producer. You can
find more information on CAMEL-21682.
:::
::::

::::: sect2
### camel-jgroups {#_camel_jgroups}

::: paragraph
The cluster lock has been removed as it has been removed in JGroups 5.4
onwards, and it was not recommended to be used in older JGroups
releases. You can use another Camel component such as `camel-infinispan`
that has cluster locking.
:::

::: paragraph
The `camel-jgroups-cluster-service-starter` in Camel Spring Boot has
been removed.
:::
:::::

:::::: sect2
### camel-jbang {#_camel_jbang_2}

::: paragraph
The camel-jbang commands for `camel-k` has been removed.
:::

::: paragraph
The `camel dependency update` has removed the option `--source` to
specify the source file, but to refer to the source file directly such
as:
:::

::: paragraph
`camel dependency update --source=MyRoute.java` to be
`camel dependency update MyRoute.java`.
:::
::::::

:::: sect2
### camel-micrometer {#_camel_micrometer}

::: paragraph
We have fixed a flawed behavior when using dynamic endpoints which made
the generation of endpoint events to grow in an uncontrolled way. From
now on the component will generate events for the endpoint base URI as a
default behavior. If you still want to collect events for the extended
URI (including the parameters), then, you can use the
`camel.metrics.baseEndpointURIExchangeEventNotifier=false`
configuration. Mind that this is strongly discouraged as it can make
your number of events growing out of control.
:::
::::

:::: sect2
### camel-mina {#_camel_mina}

::: paragraph
If using object codec, then you should configure the
`objectCodecPattern` configuration to specify which java classes (FQN)
to allow for Object serialization. You can use `*` to accept all
patterns.
:::
::::

:::: sect2
### camel-minio {#_camel_minio}

::: paragraph
The header `CamelMinioBucketName` for setting a bucket to write to, on
the producer side, cannot be used anymore: the header
`CamelMinioOverrideBucketName` must be used instead. This was done to
avoid situation in which you're moving a file from a bucket to a
different one, and the header coming from the Minio consumer is used as
bucket name for Minio Producer. You can find more information on
CAMEL-21678.
:::
::::

::::: sect2
### camel-google-pubsub-lite {#_camel_google_pubsub_lite}

::: paragraph
The component `camel-google-pubsub-lite` has been deprecated following
the deprecation of the corresponding service by Google Cloud Platform.
:::

::: paragraph
Google recommends migrating your Pub/Sub Lite service to either Google
Cloud Managed Service for Apache Kafka or Google Cloud Pub/Sub.
Depending on your choice, you should use `camel-kafka` or
`camel-google-pubsub component`, respectively.
:::
:::::

:::: sect2
### camel-tracing {#_camel_tracing}

::: paragraph
We have deprecated the setting of MDC `trace_id` and `span_id` in favour
of implementation specific feature. You need to check the specific
tracing/telemetry component configuration to learn how to switch from
the deprecated configuration to the new one. Most of the time you will
need to remove the `camel.main.use-mdc-logging` Camel property (or set
it to `false`) and add dependencies and configuration settings to enable
the specific component instrumentation.
:::
::::

:::: sect2
### camel-langchain4j-chat {#_camel_langchain4j_chat}

::: paragraph
The function calling feature was removed. Please use the
`camel-langchain4j-tools` component for function calling.
:::
::::

:::: sect2
### camel-smb {#_camel_smb}

::: paragraph
The `camel-smb` component has been updated to extend `GenericFile`
classes and now supports more consumer and producer options. The
Consumer includes options for filtering, pre and post processing,
duplicate handling, directory traversal, polling, and readlocks. The
Producer includes options for writing to temporary files, writing
content, and handling existing files.
:::
::::

::::: sect2
### camel-solr {#_camel_solr}

::: paragraph
The `camel-solr` component has been refactored. The `solrs` and
`solrCloud` schemes have been deprecated in the uri format (but can
still be enabled via the enableSSL and solrClient configuration
options). The solr operations have been simplified and some solr
operations will be no longer be available in the next release. For those
operations, a warning message will tell you how to get the same results
with the new operations. The Solr component exchange headers have been
renamed and extended. As a consequence, the user should review the use
of the Solr exchange headers and rename them when applicable.
:::

::: paragraph
All the solr headers has been renamed to use `CamelSolr` as prefix, such
as `operation` → `CamelSolrOperation`.
:::
:::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

::::: sect1
## camel-spring-boot {#_camel_spring_boot}

:::: sectionbody
::: paragraph
The `camel-k-starter` has been removed.
:::
::::
:::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
