::: {#header}
# ConsumerTemplate
:::

:::::::::::::::::::::: {#content}
::::::::: {#preamble}
:::::::: sectionbody
::: paragraph
The `ConsumerTemplate` interface allows you to receive message exchanges
from endpoints in a variety of different ways to make it easy to work
with Camel [Endpoint](endpoint.html) instances from Java code.
:::

::: paragraph
It can be configured with a default endpoint if you just want to receive
lots of messages to the same endpoint; or you can specify an
[Endpoint](endpoint.html) or uri as the first parameter.
:::

::: paragraph
The `receiveBody()` method allows you to receive any object to an
endpoint easily as shown:
:::

:::: listingblock
::: content
``` highlight
ConsumerTemplate template = exchange.getContext().createConsumerTemplate();

// recieve from default endpoint
Object body = template.receiveBody();

// receive from a specific queue
body = template.receiveBody("activemq:MyQueue");
```
:::
::::
::::::::
:::::::::

::::::::::: sect1
## Receive modes {#_receive_modes}

:::::::::: sectionbody
::: paragraph
The consumer template operates in three modes:
:::

::: ulist
- **receive** - Consumes from the endpoint, waiting until there is a
  message (can potentially wait for a long time!).

- **receiveNoWait** - Consumes from endpoint, not waiting for a message
  if none exists.

- **receiveTimeout** - Consumes from the endpoint, waiting until there
  is a response, or the timeout occurs.
:::

::: paragraph
In the previous examples then it was the first mode we used
(`receiveBody`). For example if there are no messages on the
`activemq:MyQueue` then Camel would wait until a message is sent to this
queue.
:::

::: paragraph
Often you dont want to wait *forever* so its often a good idea to use a
timeout value, such as 5 seconds:
:::

:::: listingblock
::: content
``` highlight
ConsumerTemplate template = exchange.getContext().createConsumerTemplate();

// receive from a specific queue
body = template.receiveBody("activemq:MyQueue", 5000);
```
:::
::::

::: paragraph
Here we wait at most 5 seconds for a message to be consumed, if there
was no message, then `null` is returned as response.
:::
::::::::::
:::::::::::

::::: sect1
## See Also {#_see_also}

:::: sectionbody
::: paragraph
See [ProducerTemplate](producertemplate.html)
:::
::::
:::::
::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
