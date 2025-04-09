::: {#header}
# Apache Camel 4.x Upgrade Guide
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
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

::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Upgrading Camel 4.2 to 4.3 {#_upgrading_camel_4_2_to_4_3}

:::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::::: sect2
### camel-core {#_camel_core}

::: paragraph
Moved class `org.apache.camel.impl.engine.MemoryStateRepository` from
camel-base-engine to
`org.apache.camel.support.processor.state.MemoryStateRepository` in
camel-support.
:::

::: paragraph
Moved class `org.apache.camel.impl.engine.FileStateRepository` from
camel-base-engine to
`org.apache.camel.support.processor.state.FileStateRepository` in
camel-support.
:::
:::::

:::::::::: sect2
### Resequence EIP {#_resequence_eip}

::: paragraph
The configuration for batch and stream has been renamed from
`batch-config` to `batchConfig` and `stream-config` to `streamConfig`.
:::

::: paragraph
For example before:
:::

:::: listingblock
::: content
``` highlight
<resequence>
    <stream-config timeout="1000" deliveryAttemptInterval="10"/>
    <simple>${header.seqnum}</simple>
    <to uri="mock:result" />
</resequence>
```
:::
::::

::: paragraph
And now after:
:::

:::: listingblock
::: content
``` highlight
<resequence>
    <streamConfig timeout="1000" deliveryAttemptInterval="10"/>
    <simple>${header.seqnum}</simple>
    <to uri="mock:result" />
</resequence>
```
:::
::::
::::::::::

::::::::::: sect2
### Throttle EIP {#_throttle_eip}

::: paragraph
Throttle now uses the number of concurrent requests as the throttling
measure instead of the number of requests per period.
:::

::: paragraph
Update throttle expressions configured with `maxRequestsPerPeriod` to
use `maxConcurrentRequests` instead, and remove any `timePeriodMillis`
option.
:::

::: paragraph
For example, update the following:
:::

:::: listingblock
::: content
``` highlight
long maxRequestsPerPeriod = 100L;

from("seda:a")
  .throttle(maxRequestsPerPeriod).timePeriodMillis(500)
  .to("seda:b")

// 1000 ms default time period
from("seda:c")
  .throttle(maxRequestsPerPeriod)
  .to("seda:d")
```
:::
::::

::: paragraph
to use `maxConcurrentRequests`:
:::

:::: listingblock
::: content
``` highlight
long maxConcurrentRequests = 30L;

from("seda:a")
  .throttle(maxConcurrentRequests)
  .to("seda:b")

from("seda:c")
  .throttle(maxConcurrentRequests)
  .to("seda:d")
```
:::
::::
:::::::::::

:::::: sect2
### Consumer health checks {#_consumer_health_checks}

::: paragraph
The scheduled consumers have been improved to mark the consumer as
*ready* sooner, when possible. Previously a consumer would mark as ready
after the first poll was completed. For example, an FTP consumer
downloading a big file on the first poll could take so long time that
the readiness check would timeout and fail during the startup of your
Camel application.
:::

::: paragraph
The following components are now marking the consumer as ready sooner:
:::

::: ulist
- camel-aws

- camel-azure

- camel-box

- camel-dhis2

- camel-fhir

- camel-couchbase

- camel-ftp

- camel-google

- camel-ironmq

- camel-jooq

- camel-jpa

- camel-mail

- camel-minio

- camel-mybatis

- camel-olingo2

- camel-olingo4

- camel-slack

- camel-splunk

- camel-sql

- camel-twilio

- camel-zendesk
:::
::::::

:::: sect2
### camel-management {#_camel_management}

::: paragraph
If the `nodeIdPrefix` has been configured on routes, then the MBeans for
the processors will now use the prefix in their `ObjectName` also.
:::
::::

::::: sect2
### camel-console {#_camel_console}

::: paragraph
The context and route consoles have changed some values in their JSON
output data for timestamp for created, completed and failed exchanges.
:::

+-----------------------------------+-----------------------------------+
| **Old Key**                       | **New Key**                       |
+-----------------------------------+-----------------------------------+
| `sinceLastCreatedExchange`        | `lastCreatedExchangeTimestamp`    |
+-----------------------------------+-----------------------------------+
| `sinceLastCompletedExchange`      | `lastCompletedExchangeTimestamp`  |
+-----------------------------------+-----------------------------------+
| `sinceLastFailedExchange`         | `lastFailedExchangeTimestamp`     |
+-----------------------------------+-----------------------------------+

::: paragraph
The values are also changed from String ago to timestamp in millis.For
example, old value `3m5s` is now `1701599263337`.
:::
:::::

::::: sect2
### camel-jbang {#_camel_jbang}

::: paragraph
The `camel transform` command has been renamed to
`camel transform route` as this command is used for transforming routes
between DSLs such as XML to YAML.
:::

::: paragraph
There is a new `camel transform message` command to do message
transformation.
:::
:::::

:::: sect2
### camel-jetty {#_camel_jetty}

::: paragraph
Jetty has been upgraded from v11 to v12. End users may need to adjust to
changes in Jetty.
:::
::::

::::::: sect2
### camel-kafka {#_camel_kafka}

::: paragraph
The behavior for `breakOnFirstError` was altered as numerous issues were
fixed. The behavior related to committing the offset is now determined
by the `CommitManager` that is configured.
:::

::: paragraph
When the default `CommitManager` is used (`NoopCommitManager`) then no
commit is performed. The route implementation will be responsible for
managing the offset using `KafkaManualCommit` to manage the retrying of
the payload.
:::

::: paragraph
When using the `SyncCommitManager` then the offset will be committed so
that the payload is continually retried. This was the behavior described
in the documentation.
:::

::: paragraph
When using the `AsyncCommitManager` then the offset will be committed so
that the payload is continually retried. This was the behavior described
in the documentation.
:::
:::::::

:::::: sect2
### camel-yaml-dsl {#_camel_yaml_dsl}

::: paragraph
Using kebab-case in general has been deprecated, and you will see a WARN
logs. Please migrate to camelCase.
:::

::: paragraph
The language for exchange property now only supports camelCase style,
i.e. `exchange-property` is now `exchangeProperty`.
:::

::: paragraph
The `camelYamlDsl.json` Schema file has removed `inheritErrorHandler`
option for all EIPs where it was not applicable. This option is only
intended for the Load Balancer EIP. This makes the YAML schema in-line
with the XML DSL schema.
:::
::::::

:::: sect2
### camel-hdfs {#_camel_hdfs}

::: paragraph
The HDFS component has been deprecated, and planned to be removed in 4.4
(see CAMEL-20196).
:::
::::

:::: sect2
### camel-kafka {#_camel_kafka_2}

::: paragraph
The header name for the `List<RecordMetadata>` metadata has changed from
`org.apache.kafka.clients.producer.RecordMetadata` to
`kafka.RECORD_META`, and the header constant from `KAFKA_RECORDMETA` to
`KAFKA_RECORD_META`.
:::
::::
::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
