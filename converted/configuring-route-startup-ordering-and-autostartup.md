::: {#header}
# Configuring Routes Startup Ordering and Auto-startup
:::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
:::::: {#preamble}
::::: sectionbody
::: paragraph
Camel supports configuring two aspects:
:::

::: ulist
- auto-startup

- order of starting routes
:::
:::::
::::::

::::::::::::::::::::::::::::::::: sect1
## Auto-startup {#_auto_startup}

:::::::::::::::::::::::::::::::: sectionbody
::: paragraph
The `autoStartup` option allows is to configure Camel to **not** auto
start routes when Camel starts.
:::

::: paragraph
The auto startup can be configured on two levels:
:::

::: ulist
- Camel context: *Globally*

- Route: *Individually per route*
:::

::: paragraph
For example, the [CamelContext](camelcontext.html) below we have
configured `autoStartup=false` to prevent Camel starting all the routes
on startup.
:::

:::: listingblock
::: content
``` highlight
<camelContext id="myCamel" xmlns="http://camel.apache.org/schema/spring"
              autoStartup="false">
    <route>
        <from uri="direct:foo"/>
        <to uri="mock:foo"/>
    </route>
    <route>
        <from uri="direct:bar"/>
        <to uri="mock:bar"/>
    </route>
</camelContext>
```
:::
::::

::: paragraph
So how do you start the routes?
:::

::: paragraph
The `autoStartup` option on the `<camelContext>` is only used once, so
you can manually start Camel later by invoking its `start` method in
Java as shown below.
:::

::: paragraph
For example, when using Spring, you can get hold of the `CamelContext`
via the Spring `ApplicationContext`:
:::

:::: listingblock
::: content
``` highlight
ApplicationContext ac = ...
CamelContext camel = ac.getBean("myCamel", CamelContext.class);

// now start all the routes
camel.getRouteController().startAllRoutes();
```
:::
::::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | The routes can also be started    |
| Tip                               | via JMX (requires                 |
| :::                               | `camel-management` JAR to be on   |
|                                   | the classpath) by invoking the    |
|                                   | `startAllRoutes` operation on the |
|                                   | CamelContext MBean.               |
+-----------------------------------+-----------------------------------+
:::

::::::::::::::::::: sect2
### Auto-startup per route level {#_auto_startup_per_route_level}

::: paragraph
You can also use the `autoStartup` option to configure if a given route
should be started when Camel starts. By default, a route is auto
started.
:::

::: paragraph
In XML DSL, you disable auto startup as follows:
:::

:::: listingblock
::: content
``` highlight
<route autoStartup="false">
   <from uri="activemq:queue:special"/>
   <to uri="file://backup"/>
</route>
```
:::
::::

::: paragraph
And to explicit state it should be started:
:::

:::: listingblock
::: content
``` highlight
<route autoStartup="true">
   <from uri="activemq:queue:special"/>
   <to uri="file://backup"/>
</route>
```
:::
::::

::: paragraph
And in Java DSL you can configure auto startup on `CamelContext` as
follows:
:::

:::: listingblock
::: content
``` highlight
camelContext.setAutoStartup(false);
```
:::
::::

::: paragraph
And on route level you can disable it via `noAutoStartup`:
:::

:::: listingblock
::: content
``` highlight
from("activemq:queue:special").noAutoStartup()
    .to("file://backup");
```
:::
::::

::: paragraph
To startup based on a boolean, String or
[Property](components::properties-component.html), do one of the
following:
:::

:::: listingblock
::: content
``` highlight
// using a boolean
from("activemq:queue:special").autoStartup(false)
    .to("file://backup");

// using a string
from("activemq:queue:special").autoStartup("false")
    .to("file://backup");

// using property placeholders
from("activemq:queue:special").autoStartup("{{startupRouteProperty}}")
    .to("file://backup");
```
:::
::::
:::::::::::::::::::
::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::: sect1
## Configuring starting order for routes {#_configuring_starting_order_for_routes}

::::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
You can also configure the order in which routes are started.
Previously, Camel started the routes in a non-deterministic order. Now
you have fine-grained control in which order the routes should be
started. There is a new attribute `startupOrder` which is an `Integer`
that states the order. Camel then sorts the routes before starting time.
The routes with the lowest `startupOrder` are started first, and the
ones with the highest are started last.
:::

::: {.admonitionblock .important}
+-----------------------------------+-----------------------------------+
| ::: title                         | All `startupOrder` defined must   |
| Important                         | be unique among all routes in     |
| :::                               | your                              |
|                                   | [                                 |
|                                   | CamelContext](camelcontext.html). |
|                                   | Otherwise, if there are clashes   |
|                                   | in `startupOrder` numbers among   |
|                                   | routes, the routes will fail to   |
|                                   | start up throwing                 |
|                                   | `org.apache.ca                    |
|                                   | mel.FailedToStartRouteException`. |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
Normally you should also use numbers that are lower than 1000, as routes
without an explicit `startupOrder` definition will have a number
starting from 1000 auto assigned. So view numbers from 1000 upwards as
reserved internally for Camel itself.
:::

::: paragraph
However, you can also use higher numbers than 1000 (to avoid collisions
with those auto assigned numbers) to specify the last routes to start
up. Normally, the usage of numbers starting from 10000 should be safe
for the purpose.
:::

::: paragraph
In terms of the `startupOrder` there are no strict rules that it must
start from 1 and increment by 1. You can, for example, use: 100, 200,
205, 89 if you like. The only rule of thumb is that the numbers must be
unique.
:::

:::::: sect2
### Why do you want to control the starting order? {#_why_do_you_want_to_control_the_starting_order}

::: paragraph
It can help in cases where routes are inter-dependent on each other and
also help with graceful shutting down Camel as Camel can stop the routes
in the correct order as well.
:::

::: paragraph
Apache Camel will stop the routes in the **reverse** order that they
were started.
:::

::: paragraph
Let's try a couple of examples.
:::
::::::

::::::::: sect2
### Startup ordering example {#_startup_ordering_example}

:::: listingblock
::: content
``` highlight
from("seda:foo").startupOrder(1)
    .to("mock:result");

from("direct:start").startupOrder(2)
    .to("seda:foo");
```
:::
::::

::: paragraph
And the same example with XML DSL:
:::

:::: listingblock
::: content
``` highlight
<routes>
    <route startupOrder="1">
        <from uri="seda:foo"/>
        <to uri="mock:result"/>
    </route>

    <route startupOrder="2">
        <from uri="direct:start"/>
        <to uri="seda:foo"/>
    </route>
</routes>
```
:::
::::

::: paragraph
In this example, we have two routes in which we have started that the
direct:start route should be started **after** the seda:foo route.
Because direct:start is considered the input, and we want seda:foo route
to be up and running beforehand.
:::
:::::::::

::::::::::: sect2
### Using startOrder together with non startOrder {#_using_startorder_together_with_non_startorder}

::: paragraph
You can also mix and match routes with and without `startupOrder`
defined. The first two routes below have start order defined, and the
last route has not.
:::

:::: listingblock
::: content
``` highlight
from("seda:foo").startupOrder(1)
    .to("mock:result");

from("direct:start").startupOrder(2)
    .to("seda:foo");

from("direct:bar")
    .to("seda:bar");
```
:::
::::

::: paragraph
And the same example with XML DSL:
:::

:::: listingblock
::: content
``` highlight
<routes>
    <route startupOrder="1">
        <from uri="seda:foo"/>
        <to uri="mock:result"/>
    </route>

    <route startupOrder="2">
        <from uri="direct:start"/>
        <to uri="seda:foo"/>
    </route>

    <route>
        <from uri="direct:bar"/>
        <to uri="seda:bar"/>
    </route>
</routes>
```
:::
::::

::: paragraph
In the route above we have **not** defined a `startupOrder` on the last
route direct:bar in which Camel will auto assign a number for it, in
which this case will be 1000. Therefore, the route will be started last.
:::

::: paragraph
So you can use this to your advantage to only assign a `startupOrder` on
the routes which really needs it.
:::
:::::::::::

:::::::: sect2
### Configuring routes to start up last {#_configuring_routes_to_start_up_last}

::: paragraph
You can use a high number in `startupOrder` to have a specific route
startup last as shown below:
:::

:::: listingblock
::: content
``` highlight
// use auto assigned startup ordering
from("direct:start").to("seda:foo");

// should start first
from("seda:foo").startupOrder(1).to("mock:result");

// should start last after the default routes
from("direct:bar").startupOrder(12345).to("seda:bar");

// use auto assigned startup ordering
from("seda:bar").to("mock:other");
```
:::
::::

::: paragraph
In the example above, the order of route startups should be:
:::

::: {.olist .arabic}
1.  *`seda:foo`*

2.  *`direct:start`*

3.  *`seda:bar`*

4.  *`direct:bar`*
:::
::::::::

::::: sect2
### Shutting down routes {#_shutting_down_routes}

::: paragraph
Apache Camel will shut down the routes in the **reverse** order that
they were started.
:::

::: paragraph
See more at [Graceful Shutdown](graceful-shutdown.html).
:::
:::::
:::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
