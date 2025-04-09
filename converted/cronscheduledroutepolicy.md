::: {#header}
# CronScheduledRoutePolicy
:::

::::::::::::::::::::::::::::: {#content}
:::::: {#preamble}
::::: sectionbody
::: paragraph
`CronScheduledRoutePolicy` is a
[ScheduledRoutePolicy](scheduledroutepolicy.html) that facilitates route
activation, deactivation, suspension and resumption of routes based on a
[Quartz](components::quartz-component.html) cron trigger.
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | :::: paragraph                    |
| Tip                               | ::: title                         |
| :::                               | Relationship to the Quartz        |
|                                   | component                         |
|                                   | :::                               |
|                                   |                                   |
|                                   | All Scheduled route policies      |
|                                   | share the scheduler created by    |
|                                   | the                               |
|                                   | [Quartz](c                        |
|                                   | omponents::quartz-component.html) |
|                                   | component. In this way,           |
|                                   | scheduler, jobs and triggers can  |
|                                   | be managed in a common and        |
|                                   | consistent way.                   |
|                                   | ::::                              |
+-----------------------------------+-----------------------------------+
:::
:::::
::::::

:::::::::: sect1
## Using cron scheduled route policy {#_using_cron_scheduled_route_policy}

::::::::: sectionbody
::: paragraph
To use a `CronScheduledRoutePolicy` it is necessary to instantiate an
object of the type
`org.apache.camel.routepolicy.quartz.CronScheduledRoutePolicy`.
:::

::: paragraph
To perform a route operation at a given time, the following information
must be provided.
:::

::: sect2
### Starting a route {#_starting_a_route}

+-----------------+-----------------+-----------------+-----------------+
| Parameter Name  | Type            | Default Value   | Description     |
+=================+=================+=================+=================+
| routeStartTime  | String          |                 | the initial     |
|                 |                 |                 | scheduled date  |
|                 |                 |                 | and time as a   |
|                 |                 |                 | Cron Expression |
|                 |                 |                 | for route start |
+-----------------+-----------------+-----------------+-----------------+
:::

::: sect2
### Stopping a route {#_stopping_a_route}

+-----------------+-----------------+-----------------+-----------------+
| Parameter Name  | Type            | Default Value   | Description     |
+=================+=================+=================+=================+
| routeStopTime   | String          |                 | the initial     |
|                 |                 |                 | scheduled date  |
|                 |                 |                 | and time as a   |
|                 |                 |                 | Cron Expression |
|                 |                 |                 | for route stop  |
+-----------------+-----------------+-----------------+-----------------+
| route           | int             | 10000           | the time period |
| StopGracePeriod |                 |                 | to wait before  |
|                 |                 |                 | initiating      |
|                 |                 |                 | graceful route  |
|                 |                 |                 | stop            |
+-----------------+-----------------+-----------------+-----------------+
| ro              | long            | TimeUn          | the time unit   |
| uteStopTimeUnit |                 | it.MILLISECONDS | for the grace   |
|                 |                 |                 | period          |
|                 |                 |                 | expressed as    |
|                 |                 |                 | `               |
|                 |                 |                 | java.util.concu |
|                 |                 |                 | rrent.TimeUnit` |
+-----------------+-----------------+-----------------+-----------------+
:::

::: sect2
### Suspending a route {#_suspending_a_route}

+-----------------+-----------------+-----------------+-----------------+
| Parameter Name  | Type            | Default Value   | Description     |
+=================+=================+=================+=================+
| r               | String          |                 | the initial     |
| outeSuspendTime |                 |                 | scheduled date  |
|                 |                 |                 | and time as a   |
|                 |                 |                 | Cron Expression |
|                 |                 |                 | for route       |
|                 |                 |                 | suspension      |
+-----------------+-----------------+-----------------+-----------------+
:::

::: sect2
### Resuming a route {#_resuming_a_route}

+-----------------+-----------------+-----------------+-----------------+
| Parameter Name  | Type            | Default Value   | Description     |
+=================+=================+=================+=================+
| routeResumeTime | String          |                 | the initial     |
|                 |                 |                 | scheduled date  |
|                 |                 |                 | and time as a   |
|                 |                 |                 | Cron Expression |
|                 |                 |                 | for route       |
|                 |                 |                 | resumption      |
+-----------------+-----------------+-----------------+-----------------+
:::
:::::::::
::::::::::

:::::::::::::::: sect1
## Configuring the route policy {#_configuring_the_route_policy}

::::::::::::::: sectionbody
::: paragraph
Once the `org.apache.camel.routepolicy.quartz.CronScheduledRoutePolicy`
is created it can be wired into the Camel route as follows:
:::

::: paragraph
In Java:
:::

:::: listingblock
::: content
``` highlight
CronScheduledRoutePolicy startPolicy = new CronScheduledRoutePolicy();
startPolicy.setRouteStartTime("*/3 * * * * ?");

from("direct:start")
    .routeId("testRoute").routePolicy(startPolicy).noAutoStartup()
    .to("mock:success");
```
:::
::::

::: paragraph
And with Spring XML:
:::

:::: listingblock
::: content
``` highlight
<bean id="startPolicy" class="org.apache.camel.routepolicy.quartz.CronScheduledRoutePolicy">
    <property name="routeStartTime" value="*/3 * * * * ?"/>
</bean>

<camelContext xmlns="http://camel.apache.org/schema/spring">
    <route id="testRoute" routePolicyRef="startPolicy" autoStartup="false">
        <from uri="direct:start"/>
        <to uri="mock:success"/>
    </route>
</camelContext>
```
:::
::::

::: {.admonitionblock .important}
+-----------------------------------+-----------------------------------+
| ::: title                         | Notice how the route to be        |
| Important                         | scheduled **MUST** be configured  |
| :::                               | to not                            |
|                                   | [auto-                            |
|                                   | startup](configuring-route-startu |
|                                   | p-ordering-and-autostartup.html), |
|                                   | to let the route scheduler take   |
|                                   | control of starting and stopping  |
|                                   | the route accordingly.            |
+-----------------------------------+-----------------------------------+
:::

:::::: sect2
### Dependency {#_dependency}

::: paragraph
Maven users will need to add a camel-quartz dependency to their
`pom.xml` to avail this capability.
:::

:::: listingblock
::: content
``` highlight
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-quartz</artifactId>
    <version>x.x.x</version>
    <!-- use the same version as your Camel core version -->
</dependency>
```
:::
::::
::::::
:::::::::::::::
::::::::::::::::
:::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
