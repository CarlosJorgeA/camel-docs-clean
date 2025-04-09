::: {#header}
# SimpleScheduledRoutePolicy
:::

:::::::::::::::::::::::::::::: {#content}
::::: {#preamble}
:::: sectionbody
::: paragraph
`SimpleScheduledRoutePolicy` is a
[ScheduledRoutePolicy](scheduledroutepolicy.html) that facilitates route
activation, de-activation, suspension and resumption of routes based on
the [Quartz](components::quartz-component.html) simple trigger.
:::
::::
:::::

::::::::::::: sect1
## How it works {#_how_it_works}

:::::::::::: sectionbody
::: paragraph
In order to use a `SimpleScheduledRoutePolicy` it is necessary to
instantiate an object of the type
`org.apache.camel.routepolicy.quartz.SimpleScheduledRoutePolicy`.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | All Scheduled route policies      |
| Note                              | share the scheduler created by    |
| :::                               | the Quartz component. In this     |
|                                   | way, scheduler, jobs and triggers |
|                                   | can be managed in a common and    |
|                                   | consistent way.                   |
+-----------------------------------+-----------------------------------+
:::

::::::::: sect2
### Configuration options {#_configuration_options}

::: paragraph
In order to perform a route operation at a given time the following
information must be provided.
:::

::: ulist
- Starting a route
:::

+-----------------+-----------------+-----------------+-----------------+
| Parameter Name  | Type            | Default         | Description     |
+=================+=================+=================+=================+
| routeStartDate  | java.util.Date  |                 | the initial     |
|                 |                 |                 | scheduled Date  |
|                 |                 |                 | and time for    |
|                 |                 |                 | route start     |
+-----------------+-----------------+-----------------+-----------------+
| routeS          | int             |                 | no of times to  |
| tartRepeatCount |                 |                 | repeat the job  |
+-----------------+-----------------+-----------------+-----------------+
| routeStar       | long            |                 | the time        |
| tRepeatInterval |                 |                 | interval in     |
|                 |                 |                 | milliseconds to |
|                 |                 |                 | trigger the     |
|                 |                 |                 | next attempt to |
|                 |                 |                 | start the route |
+-----------------+-----------------+-----------------+-----------------+

::: ulist
- Stopping a route
:::

+-----------------+-----------------+-----------------+-----------------+
| Parameter Name  | Type            | Default         | Description     |
+=================+=================+=================+=================+
| routeStopDate   | java.util.Date  |                 | the initial     |
|                 |                 |                 | scheduled Date  |
|                 |                 |                 | and time for    |
|                 |                 |                 | route stop      |
+-----------------+-----------------+-----------------+-----------------+
| route           | int             |                 | no of times to  |
| StopRepeatCount |                 |                 | repeat the job  |
+-----------------+-----------------+-----------------+-----------------+
| routeSto        | long            |                 | the time        |
| pRepeatInterval |                 |                 | interval in     |
|                 |                 |                 | milliseconds to |
|                 |                 |                 | trigger the     |
|                 |                 |                 | next attempt to |
|                 |                 |                 | stop the route  |
+-----------------+-----------------+-----------------+-----------------+
| route           | int             | 10 seconds      | the time period |
| StopGracePeriod |                 |                 | to wait before  |
|                 |                 |                 | initiating      |
|                 |                 |                 | graceful route  |
|                 |                 |                 | stop            |
+-----------------+-----------------+-----------------+-----------------+
| ro              | long            | TimeUn          | the time unit   |
| uteStopTimeUnit |                 | it.MILLISECONDS | for the grace   |
|                 |                 |                 | period          |
|                 |                 |                 | expressed as    |
|                 |                 |                 | java.util.conc  |
|                 |                 |                 | urrent.TimeUnit |
+-----------------+-----------------+-----------------+-----------------+

::: ulist
- Suspending a route
:::

+-----------------+-----------------+-----------------+-----------------+
| Parameter Name  | Type            | Default         | Description     |
+=================+=================+=================+=================+
| r               | java.util.Date  |                 | the initial     |
| outeSuspendDate |                 |                 | scheduled Date  |
|                 |                 |                 | and time for    |
|                 |                 |                 | route           |
|                 |                 |                 | suspension      |
+-----------------+-----------------+-----------------+-----------------+
| routeSus        | int             |                 | no of times to  |
| pendRepeatCount |                 |                 | repeat the job  |
+-----------------+-----------------+-----------------+-----------------+
| routeSuspen     | long            |                 | the time        |
| dRepeatInterval |                 |                 | interval in     |
|                 |                 |                 | milliseconds to |
|                 |                 |                 | trigger the     |
|                 |                 |                 | next attempt to |
|                 |                 |                 | suspend the     |
|                 |                 |                 | route           |
+-----------------+-----------------+-----------------+-----------------+

::: ulist
- Resuming a route
:::

+-----------------+-----------------+-----------------+-----------------+
| Parameter Name  | Type            | Default         | Description     |
+=================+=================+=================+=================+
| routeResumeDate | java.util.Date  |                 | the initial     |
|                 |                 |                 | scheduled Date  |
|                 |                 |                 | and time for    |
|                 |                 |                 | route start     |
+-----------------+-----------------+-----------------+-----------------+
| routeRe         | int             |                 | no of times to  |
| sumeRepeatCount |                 |                 | repeat the job  |
+-----------------+-----------------+-----------------+-----------------+
| routeResum      | long            |                 | the time        |
| eRepeatInterval |                 |                 | interval in     |
|                 |                 |                 | milliseconds to |
|                 |                 |                 | trigger the     |
|                 |                 |                 | next attempt to |
|                 |                 |                 | resume the      |
|                 |                 |                 | route           |
+-----------------+-----------------+-----------------+-----------------+

::: paragraph
Once the
`org.apache.camel.routepolicy.quartz.SimpleScheduledRoutePolicy` is
created it can be wired into the camel route as follows.
:::
:::::::::
::::::::::::
:::::::::::::

:::::::::: sect1
## Using the SimpleScheduledRoutePolicy {#_using_the_simplescheduledroutepolicy}

::::::::: sectionbody
::: paragraph
The `SimpleScheduledRoutePolicy` can be used in Java DSL as show:
:::

:::: listingblock
::: content
``` highlight
SimpleScheduledRoutePolicy policy = new SimpleScheduledRoutePolicy();
long startTime = System.currentTimeMillis() + 3000L;
policy.setRouteStartDate(new Date(startTime));
policy.setRouteStartRepeatCount(1);
policy.setRouteStartRepeatInterval(3000);

from("direct:start")
   .routeId("test")
   .routePolicy(policy)
   .to("mock:success");
```
:::
::::

::: paragraph
And in Spring XML:
:::

:::: listingblock
::: content
``` highlight
<bean id="date" class="org.apache.camel.routepolicy.quartz.SimpleDate"/>

<bean id="startPolicy" class="org.apache.camel.routepolicy.quartz.SimpleScheduledRoutePolicy">
    <property name="routeStartDate" ref="date"/>
    <property name="routeStartRepeatCount" value="1"/>
    <property name="routeStartRepeatInterval" value="3000"/>
</bean>

<camelContext xmlns="http://camel.apache.org/schema/spring">
    <route id="myroute" routePolicyRef="startPolicy">
        <from uri="direct:start"/>
        <to uri="mock:success"/>
    </route>
</camelContext>
```
:::
::::
:::::::::
::::::::::

::::::: sect1
## Dependency {#_dependency}

:::::: sectionbody
::: paragraph
Maven users will need to add a `camel-quartz` dependency to their
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
:::::::
::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
