::: {#header}
# Notify Builder
:::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
::::::: {#preamble}
:::::: sectionbody
::: paragraph
The `NotifyBuilder` is a builder from the `org.apache.camel.builder`
package which allows you to build expressions and then test or wait for
that condition to occur. The expressions are based around notifications
about [Exchange](exchange.html) being routed. So what does that mean?
:::

::: paragraph
It means that you can build an expressions which can tell you when Camel
is finished with routing 5 messages etc. You may want to use this when
testing a route which you cannot or will not use
[Mocks](components::mock-component.html).
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | The `NotifyBuilder` is only       |
| Note                              | intended for testing purposes and |
| :::                               | you can only use this with Java   |
|                                   | coding.                           |
+-----------------------------------+-----------------------------------+
:::
::::::
:::::::

::::::::::::: sect1
## Basic example using NotifyBuilder {#_basic_example_using_notifybuilder}

:::::::::::: sectionbody
::: paragraph
Suppose we have a very simple route:
:::

:::: listingblock
::: content
``` highlight
from("jms:queue:quotes")
    .to("bean:quotes");
```
:::
::::

::: paragraph
Imagine the route being more complex, and a production ready route.
:::

::: paragraph
Now you want to test this route without using mocks or the likes. We
want to test that it could process a message send to that queue. By
using the `NotifyBuilder` we can build an expression which expresses
when that condition occurred.
:::

:::: listingblock
::: content
``` highlight
NotifyBuilder notify = new NotifyBuilder().whenDone(1).create();

// now use some API to send a message etc. Maybe you cannot use Camel's ProducerTemplate
// now we want to wait until the message has been routed and completed

boolean done = notify.matches(10, TimeUnit.SECONDS);
assertTrue("Should be done", done);

// now maybe use some API to see that the message did as expected
```
:::
::::

::: paragraph
This is a very basic example where we configured the `NotifyBuilder` to
match when any `Exchange` is done.
:::

::: paragraph
The builder has many more methods to set more complex expressions, which
even can be stacked using and, or, not operations.
:::
::::::::::::
:::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## NotifyBuilder API {#_notifybuilder_api}

:::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
The `NotifyBuilder` has many methods that allow you to build even more
complex expressions. The table below list the most commonly used
methods.
:::

+-------------+--------------------------------------------------------+
| Method      | Description                                            |
+=============+========================================================+
| `from(en    | Matches only when Exchanges are incoming from that     |
| dpointUri)` | particular endpoint. The endpointUri can be a pattern, |
|             | which is the same pattern matching used by Intercept.  |
+-------------+--------------------------------------------------------+
| `fromRout   | Matches only when Exchanges are incoming from that     |
| e(routeId)` | particular route. The routeId can be a pattern, which  |
|             | is the same pattern matching used by Intercept.        |
+-------------+--------------------------------------------------------+
| `filter(    | Filters out unwanted Exchanges (only messages passing  |
| predicate)` | (true) the predicate is used).                         |
+-------------+--------------------------------------------------------+
| `we         | Matches only when Exchanges has at any point been sent |
| reSentTo(en | to the given endpoint. The endpointUri can be a        |
| dpointUri)` | pattern, which is the same pattern matching used by    |
|             | Intercept.                                             |
+-------------+--------------------------------------------------------+
| `whenDo     | Matches when X number or more messages is done.        |
| ne(number)` |                                                        |
+-------------+--------------------------------------------------------+
| `whenComple | Matches when X number or more messages is complete.    |
| te(number)` |                                                        |
+-------------+--------------------------------------------------------+
| `whenFail   | Matches when X number or more messages is failed.      |
| ed(number)` |                                                        |
+-------------+--------------------------------------------------------+
| `w          | Matches when the message bodies are done in the same   |
| henBodiesDo | order. This method is non strict which means that it   |
| ne(bodies)` | will disregard any additional done messages.           |
+-------------+--------------------------------------------------------+
| `whenAnyD   | Matches if any one of the done messages matched the    |
| oneMatches( | Predicate.                                             |
| predicate)` |                                                        |
+-------------+--------------------------------------------------------+
| `create`    | Creates the notifier. After you have created it you    |
|             | can use the matches methods.                           |
+-------------+--------------------------------------------------------+
| `matches`   | Tests whether the notifier currently matches. This     |
|             | operation returns immediately. This method is to be    |
|             | used after you have created the expression.            |
+-------------+--------------------------------------------------------+
| `matche     | Waits until the notifier matches or times out. Returns |
| s(timeout)` | `true` if matches, or `false` if time-out occurs. This |
|             | operation returns immediately. This method is to be    |
|             | used after you have created the expression.            |
+-------------+--------------------------------------------------------+
| `match      | Wait until the builder matches or timeout. The timeout |
| esWaitTime` | value used is based on the highest result wait time    |
|             | configured on any of mock endpoints being used. If no  |
|             | mock endpoint was used, then the default timeout value |
|             | is 10 seconds. This method is convenient to use in     |
|             | unit tests when you use mocks. Then you don't have to  |
|             | specify the timeout value explicit.                    |
+-------------+--------------------------------------------------------+

::: paragraph
`NotifyBuilder` has more than 30 methods. For full list of methods then
see the [javadoc for
NotifyBuilder](https://www.javadoc.io/doc/org.apache.camel/camel-core-model/current/org/apache/camel/builder/NotifyBuilder.html).
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | You can create multiple instances |
| Tip                               | of `NotifyBuilder` if you want to |
| :::                               | be notified of different          |
|                                   | conditions. `NotifyBuilder` also  |
|                                   | supports using binary operations  |
|                                   | (and, or, not) to stack together  |
|                                   | multiple conditions.              |
+-----------------------------------+-----------------------------------+
:::

:::::: sect2
### Difference between done, completed and failed {#_difference_between_done_completed_and_failed}

::: paragraph
The `NotifyBuilder` identifies three ways a message can complete:
:::

::: ulist
- *Done* - The message is done, regardless of whether it completes or
  fails.

- *Completed* - The message completes with success (no failure).

- *Failed* - The message fails (for example an exception is thrown and
  not handled).
:::

::: paragraph
The names of these three ways are also incorporated in the names of the
builder methods: `whenDone`, `whenCompleted`, and `whenFailed`.
:::
::::::

::::::::::::::::::::::::::::: sect2
### Examples {#_examples}

:::: listingblock
::: content
``` highlight
NotifyBuilder notify = new NotifyBuilder(context)
    .from("direct:foo").whenDone(5)
    .create();
```
:::
::::

::: paragraph
Here we want to match when the direct:foo endpoint have done 5 messages.
:::

::: paragraph
You may also want to be notified when an message is done by the index,
for example the very first message. To do that you can simply do:
:::

:::: listingblock
::: content
``` highlight
NotifyBuilder notify = new NotifyBuilder(context)
    .whenDoneByIndex(0)
    .create();
```
:::
::::

::: paragraph
This ensures that the notifier only matches exactly when the first
message is done.
:::

::: paragraph
If you use `whenDone(1)` instead, then the notifier matches when at
least one message is done. There could be use cases where `whenDone(1)`
would match even if the first message hasn't been done yet, as other
message in between could be done ahead of the first message. That is why
`whenDoneByIndex` was added to support this scenario.
:::

::: paragraph
Here we want to match when the direct:foo endpoint have done 5 messages
which contains the word \'test\' in the body. The filter accepts a
[Predicate](predicate.html).
:::

:::: listingblock
::: content
``` highlight
NotifyBuilder notify = new NotifyBuilder(context)
    .from("direct:foo").filter(body().contains("test")).whenDone(5)
    .create();
```
:::
::::

::: paragraph
Here we just say that at least one message should be done received from
any JMS endpoint (notice the wildcard matching).
:::

:::: listingblock
::: content
``` highlight
NotifyBuilder notify = new NotifyBuilder(context)
    .from("jms:*").whenDone(1)
    .create();
```
:::
::::

::: paragraph
Here, we just say that at least three message should be done received
from any of myCoolRoutes (notice the wildcard matching).
:::

:::: listingblock
::: content
``` highlight
NotifyBuilder notify = new NotifyBuilder(context)
.fromRoute("myCoolRoutes*").whenDone(3)
.create();
```
:::
::::

::: paragraph
Here both 5 foo messages and 7 bar messages must be done. Notice the use
of the and operator.
:::

:::: listingblock
::: content
``` highlight
NotifyBuilder notify = new NotifyBuilder(context)
    .from("direct:foo").whenDone(5)
    .and().from("direct:bar").whenDone(7)
    .create();
```
:::
::::

::: paragraph
Here we expect to receive two messages whose body is Hello World then
Bye World.
:::

:::: listingblock
::: content
``` highlight
NotifyBuilder notify = new NotifyBuilder(context)
    .from("direct:foo").whenBodiesReceived("Hello World", "Bye World")
    .create();
```
:::
::::

::: paragraph
Here we expect to receive a message which contains Camel in the body.
:::

:::: listingblock
::: content
``` highlight
NotifyBuilder notify = new NotifyBuilder(context)
    .whenAnyReceivedMatches(body().contains("Camel"))
    .create();
```
:::
::::
:::::::::::::::::::::::::::::

:::::: sect2
### Using mock endpoint for fine-grained expectations {#_using_mock_endpoint_for_fine_grained_expectations}

:::: listingblock
::: content
``` highlight
// let's use a mock to set the expressions as it got many great assertions for that
// notice we use mock:assert which does NOT exist in the route, it's just a pseudo name
MockEndpoint mock = getMockEndpoint("mock:assert");
mock.expectedBodiesReceivedInAnyOrder("Hello World", "Bye World", "Hi World");

NotifyBuilder notify = new NotifyBuilder(context)
    .from("direct:foo").whenReceivedSatisfied(mock)
    .create();
```
:::
::::

::: paragraph
Here we combine a [Mock](components::mock-component.html) with the
`NotifyBuilder`. We use the mock to set fine-grained expectations such
as we should receive 3 messages in any order. Then using the builder we
can tell that those messages should be received from the direct:foo
endpoint. You can combine multiple expressions as much as you like.
However, we suggest using the mock for fine-grained expectations that
you may already know how to use. You can also specify that the Exchanges
must have been sent to a given endpoint.
:::
::::::

::::::::::::::: sect2
### Using wereSentTo {#_using_weresentto}

::: paragraph
For example in the following we expect the message to be sent to
mock:bar
:::

:::: listingblock
::: content
``` highlight
NotifyBuilder notify = new NotifyBuilder(context)
    .wereSentTo("mock:bar")
    .create();
```
:::
::::

::: paragraph
You can combine this with any of the other expectations, such as, to
only match if 3+ messages are done, and were sent to the mock:bar
endpoint:
:::

:::: listingblock
::: content
``` highlight
NotifyBuilder notify = new NotifyBuilder(context)
    .whenDone(3).wereSentTo("mock:bar")
    .create();
```
:::
::::

::: paragraph
You can add additional `wereSentTo`, such as the following two:
:::

:::: listingblock
::: content
``` highlight
NotifyBuilder notify = new NotifyBuilder(context)
    .wereSentTo("activemq:queue:foo").wereSentTo("activemq:queue:bar")
.create();
```
:::
::::

::: paragraph
As well as you can expect a number of messages to be done, and a message
to fail, which has to be sent to another endpoint:
:::

:::: listingblock
::: content
``` highlight
NotifyBuilder notify = new NotifyBuilder(context)
    .whenDone(3).wereSentTo("activemq:queue:goodOrder")
    .and().whenFailed(1).wereSentTo("activemq:queue:badOrder")
    .create();
```
:::
::::
:::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
