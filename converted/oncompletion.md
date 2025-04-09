::: {#header}
# OnCompletion
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
::::: {#preamble}
:::: sectionbody
::: paragraph
Camel has this concept of a *Unit of Work* that encompass the Exchange.
The unit of work among others supports synchronization callbacks that
are invoked when the Exchange is complete. The callback API is defined
in `org.apache.camel.spi.Synchronization` and the extended
synchronization `org.apache.camel.spi.SynchronizationRouteAware` that
have callbacks for route events.
:::
::::
:::::

::::: sect1
## UnitOfWork API {#_unitofwork_api}

:::: sectionbody
::: paragraph
You can get hold of the `org.apache.camel.spi.UnitOfWork` from
`org.apache.camel.Exchange` with the method `getUnitOfWork()`.
:::
::::
:::::

::::::::::::::::::::::::::::::::::::: sect1
## OnCompletion DSL {#_oncompletion_dsl}

:::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
The OnCompletion EIP supports the following features:
:::

::: ulist
- level: context or route (route level override global level)

- triggered either always, only if completed with success, or only if
  failed

- `onWhen` predicate to only trigger if matched

- `mode` to define whether to run either before or after route consumer
  writes response back to callee (if it is InOut) (default
  AfterConsumer)

- `parallelProcessing` whether to run async or sync (use a thread pool
  or not) (default false)
:::

::: paragraph
The onCompletion supports running the completion task in either
synchronous or asynchronous mode (using a thread pool) and also whether
to run before or after the route consumer is done. The reason is to give
more flexibility. For example to specify to run synchronous and before
the route consumer is done, which allows to modify the exchange before
the consumer writes back any response to the callee. You can use this to
for example add customer headers, or send to a log to log the response
message, etc.
:::

:::: sect2
### onCompletion with route scope {#_oncompletion_with_route_scope}

::: paragraph
The OnCompletion EIP allows you to add custom routes/processors when the
original Exchange is complete. Camel spins off a copy of the Exchange
and routes it in a separate thread, similar to a Wire Tap. This allows
the original thread to continue while the onCompletion route is running
concurrently. We chose this model as we did not want the onCompletion
route to interfere with the original route.
:::
::::

::::::::::: sect2
### Multiple onCompletions {#_multiple_oncompletions}

::: paragraph
You may define multiple onCompletions at both context and route level.
:::

::: paragraph
When you define route level onCompletions then any context levels are
disabled for that given route.
:::

:::: listingblock
::: content
``` highlight
from("direct:start")
    .onCompletion()
        // this route is only invoked when the original route is complete as a kind
        // of completion callback
        .to("log:sync")
        .to("mock:sync")
    // must use end to denote the end of the onCompletion route
    .end()
    // here the original route contiunes
    .process(new MyProcessor())
    .to("mock:result");
```
:::
::::

::: paragraph
By default the OnCompletion EIP will be triggered when the Exchange is
complete and regardless if the Exchange completed with success or with a
failure (such as an Exception was thrown). You can limit the trigger to
only occur `onCompleteOnly` or by `onFailureOnly` as shown below:
:::

:::: listingblock
::: content
``` highlight
from("direct:start")
    // here we qualify onCompletion to only invoke when the exchange failed (exception or FAULT body)
    .onCompletion().onFailureOnly()
        .to("log:sync")
        .to("mock:syncFail")
    // must use end to denote the end of the onCompletion route
    .end()
    .onCompletion().onCompleteOnly()
        .to("log:sync")
        .to("mock:syncOK")
    .end()
    // here the original route continues
    .process(new MyProcessor())
    .to("mock:result");
```
:::
::::

::: paragraph
You can identify if the Exchange is an OnCompletion Exchange as Camel
will add the property `Exchange.ON_COMPLETION` with a boolean value of
`true`.
:::
:::::::::::

::::::::: sect2
### Using onCompletion from XML DSL {#_using_oncompletion_from_xml_dsl}

::: paragraph
The onCompletion is defined like this with XML DSL:
:::

:::: listingblock
::: content
``` highlight
<route>
    <from uri="direct:start"/>
    <!-- this onCompletion block will only be executed when the exchange is done being routed -->
    <!-- this callback is always triggered even if the exchange failed -->
    <onCompletion>
        <!-- so this is a kinda like an after completion callback -->
        <to uri="log:sync"/>
        <to uri="mock:sync"/>
    </onCompletion>
    <process ref="myProcessor"/>
    <to uri="mock:result"/>
</route>
```
:::
::::

::: paragraph
And the `onCompleteOnly` and `onFailureOnly` is defined as a boolean
attribute on the `<onCompletion>` tag, so the failure example would be:
:::

:::: listingblock
::: content
``` highlight
<route>
    <from uri="direct:start"/>
    <!-- this onCompletion block will only be executed when the exchange is done being routed -->
    <!-- this callback is only triggered when the exchange failed, as we have onFailureOnly=true -->
    <onCompletion onFailureOnly="true">
        <to uri="log:sync"/>
        <to uri="mock:sync"/>
    </onCompletion>
    <process ref="myProcessor"/>
    <to uri="mock:result"/>
</route>
```
:::
::::
:::::::::

:::::::::: sect2
### onCompletion with global level {#_oncompletion_with_global_level}

::: paragraph
This works just like the route level except from the fact that they are
defined globally. An example below:
:::

:::: listingblock
::: content
``` highlight
// define a global on completion that is invoked when the exchange is done being routed
onCompletion().to("log:global").to("mock:sync");

from("direct:start")
    .process(new MyProcessor())
    .to("mock:result");
```
:::
::::

::: paragraph
And in XML:
:::

:::: listingblock
::: content
``` highlight
<!-- this is a global onCompletion route that is invoked when any exchange is done being routed
     as a kind of after callback -->
<onCompletion>
    <to uri="log:global"/>
    <to uri="mock:sync"/>
</onCompletion>

<route>
    <from uri="direct:start"/>
    <process ref="myProcessor"/>
    <to uri="mock:result"/>
</route>
```
:::
::::

::: {.admonitionblock .important}
+-----------------------------------+-----------------------------------+
| ::: title                         | If an `onCompletion` is defined   |
| Important                         | in a route, it overrides **all**  |
| :::                               | global scoped, and thus it is     |
|                                   | only the route scoped that is     |
|                                   | used. The globally scoped are not |
|                                   | in use.                           |
+-----------------------------------+-----------------------------------+
:::
::::::::::

:::::: sect2
### Using onCompletion with onWhen predicate {#_using_oncompletion_with_onwhen_predicate}

::: paragraph
As other DSL in Camel you can attach a predicate to the `onCompletion`,
so it only triggers in certain conditions, when the predicate matches.
For example to only trigger if the message body contains the word
*Hello* we can do like:
:::

:::: listingblock
::: content
``` highlight
from("direct:start")
    .onCompletion().onWhen(body().contains("Hello"))
        // this route is only invoked when the original route is done being routed
        // and the onWhen predicate is true
        .to("log:sync")
        .to("mock:sync")
    // must use end to denote the end of the onCompletion route
    .end()
    // here the original route continues
    .to("log:original")
    .to("mock:result");
```
:::
::::
::::::
::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::: sect1
## Using onCompletion with or without thread pool {#_using_oncompletion_with_or_without_thread_pool}

:::::::::::::::::::::::: sectionbody
::: paragraph
To use a thread pool then either set a `executorService` or set
`parallelProcessing` to true.
:::

::: paragraph
For example in Java DSL do
:::

:::: listingblock
::: content
``` highlight
onCompletion().parallelProcessing()
    .to("mock:before")
    .delay(1000)
    .setBody(simple("OnComplete:${body}"));
```
:::
::::

::: paragraph
And in XML DSL:
:::

:::: listingblock
::: content
``` highlight
<onCompletion parallelProcessing="true">
  <to uri="mock:before"/>
  <delay><constant>1000</constant></delay>
  <setBody><simple>OnComplete:${body}</simple></setBody>
</onCompletion>
```
:::
::::

::: paragraph
You can also refer to a specific thread pool to be used, using the
`executorServiceRef` option
:::

:::: listingblock
::: content
``` highlight
<onCompletion executorServiceRef="myThreadPool">
  <to uri="mock:before"/>
  <delay><constant>1000</constant></delay>
  <setBody><simple>OnComplete:${body}</simple></setBody>
</onCompletion>
```
:::
::::

::::::::::::: sect2
### OnCompletion consumer modes {#_oncompletion_consumer_modes}

::: paragraph
OnCompletion supports two modes that affect the route consumer:
:::

::: ulist
- AfterConsumer - Default mode which runs after the consumer is done

- BeforeConsumer - Runs before the consumer is done, and before the
  consumer writes back response to the callee
:::

::: paragraph
The AfterConsumer mode is the default mode which is the same behavior as
in older Camel releases.
:::

::: paragraph
The new BeforeConsumer mode is used to run `onCompletion` before the
consumer writes its response back to the callee (if in InOut mode). This
allows the `onCompletion` to modify the Exchange, such as adding special
headers, or to log the Exchange as a response logger etc.
:::

::: paragraph
For example to always add a \"created by\" header you use
`modeBeforeConsumer()` as shown below:
:::

:::: listingblock
::: content
``` highlight
.onCompletion().modeBeforeConsumer()
    .setHeader("createdBy", constant("Someone"))
.end()
```
:::
::::

::: paragraph
And in XML DSL you set the mode attribute to BeforeConsumer:
:::

:::: listingblock
::: content
``` highlight
<onCompletion mode="BeforeConsumer">
  <setHeader name="createdBy">
    <constant>Someone</constant>
  </setHeader>
</onCompletion>
```
:::
::::
:::::::::::::
::::::::::::::::::::::::
:::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
