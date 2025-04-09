::: {#header}
# Graceful Shutdown
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
:::::: {#preamble}
::::: sectionbody
::: paragraph
Camel supports a pluggable shutdown strategy using
`org.apache.camel.spi.ShutdownStrategy`. Its responsible for shutting
down routes in a graceful manner. The other resources will still be
handled by [CamelContext](camelcontext.html) to shutdown. This leaves
the problem at hand with properly shutting down all the routes reliably
to the `ShutdownStrategy`.
:::

::: paragraph
Camel provides a default strategy in the
`org.apache.camel.impl.engine.DefaultShutdownStrategy` which is capable
of doing that.
:::
:::::
::::::

:::::::::::::::::::::::::::: sect1
## Default Shutdown Strategy {#_default_shutdown_strategy}

::::::::::::::::::::::::::: sectionbody
::: paragraph
The default strategy (`DefaultShutdownStrategy`) will gracefully
shutdown routes:
:::

::: ulist
- in the reverse order they were started

- let pending and current in flight exchanges run to completion before
  shutting down

- using a timeout of 45 seconds which then forces a *hard* shutdown
:::

::: paragraph
The settings are configurable, so you can change the timeout, and
whether in case of timeout to force a *hard* shutdown or ignore.
:::

::: paragraph
When shutting down the strategy will output to log the progress:
:::

:::: listingblock
::: content
``` highlight
2009-12-20 10:56:53,055 [main ] INFO DefaultCamelContext - Apache Camel (CamelContext:camel-1) is stopping
2009-12-20 10:56:53,056 [main ] INFO DefaultShutdownStrategy - Starting to graceful shutdown routes (timeout 45 seconds)
2009-12-20 10:56:53,059 [1: ShutdownTask] INFO DefaultShutdownStrategy - Waiting as there are still 5 inflight exchanges to complete before we can shutdown
2009-12-20 10:56:54,060 [1: ShutdownTask] INFO DefaultShutdownStrategy - Waiting as there are still 4 inflight exchanges to complete before we can shutdown
2009-12-20 10:56:55,061 [1: ShutdownTask] INFO DefaultShutdownStrategy - Waiting as there are still 3 inflight exchanges to complete before we can shutdown
2009-12-20 10:56:56,065 [1: ShutdownTask] INFO DefaultShutdownStrategy - Waiting as there are still 2 inflight exchanges to complete before we can shutdown
2009-12-20 10:56:57,066 [1: ShutdownTask] INFO DefaultShutdownStrategy - Waiting as there are still 1 inflight exchanges to complete before we can shutdown
2009-12-20 10:56:58,069 [main ] INFO DefaultShutdownStrategy - Graceful shutdown of routes complete in 5 seconds.
2009-12-20 10:56:58,072 [main ] INFO DefaultInflightRepository - Shutting down with no inflight exchanges.
2009-12-20 10:56:58,077 [main ] INFO DefaultCamelContext - Apache Camel (CamelContext:camel-1) stopped
```
:::
::::

::: paragraph
Notice how the strategy waits while there are inflight exchanges still
being processed before it shutdown the routes and after that Camel
itself.
:::

:::::::: sect2
### Suppressing logging due to timeout not allowing all inflight messages to complete {#_suppressing_logging_due_to_timeout_not_allowing_all_inflight_messages_to_complete}

::: paragraph
If a graceful shutdown could not shutdown cleanly within the given
timeout period, then Camel performs a more aggressive shutdown (hard) by
forcing routes and thread pools etc to shut down, and the routing engine
will reject continue processing [Exchange](exchange.html)s. If this
happens you may see WARN logs about [Exchange](exchange.html)s being
rejected and other failures due the forced shutdown.
:::

::: paragraph
If you do not want to see these logs, you can suppress this by setting
the option suppressLoggingOnTimeout to true.
:::

:::: listingblock
::: content
``` highlight
context.getShutdownStrategy().setSuppressLoggingOnTimeout(true);
```
:::
::::

::: paragraph
Notice that suppression is *a best effort* attempt and there may still
be some logging from 3rd party libraries, which Camel cannot control.
:::
::::::::

:::::::::: sect2
### Logging inflight exchange information on timeout {#_logging_inflight_exchange_information_on_timeout}

::: paragraph
If a graceful shutdown could not shutdown cleanly within the given
timeout period, then Camel performs a more aggressive shutdown by
forcing routes and thread pools etc to shut down. When the timeout
happens, then Camel logs information about the current inflight
exchanges, which shows from which route the exchange origins, and where
it currently is being routed. For example the logging below, shows that
there is 1 inflight exchange, that origins from route1, and currently is
still in route1 at the \"delay1\" node. The elapsed is time in millis
how long at the current node (eg delay1) and duration is total time in
mills.
:::

::: paragraph
If you enable DEBUG logging level on
`org.apache.camel.impl.engine.DefaultShutdownStrategy` then it logs the
same inflight exchange information during graceful shutdown
:::

:::: listingblock
::: content
``` highlight
2015-01-12 13:23:23,656 [ - ShutdownTask] INFO DefaultShutdownStrategy -
There are 1 inflight exchanges: InflightExchange:
[exchangeId=ID-davsclaus-air-62213-1421065401253-0-3,
fromRouteId=route1, routeId=route1, nodeId=delay1, elapsed=2007,
duration=2017]
```
:::
::::

::: paragraph
If you do not want to see these logs, you can turn this off by setting
the option logInflightExchangesOnTimeout to false.
:::

:::: listingblock
::: content
``` highlight
context.getShutdownStrategy().setLogInflightExchangesOnTimeout(false);
```
:::
::::
::::::::::

::::: sect2
### JMX managed {#_jmx_managed}

::: paragraph
The `ShutdownStrategy` is JMX aware, so you can manage it from a JMX
console. This allows to adjust the strategy at runtime.
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | The timeout settings can also be  |
| Tip                               | changed on the                    |
| :::                               | `CamelContextMBean`.              |
+-----------------------------------+-----------------------------------+
:::
:::::
:::::::::::::::::::::::::::
::::::::::::::::::::::::::::

::::: sect1
## Controlling ordering of routes {#_controlling_ordering_of_routes}

:::: sectionbody
::: paragraph
You can configure the order in which routes should be started, and thus
also the same order they are being shutdown. See more at [Configuring
route startup ordering and
autostartup](configuring-route-startup-ordering-and-autostartup.html).
:::
::::
:::::

::::::::::::: sect1
## Fine grained configuration {#_fine_grained_configuration}

:::::::::::: sectionbody
::: paragraph
You can control two areas that influence graceful shutdown in the Camel
routing:
:::

::: ulist
- `ShutdownRoute`

- `ShutdownRunningTask`
:::

::: paragraph
These options can be configured on two levels: *context* and *route*.
The route level take precedence over context, meaning that if not
explicit configured on the route level, then the context level is used.
:::

::::: sect2
### ShutdownRoute {#_shutdownroute}

::: paragraph
This option can control how a given route should act during graceful
shutdown. It has two values `Default` and `Defer`. The `Default` is
obviously the default option which lets Camel shutdown the route as
early as possible. The `Defer` is used to defer shutting down this route
to a later stage. This is useful when other routes are dependent upon
it. For example an internal route which other routes reuse.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | It is best to only defer shutting |
| Note                              | down internal routes only. Public |
| :::                               | routes should shut down as        |
|                                   | quickly as possible otherwise it  |
|                                   | will just keep intake new         |
|                                   | messages which will delay the     |
|                                   | shutdown processor. Or even have  |
|                                   | it timeout if a lot of new        |
|                                   | messages keep coming in.          |
+-----------------------------------+-----------------------------------+
:::
:::::

::::: sect2
### ShutdownRunningTask {#_shutdownrunningtask}

::: paragraph
This option control how a given route consumer acts during shutdown.
Most route consumer will only operate on a single task (message),
however the [Batch Consumer](batch-consumer.html) can operate on many
messages (in a batch). This option is for batch consumers.
:::

::: paragraph
The default value is `CompleteCurrentTaskOnly` which mean that the
current *in progress* task (message) will be completed and then the
consumer will shut down. The other option `CompleteAllTasks` allows the
consumer to complete all the tasks (messages) before shutting down. For
example a [File](components::file-component.html) consumer will process
all the pending files it has picked up before shutting down.
:::
:::::
::::::::::::
:::::::::::::

::::::::::::::: sect1
## Stop individual routes {#_stop_individual_routes}

:::::::::::::: sectionbody
::: paragraph
It is possible to stop (will do a gracefully shut down) an individual
route using `stopRoute(routeId)` method as shown:
:::

:::: listingblock
::: content
``` highlight
camelContext.getRouteController().stopRoute(routeId);
```
:::
::::

::: paragraph
Routes can also be stopped via JMX.
:::

::::::::: sect2
### Stopping and marking routes as failed due to an exception {#_stopping_and_marking_routes_as_failed_due_to_an_exception}

::: paragraph
It is possible to stop and fail (will do a gracefully shut down) an
individual route using `stopRoute(routeId, cause)` method as shown:
:::

:::: listingblock
::: content
``` highlight
Exception cause = ...
camelContext.getRouteController().stopRoute(routeId, cause);
```
:::
::::

::: paragraph
This will stop the route and then mark the route as failed with the
caused exception.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | The Camel [Health                 |
| Note                              | Check](health-check.html) detect  |
| :::                               | the route as failed and report it |
|                                   | as DOWN. If the route is manually |
|                                   | stopped, then the route is not    |
|                                   | marked as failed, and the [Health |
|                                   | Check](health-check.html) will    |
|                                   | report the status as UNKNOWN.     |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
Routes can also be stopped and failed via JMX.
:::
:::::::::
::::::::::::::
:::::::::::::::

::::::::::: sect1
## Implementing custom component or ShutdownStrategy {#_implementing_custom_component_or_shutdownstrategy}

:::::::::: sectionbody
::: paragraph
If you develop your own Camel component or want to implement your own
shutdown strategy then read this section for details.
:::

:::: sect2
### ShutdownStrategy {#_shutdownstrategy}

::: paragraph
You can implement your own strategy to control the shutdown by
implementing the `org.apache.camel.spi.ShutdownStrategy` and the set it
on the `CamelContext` using the `setShutdownStrategy` method.
:::
::::

:::::: sect2
### ShutdownAware {#_shutdownaware}

::: paragraph
The interface `org.apache.camel.spi.ShutdownAware` is an optional
interface consumers can implement to have fine grained control during
shutdown. The `ShutdownStrategy` must be able to deal with consumers
which implement this interface. This interface was introduced to cater
for in memory consumers such as [SEDA](components::seda-component.html)
which potentially have a number of pending messages on its internal in
memory queues. What this allows is to let it control the shutdown
process to let it complete its pending messages.
:::

::: paragraph
The method `getPendingExchangesSize` should return the number of pending
messages which reside on the in memory queues. The method
`deferShutdown` should return `true` to defer the shutdown to a later
stage, when there are no more pending and inflight messages.
:::

::: paragraph
[Batch Consumer](batch-consumer.html) should implement `ShutdownAware`
so they properly support the `ShutdownRunningTask` option. See
`GenericFileConsumer` for an example.
:::
::::::
::::::::::
:::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
