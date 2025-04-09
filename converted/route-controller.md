::: {#header}
# Route Controller
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
::::::: {#preamble}
:::::: sectionbody
::: paragraph
Camel uses a `RouteController` for managing the lifecycle of all the
routes. The controller is mainly in use during starting up the routes
when Camel startup.
:::

::: paragraph
The controller is handling which routes, and in which order they should
be started.
:::

::: paragraph
Camel offers two kinds of controllers. The classic default controller,
and a supervising controller that can attempt to restart routes that
failed during startup.
:::
::::::
:::::::

::::::::::::: sect1
## DefaultRouteController {#_defaultroutecontroller}

:::::::::::: sectionbody
::: paragraph
This is the default controller and strategy that has always been in use
by Camel. The controller works with the *fail fast* principle, that if
any routes fail to start, then it gives up and Camel itself fails to
start up.
:::

::: paragraph
The controller also starts the routes sequentially and uses a single
thread. This means there is no concurrent startup, meaning the routes
are started more reliably without concurrency issues.
:::

::: paragraph
If a route fails on startup then it is often its `Consumer` that
triggers an error in its startup. Some components offer a way to handle
this internally and attempt to recover from this. However, most
components do not offer such a feature and therefore in most situations,
the route would fail to startup and therefore also Camel itself. The JMS
component is an example of a component that can recover from startup
issues in the `Consumer` such as failing to connect to the JMS broker.
:::

::: paragraph
The `SupervisingRouteController` is capable of handling this, and
manages routes that have failed to startup, by taking over and
attempting to restart these routes.
:::

::: paragraph
Given the routes below:
:::

:::: listingblock
::: content
``` highlight
from("file:foo/cake")
  to("log:cake");

from("salesforce:cheese")
  to("log:cheese");
```
:::
::::

::: paragraph
Then the two routes may fail on startup. However, the first route with
the file component would very likely always start up as it is just using
the file system that is reliable.
:::

::: paragraph
The second route, however, is using Salesforce, which can fail to start
up if there is no network connecting to Salesforce.
:::
::::::::::::
:::::::::::::

::::::::::::::::::::::::::::::: sect1
## SupervisingRouteController {#_supervisingroutecontroller}

:::::::::::::::::::::::::::::: sectionbody
::: paragraph
A supervising capable `RouteController` that delays the startup of the
routes after the camel context startup and takes control of starting the
routes in a safe manner. This controller is able to retry starting
failing routes, and has various options to configure settings for
backoff between restarting routes.
:::

::: paragraph
If we take the same example again:
:::

:::: listingblock
::: content
``` highlight
from("file:foo/cake")
  to("log:cake");

from("salesforce:cheese")
  to("log:cheese");
```
:::
::::

::: paragraph
Then we can tell Camel to use the supervising route controller to let
Camel attempt to recover starting the salesforce route.
:::

:::::::::::: sect2
### Configuring Supervising Route Controller {#_configuring_supervising_route_controller}

::: paragraph
Enabling and configuring supervising route controller from Java:
:::

:::: listingblock
::: content
``` highlight
CamelContext camel = ...
SupervisingRouteController src = camel.getRouteController().supervise();
src.setBackOffDelay(5000);
src.setBackOffMaxAttempts(3);
src.setInitialDelay(1000);
src.setThreadPoolSize(2);
```
:::
::::

::: paragraph
If you use Camel with Spring Boot or Camel Main, you can also enable
supervising from `application.properties`:
:::

:::: listingblock
::: content
``` highlight
camel.routecontroller.enabled = true

# and you can configure more options
camel.routecontroller.backoffDelay = 5000
camel.routecontroller.backoffMaxAttempts = 3
camel.routecontroller.initialDelay = 1000
camel.routecontroller.threadPoolSize = 2
```
:::
::::

::: paragraph
And for users with Spring \<beans\> you can do as follows:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
    <routeController id="myController"
                     supervising="true" initialDelay="1000" threadPoolSize="2"
                     backOffDelay="5000" backOffMaxAttempts="3"/>
    <route>
      <from uri="file:foo/cake"/>
      <to uri="log:cake"/>
    </route>
    <route>
      <from uri="salesforce:cheese"/>
      <to uri="log:cheese"/>
    </route>
</camelContext>
```
:::
::::
::::::::::::

::::: sect2
### Supervising Route Controller Options {#_supervising_route_controller_options}

::: paragraph
You can configure the `SupervisingRouteController` using the following
options:
:::

+------+-------------+-------------------------------------------------+
| Op   | Default     | Description                                     |
| tion |             |                                                 |
+======+=============+=================================================+
| Ena  | `false`     | To enable using supervising route controller    |
| bled |             | which allows Camel to start up, and then, the   |
|      |             | controller takes care of starting the routes in |
|      |             | a safe manner. This can be used when you want   |
|      |             | to startup Camel despite a route may otherwise  |
|      |             | fail fast during startup and cause Camel to     |
|      |             | fail to startup as well. By delegating the      |
|      |             | route startup to the supervising route          |
|      |             | controller then its manages the startup using a |
|      |             | background thread. The controller allows to be  |
|      |             | configured with various settings to attempt to  |
|      |             | restart failing routes.                         |
+------+-------------+-------------------------------------------------+
| Init |             | Initial delay in milliseconds before the route  |
| ialD |             | controller starts, after CamelContext has been  |
| elay |             | started.                                        |
+------+-------------+-------------------------------------------------+
| Back | `2000`      | Backoff delay in milliseconds when restarting a |
| OffD |             | route that failed to startup.                   |
| elay |             |                                                 |
+------+-------------+-------------------------------------------------+
| Ba   |             | Backoff maximum number of attempts to restart a |
| ckOf |             | route that failed to startup. When this         |
| fMax |             | threshold has been exceeded then the controller |
| Atte |             | will give up attempting to restart the route,   |
| mpts |             | and the route will remain as stopped. Will by   |
|      |             | default attempt forever.                        |
+------+-------------+-------------------------------------------------+
| Bac  |             | Backoff maximum delay in milliseconds when      |
| kOff |             | restarting a route that failed to startup.      |
| MaxD |             |                                                 |
| elay |             |                                                 |
+------+-------------+-------------------------------------------------+
| B    |             | Backoff maximum elapsed time in milliseconds,   |
| ackO |             | after which the backoff should be considered    |
| ffMa |             | exhausted and no more attempts should be made.  |
| xEla |             |                                                 |
| psed |             |                                                 |
| Time |             |                                                 |
+------+-------------+-------------------------------------------------+
| B    | 1.0         | Backoff multiplier to use for exponential       |
| ackO |             | backoff. This is used to extend the delay       |
| ffMu |             | between restart attempts.                       |
| ltip |             |                                                 |
| lier |             |                                                 |
+------+-------------+-------------------------------------------------+
| I    |             | Pattern for filtering routes to be included as  |
| nclu |             | supervised. The pattern is matched on route id, |
| deRo |             | and endpoint uri for the route. Multiple        |
| utes |             | patterns can be separated by comma. For         |
|      |             | example, to include all kafka routes, you can   |
|      |             | say `kafka:`. And to include routes with        |
|      |             | specific route ids *`myRoute,myOtherRoute`*.    |
|      |             | The pattern supports wildcards and uses the     |
|      |             | matcher from                                    |
|      |             | `org.ap                                         |
|      |             | ache.camel.support.PatternHelper#matchPattern`. |
+------+-------------+-------------------------------------------------+
| E    |             | Pattern for filtering routes to be excluded as  |
| xclu |             | supervised. The pattern is matched on route id, |
| deRo |             | and endpoint uri for the route. Multiple        |
| utes |             | patterns can be separated by comma. For         |
|      |             | example, to exclude all JMS routes, you can say |
|      |             | `jms:`. And to exclude routes with specific     |
|      |             | route ids                                       |
|      |             | *`mySpecialRoute,myOtherSpecialRoute`*. The     |
|      |             | pattern supports wildcards and uses the matcher |
|      |             | from                                            |
|      |             | `org.ap                                         |
|      |             | ache.camel.support.PatternHelper#matchPattern`. |
+------+-------------+-------------------------------------------------+
| Th   | `1`         | The number of threads used by the route         |
| read |             | controller scheduled thread pool that are used  |
| Pool |             | for restarting routes. The pool uses 1 thread   |
| Size |             | by default, but you can increase this to allow  |
|      |             | the controller to concurrently attempt to       |
|      |             | restart multiple routes in case more than one   |
|      |             | route has problems starting.                    |
+------+-------------+-------------------------------------------------+
| Unhe | `true`      | Whether to mark the route as unhealthy (down)   |
| alth |             | when all restarting attempts (backoff) have     |
| yOnE |             | failed and the route is not successfully        |
| xhau |             | started and the route manager is giving up. If  |
| sted |             | setting this to `false` will make health checks |
|      |             | ignore this problem and allow to report the     |
|      |             | Camel application as UP.                        |
+------+-------------+-------------------------------------------------+
| U    | `true`      | Whether to mark the route as unhealthy (down)   |
| nhea |             | when the route failed to initially start, and   |
| lthy |             | is being controlled for restarting (backoff).   |
| OnRe |             | If setting this to false will make health       |
| star |             | checks ignore this problem and allow reporting  |
| ting |             | the Camel application as UP.                    |
+------+-------------+-------------------------------------------------+

::: {.admonitionblock .important}
+-----------------------------------+-----------------------------------+
| ::: title                         | The `UnhealthyOnExhausted` and    |
| Important                         | `UnhealthyOnRestarting` options   |
| :::                               | are default `false` in Camel 4.6  |
|                                   | or older.                         |
+-----------------------------------+-----------------------------------+
:::
:::::

::::::::::: sect2
### Filtering routes to fail fast {#_filtering_routes_to_fail_fast}

::: paragraph
When using supervising route controller, then all routes would by
default be supervised and allow Camel to start up successfully; even if
one or more routes failed to start up. This is because the supervising
will handle those failed routes and attempt to restart them in the
background (with backoff).
:::

::: paragraph
You may have a critical route which must always start up, and if not,
cause Camel itself to fail starting. This can be done by filtering the
route from the supervising with the include/exclude options.
:::

::: paragraph
Given the routes below:
:::

:::: listingblock
::: content
``` highlight
from("file:foo/cake")
  to("log:cake");

from("salesforce:cheese")
  to("log:cheese");

from("aws-s3:foo")
  .to("log:foo")
```
:::
::::

::: paragraph
Then suppose we should fail fast if any AWS route fails to startup. This
can be done by excluding by pattern `aws*` (uri or route id)
:::

:::: listingblock
::: content
``` highlight
camel.routecontroller.excludeRoutes = aws*
```
:::
::::
:::::::::::
::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::

:::::: sect1
## JMX management {#_jmx_management}

::::: sectionbody
::: paragraph
The route controllers are manageable in JMX, where you can find their
MBean under the `services` node.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | To use JMX with Camel then        |
| Note                              | `camel-management` JAR must be    |
| :::                               | included in the classpath.        |
+-----------------------------------+-----------------------------------+
:::
:::::
::::::

::::: sect1
## More Information {#_more_information}

:::: sectionbody
::: paragraph
When Apache Camel is shutting down, then its [Graceful
Shutdown](graceful-shutdown.html) that handles this to ensure all the
routes are shutdown graceful and safely.
:::
::::
:::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
