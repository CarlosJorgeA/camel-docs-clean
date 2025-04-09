::: {#header}
# DefaultErrorHandler
:::

::::::::::::::::::: {#content}
::::::::: {#preamble}
:::::::: sectionbody
::: paragraph
This is the default [Error Handler](error-handler.html) in Camel.
:::

::: paragraph
The default [Error Handler](error-handler.html) has the same power as
the [Dead Letter Channel.](components:eips:dead-letter-channel.html)
However, it does **not** support a *dead letter queue*, which is the
only difference between the two of them.
:::

::: paragraph
The `DefaultErrorHandler` is configured differently from [Dead Letter
Channel](components:eips:dead-letter-channel.html) as it is configured
to:
:::

::: ulist
- not redeliver

- not handled

- no dead letter queue (because it is not possible)
:::

::: paragraph
By default, any exception thrown during routing will be propagated back
to the caller and the [Exchange](exchange.html) ends immediately.
However, you can use the [Exception Clause](exception-clause.html) to
catch a given exception and lower the exception by marking it as
handled. If so, the exception will **not** be sent back to the caller,
and the [Exchange](exchange.html) will succeed, but **not continue**
being routed. See the *difference* between `handled` and `continued` in
the [Exception Clause](exception-clause.html) documentation.
:::
::::::::
:::::::::

::::::::::: sect1
## Example {#_example}

:::::::::: sectionbody
::: paragraph
In this route below, any exception thrown in, such as the
`validateOrder` bean, will be propagated back to the caller via the
jetty endpoint, which then returns an HTTP error message back to the
client.
:::

:::: listingblock
::: content
``` highlight
from("jetty:http://localhost/myservice/order")
  .to("bean:validateOrder")
  .to("jms:queue:order");
```
:::
::::

::: paragraph
We can add an `onException` in case we want to catch certain exceptions
and route them differently, for instance to catch a
`ValidationException` and return a fixed response to the caller.
:::

:::: listingblock
::: content
``` highlight
onException(ValidationException.class)
  .handled(true)
  .transform(body(constant("INVALID ORDER")));

from("jetty:http://localhost/myservice/order")
  .to("bean:validateOrder")
  .to("jms:queue:order");
```
:::
::::

::: paragraph
When the `ValidationException` is thrown from the `validateOrder` bean,
it is intercepted by Camel error handler which lets the
`onException(ValidationException.class)` handle the exception. The
[Exchange](exchange.html) is routed to this onException route, and since
we use `handled(true)`, then the original exception is cleared, and we
transform the message into a fixed response that is returned to jetty
endpoint that returns it to the original caller.
:::
::::::::::
:::::::::::
:::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
