::: {#header}
# Endpoints
:::

:::::::::::::::::::: {#content}
:::::: {#preamble}
::::: sectionbody
::: paragraph
Camel supports the [Message
Endpoint](components:eips:message-endpoint.html) pattern using the
[Endpoint](https://www.javadoc.io/doc/org.apache.camel/camel-api/current/org/apache/camel/Endpoint.html)
interface.
:::

::: paragraph
Endpoints are created by a [Component](component.html) and these
endpoints are referred to in the [DSL](dsl.html) via their endpoint
[URIs](uris.html).
:::
:::::
::::::

:::::::::: sect1
## Example {#_example}

::::::::: sectionbody
::: paragraph
The following example route demonstrates the use of a
[File](components::file-component.html) consumer endpoint and a \*
[JMS](components::jms-component.html) producer endpoint, by their
[URIs](manual::uris.html):
:::

:::: listingblock
::: content
``` highlight
from("file:messages/foo")
    .to("jms:queue:foo");
```
:::
::::

::: paragraph
And in XML:
:::

:::: listingblock
::: content
``` highlight
<route>
    <from uri="file:messages/foo"/>
    <to uri="jms:queue:foo"/>
</route>
```
:::
::::
:::::::::
::::::::::

::::::: sect1
## Endpoint API {#_endpoint_api}

:::::: sectionbody
::: paragraph
You will almost never have the need for creating endpoints manually via
Java API.
:::

::: paragraph
From an `Endpoint` you can use the following Java API methods to create
producers or consumers to the endpoint:
:::

::: ulist
- [`createProducer()`](https://www.javadoc.io/doc/org.apache.camel/camel-api/current/org/apache/camel/Endpoint.html#createProducer--)
  will create a
  [Producer](https://www.javadoc.io/doc/org.apache.camel/camel-api/current/org/apache/camel/Producer.html)
  for sending message exchanges to the endpoint.

- [`createConsumer()`](https://www.javadoc.io/doc/org.apache.camel/camel-api/current/org/apache/camel/Endpoint.html#createConsumer-org.apache.camel.Processor)
  implements the [Event Driven
  Consumer](components:eips:eventDrivenConsumer-eip.html) pattern for
  consuming message exchanges from the endpoint via a
  [Processor](https://www.javadoc.io/doc/org.apache.camel/camel-api/current/org/apache/camel/Processor.html)
  when creating a
  [Consumer](https://www.javadoc.io/doc/org.apache.camel/camel-api/current/org/apache/camel/Consumer.html).

- [`createPollingConsumer()`](https://www.javadoc.io/doc/org.apache.camel/camel-api/current/org/apache/camel/Endpoint.html#createPollingConsumer)
  implements the [Polling
  Consumer](components:eips:polling-consumer.html) pattern for consuming
  message exchanges from the endpoint via a
  [PollingConsumer](https://www.javadoc.io/doc/org.apache.camel/camel-api/current/org/apache/camel/PollingConsumer.html).
:::
::::::
:::::::
::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
