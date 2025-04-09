::: {#header}
# Apache Camel 3.x Upgrade Guide
:::

::::::::::::::::::::::::: {#content}
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

::::: sect1
## Upgrading Camel 3.14.x to 3.14.10 {#_upgrading_camel_3_14_x_to_3_14_10}

:::: sectionbody
::: paragraph
The option `synchronous` in `camel-ftp` has been removed, as this is
always enabled to ensure thread safety for the underlying FTP client.
:::
::::
:::::

:::::::::::::::::: sect1
## Upgrading Camel 3.13 to 3.14 {#_upgrading_camel_3_13_to_3_14}

::::::::::::::::: sectionbody
::::::: sect2
### API changes {#_api_changes}

::: paragraph
Added method `updateRoutesToCamelContext` to
`org.apache.camel.RoutesBuilder` interface.
:::

::: paragraph
Added parameter `Resource` in `createRoute` method on
`org.apache.camel.spi.RouteFactory`.
:::

::: paragraph
Added method `reloadProperties` to
`org.apache.camel.spi.LoadablePropertiesSource`.
:::

::: paragraph
Removed the deprecated `headerName` option from RemoveHeader EIP. Use
`name` instead.
:::
:::::::

::::: sect2
### camel-kafka {#_camel_kafka}

::: paragraph
The APIs in `camel-kafka` component changed from using the Kafka Client
classes `org.apache.kafka.clients.producer.KafkaProducer` and
`org.apache.kafka.clients.consumer.KafkaConsumer` to their interfaces
`org.apache.kafka.clients.producer.Producer` and
`org.apache.kafka.clients.consumer.Consumer` instead.
:::

::: paragraph
The option `KafkaManualCommitFactory` on component is now autowired, if
there exists a single custom factory in the registry.
:::
:::::

:::: sect2
### camel-jbang {#_camel_jbang}

::: paragraph
The option `debug-level` has been renamed to `logging-level` because the
option is for configuring the logging level.
:::
::::

:::: sect2
### camel-zookeeper/camel-master {#_camel_zookeepercamel_master}

::: paragraph
When using Zookeeper with the Master component, the given namespace is
now used to define leadership. In other words, a route defined with
`master:lock1` will result in one leader election, while a route defined
with `master:lock2` will result in a separate leader election, which may
or may not result in the same leader as `lock1`. This matches the
existing behavior of the Master component when using Consul.
:::
::::

:::: sect2
### camel-aws2-ses {#_camel_aws2_ses}

::: paragraph
The to and replyTo parameters are now comma separated String and no more
List of Strings, this is true even for the related headers.
:::
::::
:::::::::::::::::
::::::::::::::::::
:::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
