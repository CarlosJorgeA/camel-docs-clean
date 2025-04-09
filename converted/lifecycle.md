::: {#header}
# Camel Lifecycle
:::

:::::::::::::::::::::::::::: {#content}
::::::: {#preamble}
:::::: sectionbody
::: paragraph
Camel uses a simple *lifecycle* interface called
[Service](https://www.javadoc.io/doc/org.apache.camel/camel-api/current/org/apache/camel/Service.html)
which has `start()` and `stop()` methods.
:::

::: paragraph
Many of Camel's classes implement `Service` such as `CamelContext` along
with all `Component` and `Endpoint` classes.
:::

::: paragraph
When you use Camel you typically have to start the `CamelContext` which
will start all the various components and endpoints and activate the
routing rules until the context is stopped again.
:::
::::::
:::::::

::::::::::: sect1
## CamelContext Lifecycle {#_camelcontext_lifecycle}

:::::::::: sectionbody
::: paragraph
The `CamelContext` provides methods to control its lifecycle:
:::

::: ulist
- `build`

- `init`

- `start`

- `stop`

- `suspend`

- `resume`
:::

::: paragraph
The operations are paired: start/stop and suspend/resume.
:::

::: paragraph
Stop is performing a [Graceful Shutdown](graceful-shutdown.html) which
means all its internal state, cache, etc is cleared; and the routes is
being stopped in a graceful manner to ensure messages are given time to
complete.
:::

::: {.admonitionblock .important}
+-----------------------------------+-----------------------------------+
| ::: title                         | If you start a `CamelContext`     |
| Important                         | after a stop, then its performing |
| :::                               | a *cold* start, recreating all    |
|                                   | the state, cache etc. again;      |
|                                   | which is not guaranteed to        |
|                                   | startup correctly again. Instead  |
|                                   | you can use the suspend/resume    |
|                                   | operations. They will keep the    |
|                                   | `CamelContext` *warm* and only    |
|                                   | suspend/stop routes using the     |
|                                   | same graceful shutdown feature.   |
|                                   | This ensures messages are given   |
|                                   | time to complete.                 |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
End users is encouraged to use suspend/resume if you are temporary
stopping a Camel application.
:::

::: paragraph
All these operations are available in JMX as well, so you can control
Camel from JMX management.
:::
::::::::::
:::::::::::

::::::::: sect1
## Service lifecycle {#_service_lifecycle}

:::::::: sectionbody
::: paragraph
A service (`org.apache.camel.Service`) in Camel adheres to the following
lifecycle states as illustrated in the diagram below:
:::

:::: imageblock
::: content
![image](images/service_lifecycle.png)
:::
::::

::: paragraph
The `org.apache.camel.support.service.ServiceSupport` is a good base
class to extend for custom services as it offers the basic functionally
to keep track of state. You implement your custom logic in the
`doStart`, `doStop`, `doSuspend`, `doResume` methods.
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | A service can optimally support   |
| Tip                               | suspend/resume by the             |
| :::                               | `org.                             |
|                                   | apache.camel.SuspendableService`. |
|                                   | This means not all services in    |
|                                   | Camel supports suspension. It's   |
|                                   | encouraged that consumers support |
|                                   | suspension which allows           |
|                                   | suspending/resuming routes.       |
+-----------------------------------+-----------------------------------+
:::
::::::::
:::::::::

:::::: sect1
## Startup Lifecycle {#_startup_lifecycle}

::::: sectionbody
::: paragraph
When Camel startup there are various listeners that can be used to
plugin custom code, that can listen and react during startup such as
`LifecycleStrategy`, or `MainListener`.
:::

::: paragraph
If you need Camel to check for *something* before it can start up, then
look at [Startup Condition](startup-condition.html)
:::
:::::
::::::
::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
