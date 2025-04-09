::: {#header}
# Exchange Pattern
:::

:::::::::: {#content}
::: paragraph
There are two *Message Exchange Patterns* you can use in messaging.
:::

::: paragraph
From there [Enterprise Integration
Patterns](components:eips:enterprise-integration-patterns.html) they
are:
:::

::: ulist
- [Event Message](components:eips:event-message.html) (or one-way)

- [Request Reply](components:eips:requestReply-eip.html)
:::

::: paragraph
In Camel we have an `org.apache.camel.ExchangePattern` enumeration which
can be configured on the **exchangePattern** property on the Message
Exchange indicating if a message exchange is a one way [Event
Message](components:eips:event-message.html) (**InOnly**) or a [Request
Reply](components:eips:requestReply-eip.html) message exchange
(**InOut**).
:::

::: paragraph
For example to override the default pattern on a
[JMS](components::jms-component.html) endpoint you could use the
`exchangePattern` parameter in the Endpoint [URI](uris.html) as shown:
:::

:::: listingblock
::: content
``` highlight
jms:myQueue?exchangePattern=InOut
```
:::
::::
::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
