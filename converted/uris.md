::: {#header}
# URIs
:::

::::::::::::::: {#content}
::::::::::: {#preamble}
:::::::::: sectionbody
::: paragraph
Camel makes extensive use of URIs to allow you to refer to
[Endpoints](endpoint.html).
:::

::: paragraph
For example, consider the following URI:
:::

:::: listingblock
::: content
``` highlight
kafka:cheese?brokers=mykafka:1234&clientId=foo
```
:::
::::

::: paragraph
This endpoint is created by the
[Kafka](components::kafka-component.html) component. The URI contains
endpoint configurations as context-path and query parameters. In this
example, the context-path is `cheese` which is the kafka topic to use.
:::

::: paragraph
The query parameters have two parameters:
:::

::: {.olist .arabic}
1.  `brokers=mykafka:1234`: the remote Kafka broker to connect to.

2.  `clientId=foo`: the client id, which is a configuration of the Kafka
    component
:::
::::::::::
:::::::::::

::::: sect1
## More Information {#_more_information}

:::: sectionbody
::: paragraph
You can read the guide [How do I configure
endpoints](faq:how-do-i-configure-endpoints.html) to learn more about
configuring *endpoints*. Among other things, this guide explains how to
refer to beans in the [registry](registry.html), how to use raw values
for password options, how to use [property
placeholders](using-propertyplaceholder.html), or how to use the type
safe [Endpoint DSL](Endpoint-dsl.html) and [DataFormat
DSL](dataformat-dsl.html).
:::
::::
:::::
:::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
