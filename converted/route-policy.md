::: {#header}
# RoutePolicy
:::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
::::: {#preamble}
:::: sectionbody
::: paragraph
A route policy `org.apache.camel.spi.RoutePolicy` is used to control
route(s) at runtime. For example, you can use it to determine whether a
route should be running or not. However, the policies can support any
kind of use cases.
:::
::::
:::::

::::::::::: sect1
## How it works {#_how_it_works}

:::::::::: sectionbody
::: paragraph
You associate a route with a given `RoutePolicy` and then during runtime
Camel will invoke callbacks on this policy where you can implement your
custom logic. Camel provides a support class that is a good base class
to extend `org.apache.camel.support.RoutePolicySupport`.
:::

::: paragraph
There are these callbacks invoked:
:::

::: ulist
- `onInit`

- `onRemove`

- `onStart`

- `onStop`

- `onSuspend`

- `onResume`

- `onExchangeBegin`

- `onExchangeDone`
:::

::: paragraph
See the Javadoc of the `org.apache.camel.spi.RoutePolicy` for more
details; and also the implementation of the
`org.apache.camel.throttling.ThrottlingInflightRoutePolicy` for a
concrete example.
:::

::: paragraph
Camel provides the following policies out of the box:
:::

::: ulist
- `org.apache.camel.throttling.ThrottlingInflightRoutePolicy` - a
  throttling based policy that automatically suspends/resumes route(s)
  based on metrics from the current in flight exchanges. You can use
  this to dynamically throttle, e.g. a
  [JMS](components::jms-component.html) consumer, to avoid it consuming
  too fast.

- `org.apache.camel.throttling.ThrottlingExceptionRoutePolicy` - a
  throttling based policy modeled after the circuit breaker. This policy
  will stop consuming from an endpoint based on the type of exceptions
  that are thrown and the threshold setting.
:::

::: paragraph
Camel also provides an ability to schedule routes to be activated,
deactivated, suspended and/or resumed at certain times during the day
using a [ScheduledRoutePolicy](scheduledroutepolicy.html) (offered via
the [Quartz](components::quartz-component.html) component).
:::
::::::::::
:::::::::::

::::: sect1
## SuspendableService {#_suspendableservice}

:::: sectionbody
::: paragraph
If you want to dynamic suspend/resume routes, then it is advised to use
`SuspendableService` as it allows for fine-grained suspend and resume
operations.
:::
::::
:::::

::::::::: sect1
## `ThrottlingInflightRoutePolicy` {#_throttlinginflightroutepolicy}

:::::::: sectionbody
::: paragraph
The **`ThrottlingInflightRoutePolicy`** is triggered when an
[Exchange](exchange.html) is complete, which means that it requires at
least one [Exchange](exchange.html) to be complete before it *works*.
:::

::: paragraph
The throttling in flight route policy has the following options:
:::

+------+------+--------------------------------------------------------+
| Op   | Def  | Description                                            |
| tion | ault |                                                        |
+======+======+========================================================+
| `sc  | `Ro  | A scope for either `Route` or `Context` which defines  |
| ope` | ute` | if the current number of in flight exchanges is        |
|      |      | context based or for that particular route.            |
+------+------+--------------------------------------------------------+
| `m   | `1   | The maximum threshold when the throttling will start   |
| axIn | 000` | to suspend the route if the current number of in       |
| flig |      | flight exchanges is higher than this value.            |
| htEx |      |                                                        |
| chan |      |                                                        |
| ges` |      |                                                        |
+------+------+--------------------------------------------------------+
| `res | `70` | A percentage `0..100` which defines when the           |
| umeP |      | throttling should resume again in case it has been     |
| erce |      | suspended.                                             |
| ntOf |      |                                                        |
| Max` |      |                                                        |
+------+------+--------------------------------------------------------+
| `l   | `I   | The logging level used for logging the throttling      |
| oggi | NFO` | activity.                                              |
| ngLe |      |                                                        |
| vel` |      |                                                        |
+------+------+--------------------------------------------------------+
| `log | `Th  | The logger category.                                   |
| ger` | rott |                                                        |
|      | ling |                                                        |
|      | Infl |                                                        |
|      | ight |                                                        |
|      | Rout |                                                        |
|      | ePol |                                                        |
|      | icy` |                                                        |
+------+------+--------------------------------------------------------+

::::: sect2
### ThrottlingInflightRoutePolicy compared to the Throttler EIP {#_throttlinginflightroutepolicy_compared_to_the_throttler_eip}

::: paragraph
The `ThrottlingInflightRoutePolicy` compared to
[Throttler](components:eips:throttle-eip.html) EIP is that it does
**not** block during throttling. Its throttling is approximate-based,
meaning that its coarser grained and not explicitly precise as the
[Throttler](components:eips:throttle-eip.html) EIP.
:::

::: paragraph
The [Throttler](components:eips:throttle-eip.html) EIP can be much more
accurate and only allow a specific number of messages being passed per a
given time unit. Also, the `ThrottlingInflightRoutePolicy` is based its
metrics on number of in flight exchanges whereas
[Throttler](components:eips:throttle-eip.html) EIP is based on number o
messages per time unit.
:::
:::::
::::::::
:::::::::

::::: sect1
## ScheduledRoutePolicy {#_scheduledroutepolicy}

:::: sectionbody
::: paragraph
See [Scheduled Route Policy](scheduledroutepolicy.html) for scheduling
based route policy.
:::
::::
:::::

:::::::::::::::: sect1
## Using route policies in Camel routes {#_using_route_policies_in_camel_routes}

::::::::::::::: sectionbody
::: paragraph
You configure the route policy as follows from Java DSL, using the
`routePolicy` method:
:::

:::: listingblock
::: content
``` highlight
RoutePolicy myPolicy = new MyRoutePolicy();

from("seda:foo").routePolicy(myPolicy).to("mock:result");
```
:::
::::

::: paragraph
In Spring XML you configure using the `routePolictRef` attribute on
`<route>` as shown:
:::

:::: listingblock
::: content
``` highlight
<bean id="myPolicy" class="com.mycompany.MyRoutePolicy"/>

<route routePolicyRef="myPolicy">
    <from uri="seda:foo"/>
    <to uri="mock:result"/>
</route>
```
:::
::::

::: paragraph
You can configure one or more route policies (separated by comma), such
as:
:::

:::: listingblock
::: content
``` highlight
from("seda:foo").routePolicy(myPolicy, myOtherPolicy).to("mock:result");
```
:::
::::

::: paragraph
And in XML:
:::

:::: listingblock
::: content
``` highlight
<route routePolicyRef="myPolicy,myOtherPolicy">
    <from uri="seda:foo"/>
    <to uri="mock:result"/>
</route>
```
:::
::::
:::::::::::::::
::::::::::::::::

:::::::::::::::::: sect1
## Using RoutePolicyFactory {#_using_routepolicyfactory}

::::::::::::::::: sectionbody
::: paragraph
If you want to use a route policy for every route, you can use a
`org.apache.camel.spi.RoutePolicyFactory` as a factory for creating a
`RoutePolicy` instance for each route. This can be used when you want to
use the same kind of route policy for all or some routes.
:::

::: paragraph
With the factory, you only need to configure this once, and every route
created will have the policy assigned.
:::

::: paragraph
There is API on `CamelContext` to add a factory, as shown below
:::

:::: listingblock
::: content
``` highlight
context.addRoutePolicyFactory(new MyRoutePolicyFactory());
```
:::
::::

::: paragraph
And from XML DSL you just define a `<bean>` with the factory, and Camel
will automatically detect this factory:
:::

:::: listingblock
::: content
``` highlight
<bean id="myRoutePolicyFactory" class="com.foo.MyRoutePolicyFactory"/>
```
:::
::::

::: paragraph
You can have as many route policy factories as you want, so if you have
two factories, you can add them both as shown:
:::

:::: listingblock
::: content
``` highlight
context.addRoutePolicyFactory(new MyRoutePolicyFactory());
context.addRoutePolicyFactory(new MyOtherRoutePolicyFactory());
```
:::
::::

::: paragraph
And in XML:
:::

:::: listingblock
::: content
``` highlight
<bean id="myRoutePolicyFactory" class="com.foo.MyRoutePolicyFactory"/>
<bean id="myOtherRoutePolicyFactory" class="com.foo.MyOtherRoutePolicyFactory"/>
```
:::
::::
:::::::::::::::::
::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
