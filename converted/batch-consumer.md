::: {#header}
# Batch Consumer
:::

:::::::::::::::: {#content}
:::::::: {#preamble}
::::::: sectionbody
::: paragraph
Batch Consumer is basically a [Polling
Consumer](components:eips:polling-consumer.html) that is capable of
polling multiple Exchanges in a single pool.
:::

::: paragraph
To support batching the consumer must implement the
`org.apache.camel.BatchConsumer` interface.
:::

::: paragraph
A range of Camel components support batching such as:
:::

::: ulist
- [AWS2 DDB](components::aws2-ddb-component.html)

- [AWS2 Kinesis](components::aws2-kinesis-component.html)

- [AWS2 S3](components::aws2-s3-component.html)

- [AWS2 SQS](components::aws2-sqs-component.html)

- [File](components::file-component.html)

- [FTP](components::ftp-component.html)

- [IronMQ](components::ironmq-component.html)

- [Jooq](components::jooq-component.html)

- [JPA](components::jpa-component.html)

- [Mail](components::mail-component.html)

- [Minio](components::minio-component.html)

- [MyBatis](components::mybatis-component.html)

- [Slack](components::slack-component.html)

- [Splunk](components::splunk-component.html)

- [SQL](components::sql-component.html)
:::
:::::::
::::::::

:::::: sect1
## Options {#_options}

::::: sectionbody
::: paragraph
The `BatchConsumer` supports the following options:
:::

+-------------+--------------------------------------------------------+
| Option      | Description                                            |
+=============+========================================================+
| `maxMessa   | An integer to define a maximum messages to gather per  |
| gesPerPoll` | poll. By default, no maximum is set. It can be used to |
|             | set a limit of e.g., 1000 to avoid when starting up    |
|             | the server that there are thousands of files. Set a    |
|             | value of 0 or negative to disable it as unlimited.     |
+-------------+--------------------------------------------------------+

::: paragraph
Very often a `BatchConsumer` is scheduled and is based of the
`ScheduledBatchPollingConsumer` that has many options for configuring
the scheduling. These options are listed with *(scheduler)* as label in
the endpoint options\' in the [Components](components::index.html)
documentation.
:::
:::::
::::::

::::: sect1
## Exchange Properties {#_exchange_properties}

:::: sectionbody
::: paragraph
The following properties are set on the Exchange for each Exchange
polled in the same batch.
:::

+-------------+--------------------------------------------------------+
| Property    | Description                                            |
+=============+========================================================+
| `Came       | The total number of Exchanges that was polled in this  |
| lBatchSize` | batch.                                                 |
+-------------+--------------------------------------------------------+
| `Camel      | The current index of the batch. Starts from 0.         |
| BatchIndex` |                                                        |
+-------------+--------------------------------------------------------+
| `CamelBat   | A boolean indicating the last Exchange in the batch.   |
| chComplete` | Is only `true` for the last entry.                     |
+-------------+--------------------------------------------------------+
::::
:::::
::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
