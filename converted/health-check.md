::: {#header}
# Health Checks
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
:::::: {#preamble}
::::: sectionbody
::: paragraph
Camel provides support to probe the state of an integration via a
pluggable Health Check strategy based on the following concepts:
:::

::: ulist
- **HealthCheck:** represents a health check and defines its basic
  contract.

- **HealthCheckResponse:** represents a health check invocation
  response.

- **HealthCheckConfiguration:** a basic configuration object that holds
  some basic settings like the minimum delay between calls, the number
  of times a service may be reported as unhealthy before marking the
  check as failed; besides these simple options, the check
  implementation is responsible for implementing further limitations
  where needed.

- **HealthCheckRegistry:** a registry for health checks. There is a
  single default implementation and end users should really not
  implement their own.

- **HealthCheckRepository:** a simple interface to define health check
  providers. By default there is one that grabs all the checks available
  in the registry so you can add your own check i.e. instantiating your
  bean in spring/spring-boot; components can provide their own
  repository.
:::
:::::
::::::

:::::: sect1
## Health checks out of the box {#_health_checks_out_of_the_box}

::::: sectionbody
::: paragraph
Camel provides the following health checks out of the box:
:::

::: ulist
- *context* - A `HealthCheck` which performs check whether the
  `CamelContext` is started. This can be used for readiness checks; to
  know when Camel is fully started and ready to handle traffic.

- *routes* - A `HealthCheckRegistry` which discovers all the available
  routes in `CamelContext` and checks whether they are all started. This
  can be used for readiness checks; to know when Camel is fully started
  and ready to handle traffic. Combining with the supervised
  `RouteController` this allows to perform readiness check for routes
  that are under supervising, such as troublesome routes that may not
  start up the first time, and are retried to be started in the
  background with backoff delays.

- *consumers* A `HealthCheckRegistry` which discovers all the consumers
  from all the routes in `CamelContext` enabling fine-grained checks for
  the route input. This means if the consumer is failing to poll new
  messages then the health check can detect this and report as
  un-healthy. Note that the consumer will be DOWN during its
  initialization and until at least one poll run has been executed, to
  know whether that poll was a success or not. And if the poll was a
  success then the consumer is UP. This is on purpose to avoid having
  the health check to be initially UP and then after first poll run then
  DOWN again because the consumer cannot poll from external system;
  which causes the health check to be *flaky*.

- *producers* A `HealthCheckRegistry` which are for producer based
  health-checks. There are a few components which comes with producer
  health checks such as `camel-kafka` and many of the `AWS` components.
  However, producer health checks are by default disabled. See further
  below for how to enable producer health checks.

- *registry* - A `HealthCheckRegistry` which discovers all the available
  custom `HealthCheck` instances in the `Registry`.
:::
:::::
::::::

::::::: sect1
## IDs {#_ids}

:::::: sectionbody
::: paragraph
A `HealthCheck` and `HealthCheckRegistry` has an ID. The ID has the name
syntax **`name`**`-health-check`, or
**`name`**`-health-check-repository`. With the suffix `-health-check` or
`-health-check-repository`. When looking up or resolving by IDs then the
shorthand name can be used.
:::

::: paragraph
For example `context-health-check` is the ID but can also be used by its
shorthand `context`.
:::

::: paragraph
The ID of the consumer health checks is the route id with `consumer:` as
prefix, i.e. if the route is named foo, then the ID is `consumer:foo`.
:::
::::::
:::::::

::::::::::: sect1
## Readiness and Liveness {#_readiness_and_liveness}

:::::::::: sectionbody
::: paragraph
Readiness and Liveness probes serve distinct purposes. Readiness
indicates whether the application is ready to serve requests or traffic.
Liveness probes indicates whether the application is alive and
functioning. A health check is by default usable for both readiness and
liveness checks.
:::

::: paragraph
To check wether a health check is usable for readiness or livesness:
:::

:::: listingblock
::: content
``` highlight
HealthCheck healthCheck = HealthCheckHelper.getHealthCheck(camelContext, healthCheckId);

System.out.println("Readiness=" + healthCheck.isReadiness());
System.out.println("Live=" + healthCheck.isLiveness());
```
:::
::::

::: paragraph
To specify a custom health check as only usable for liveness checks, you
would need to turn off readiness, by overriding the `isReadiness` method
and return `false`.
:::

:::: listingblock
::: content
``` highlight
@Override
public boolean isReadiness() {
    return false;
}
```
:::
::::
::::::::::
:::::::::::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Configuring health-check {#_configuring_health_check}

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
Camel supports via `camel-main` to configure health-checks from
configuration files. This is possible for runtimes that leverage
`camel-main` such as Camel on Spring Boot, or Camel K.
:::

::: paragraph
Camel will automatically enable `context`, `routes`, `consumers`,
`producers` and `registry` health-checks if `camel-health` is detected
on the classpath. They are all enabled by default (except for
`producers` which is disabled).
:::

::: paragraph
However, you can configure them, for example to turn them off:
:::

:::: listingblock
::: content
``` highlight
# global option to turn health-check off (will not install health-check)
### camel.health.enabled=false

# allows to enable or disable health-checks from startup
# for example to only use context health-check
camel.health.routesEnabled=false
camel.health.consumersEnabled=false
camel.health.registryEnabled=false
```
:::
::::

::: paragraph
The same can also be done programmatically using the Camel health api:
:::

:::: listingblock
::: content
``` highlight
HealthCheckRepository consumersHealthCheckRepository = HealthCheckHelper.getHealthCheckRepository(context, "consumers");

if (consumersHealthCheckRepository != null) {
    consumersHealthCheckRepository.setEnabled(false);
}
```
:::
::::

:::::::::: sect2
### How to consume the health check {#_how_to_consume_the_health_check}

::: paragraph
Once you have the health check exposed as a service and the application
is up and running, you can check it by accessing the related endpoint:
:::

:::: listingblock
::: content
``` highlight
$ curl http://localhost:8080/q/health
{
    "status": "UP"
,
    "checks": [
        {
            "name": "context",
            "status": "UP"        },
        {
            "name": "route:route1",
            "status": "UP"        },
        {
            "name": "consumer:route1",
            "status": "UP"        }
    ]
}
```
:::
::::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | the path can change depending on  |
| Note                              | the runtime used and the          |
| :::                               | configuration.                    |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
When you're running on certain runtimes you will get more information by
default. This is also possible in the `camel-main` runtime provider.
However, you'll need to specify that as a request parameter:
:::

:::: listingblock
::: content
``` highlight
$ curl http://localhost:8080/q/health?data=true
{
    "status": "UP"
,
    "checks": [
        {
            "name": "context",
            "status": "UP",
            "data": {
                 "check.group": "camel",
                 "check.id": "context",
                 "check.kind": "ALL",
                 "context.name": "camel-1",
                 "context.phase": "5",
                 "context.status": "Started",
                 "context.version": "4.12.0-SNAPSHOT",
                 "failure.count": "0",
                 "invocation.count": "2",
                 "invocation.time": "2025-03-20T09:43:01.870896479+01:00[Europe/Madrid]",
                 "success.count": "2",
                 "success.start.time": "2025-03-20T09:42:59.074646004+01:00[Europe/Madrid]",
                 "success.time": "2025-03-20T09:43:01.870896479+01:00[Europe/Madrid]"
            }
        },
...
```
:::
::::
::::::::::

:::::::: sect2
### Configuring initial state {#_configuring_initial_state}

::: paragraph
The initial state of health-checks (readiness). There are the following
states: UP, DOWN, UNKNOWN.
:::

::: paragraph
By default, the state is DOWN, is regarded as being pessimistic/careful.
This means that the overall health checks may report as DOWN during
startup and then only if everything is up and running flip to being UP.
:::

::: paragraph
Setting the initial state to UP, is regarded as being optimistic. This
means that the overall health checks may report as UP during startup and
then if a consumer or other service is in fact un-healthy, then the
health-checks can flip being DOWN.
:::

::: paragraph
Setting the state to UNKNOWN means that some health-check would be
reported in unknown state, especially during early bootstrap where a
consumer may not be fully initialized or validated a connection to a
remote system.
:::

::: paragraph
This option allows to pre-configure the state for different modes.
:::
::::::::

:::::::::::: sect2
### Excluding health-checks {#_excluding_health_checks}

::: paragraph
By default, all enabled health-checks are invoked by Camel when check
the health status.
:::

::: paragraph
It is possible to specify checks that should be excluded, for example a
specific route by the route-id.
:::

:::: listingblock
::: content
``` highlight
camel.health.exclude-pattern = myroute
```
:::
::::

::: paragraph
You can specify multiple patterns (and use wildcards) such as:
:::

:::: listingblock
::: content
``` highlight
camel.health.exclude-pattern = myroute,foo,bar*
```
:::
::::

:::: sect3
#### Routes that are not automatically started {#_routes_that_are_not_automatically_started}

::: paragraph
If a route is configured to not to automatically start
(`autoStartup=false`), then the route and its corresponding consumer
health checks is always up as it is externally managed.
:::
::::
::::::::::::

::::::::::::::::: sect2
### Turning off consumer level health-checks {#_turning_off_consumer_level_health_checks}

::: paragraph
If routes health check is enabled then they will execute consumer
health-checks as well as part of an aggregated response.
:::

::: paragraph
The consumer checks can be turned off, to only use the route level
checks (status of the route)
:::

:::: listingblock
::: content
``` highlight
camel.health.consumersEnabled=false
```
:::
::::

::: paragraph
You can also turn off individual consumers by prefixing the ID with
`consumer:` and the route id:
:::

:::: listingblock
::: content
``` highlight
camel.health.exclude-pattern = consumer:myroute*
```
:::
::::

::: paragraph
which will turn off checks for all consumers on routes that starts with
`myroute` in the route ID.
:::

::: paragraph
Or you can turn off producer based health checks by their component
name:
:::

:::: listingblock
::: content
``` highlight
camel.health.exclude-pattern = producer:kafka*
```
:::
::::

::: paragraph
Or all AWS producer health-checks
:::

:::: listingblock
::: content
``` highlight
camel.health.exclude-pattern = producer:aws*
```
:::
::::
:::::::::::::::::

::::::: sect2
### Turning on producer level health-checks {#_turning_on_producer_level_health_checks}

::: paragraph
Only consumer based health-checks is enabled by default.
:::

::: paragraph
Some components (in particular AWS) provides also health checks for
producers. These health checks can be enabled via:
:::

:::: listingblock
::: content
``` highlight
camel.health.producersEnabled=true
```
:::
::::
:::::::

:::::::: sect2
### Turning off health checks from components {#_turning_off_health_checks_from_components}

::: paragraph
Some Camel components comes with health-checks.
:::

::: paragraph
For example to turn off both consumer and producer health checks from
Kafka:
:::

:::: listingblock
::: content
``` highlight
camel.component.kafka.health-check-consumer-enabled = false
camel.component.kafka.health-check-producer-enabled = false
```
:::
::::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | You can turn of either consumer,  |
| Tip                               | producer or both.                 |
| :::                               |                                   |
+-----------------------------------+-----------------------------------+
:::
::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::: sect1
## JMX management {#_jmx_management}

::::::::::::::::: sectionbody
::: paragraph
The health check is manageable via JMX (requires `camel-management` JAR
on the classpath). You can find the `DefaultHealthCheck` MBean under the
`health` node in the Camel JMX tree.
:::

::: paragraph
This MBean allows at runtime to manage health-checks where you can
enable and disable checks based on their IDs. As well have the latest
status whether the overall health check is healthy or not. The MBean
also allows invoking health checks based on IDs (or all of them).
:::

:::::::::: sect2
### Configuring level of details in the health check responses {#_configuring_level_of_details_in_the_health_check_responses}

::: paragraph
The option `exposureLevel` sets the level of details to exposure as
result of invoking health checks.
:::

::: paragraph
There are the following levels:
:::

::: ulist
- *full* - The full level will include all details and status from all
  the invoked health checks.

- *default* - The default level will report UP if everything is okay,
  and only include detailed information for health check that was DOWN.

- *oneline* - The oneline level will only report either UP or DOWN.
:::

::: paragraph
You can specify multiple patterns (and use wildcards) such as:
:::

::: paragraph
For example to only report either as UP or DOWN then use:
:::

:::: listingblock
::: content
``` highlight
camel.health.exposure-level = oneline
```
:::
::::
::::::::::

:::::: sect2
### Enriching and controlling health check responses {#_enriching_and_controlling_health_check_responses}

::: paragraph
Each health check that is invoked will gather details about the result
using `HealthCheckResultBuilder`.
:::

::: paragraph
To allow enriching and manipulating the result, then you can use
`HealthCheckResultStrategy` to plug in a custom bean that can process
the result, and change state, add information, remove unwanted
information, etc. on the result builder.
:::

::: paragraph
The custom bean should be registered to the Camel
[Registry](registry.html) to be discovered by `camel-health`. Only one
instance of the bean is allowed.
:::
::::::
:::::::::::::::::
::::::::::::::::::

:::::: sect1
## Invoking health checks {#_invoking_health_checks}

::::: sectionbody
::: paragraph
You can invoke the health checks from Java by using the
`org.apache.camel.health.HealthCheckHelper` which has APIs to easily
invoke all the health checks and gather their results, or filter out
unwanted checks, or invoke only the readiness or liveness checks.
:::

::: paragraph
The health checks can also be invoked from JMX.
:::
:::::
::::::

::::::::: sect1
## Writing a custom health check {#_writing_a_custom_health_check}

:::::::: sectionbody
::: paragraph
There are a limited number of health checks provided by Camel out of the
box, so you may need to write your own check which you can do by
implementing the *HealthCheck* interface or by extending
*AbstractHealthCheck* which provides some useful methods.
:::

::: paragraph
To make the health-check discoverable you should annotate the class with
`@HealthCheck` and the *name*-check syntax.
:::

:::: listingblock
::: content
``` highlight
import org.apache.camel.spi.annotations.HealthCheck;
import org.apache.camel.impl.health.AbstractHealthCheck;

@HealthCheck("my-check")
public final class MyHealthCheck extends AbstractHealthCheck {

    public MyHealthCheck() {
        super("myapp", "my-check");
    }

    @Override
    protected void doCall(HealthCheckResultBuilder builder, Map<String, Object> options) {
        // Default value
        builder.unknown();

        // Add some details to the check result
        builder.detail("my.detail", camelContext.getName());

        if (unhealtyCondition) {
            builder.down();
        } else {
            builder.up();
        }
    }
}
```
:::
::::

::: paragraph
You can now make *MyHealthCheck* available to Camel by adding an
instance to (for example Spring application context) or directly to the
Camel [Registry](registry.html).
:::
::::::::
:::::::::

:::::::::::::::::::::: sect1
## Loading custom health checks {#_loading_custom_health_checks}

::::::::::::::::::::: sectionbody
::: paragraph
Camel can discover and load custom health checks from classpath
scanning. This requires to annotate the custom health checks with
`@HealthCheck` annotation on the class (see above).
:::

::: paragraph
Then the
[camel-component-maven-plugin](camel-component-maven-plugin.html) can be
used to automatic generated service loader file in
`META-INF/services/org/apache/camel/health-checks` that Camel uses to
discover the custom health checks.
:::

::: paragraph
You must also enable loading custom health check by setting (Camel Main,
Camel K, or Camel Quarkus):
:::

:::: listingblock
::: content
``` highlight
camel.main.load-health-checks = true
```
:::
::::

::: paragraph
And in Spring Boot:
:::

:::: listingblock
::: content
``` highlight
camel.springboot.load-health-checks = true
```
:::
::::

::: paragraph
And if you use the classic Spring XML, you can enable this on
`<camelContext>` as shown:
:::

:::: listingblock
::: content
``` highlight
<camelContext loadHealthChecks="true">
    ...
</camelContext>
```
:::
::::

::: paragraph
And of course you can also enable loading health checks from Java via
`CamelContext`:
:::

:::: listingblock
::: content
``` highlight
CamelContext context = ...
context.setLoadHealthChecks(true);
```
:::
::::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | The example `main-health` has a   |
| Tip                               | custom health check which is      |
| :::                               | loadable.                         |
+-----------------------------------+-----------------------------------+
:::

::::: sect2
### Loading custom health checks in Camel Quarkus {#_loading_custom_health_checks_in_camel_quarkus}

::: paragraph
If you use Camel Quarkus then you can write custom health checks with
[MicroProfile Health](components:others:microprofile-health.html), which
Camel Quarkus can automatically discover during build time compilation
and ensure are automatically loaded.
:::

::: paragraph
Using Camel's own health check APIs does however allow building health
checks that are usable anywhere you use Camel whether its standalone,
spring boot, quarkus, or something else.
:::
:::::
:::::::::::::::::::::
::::::::::::::::::::::

::::::: sect1
## Writing custom Health Check for Camel components {#_writing_custom_health_check_for_camel_components}

:::::: sectionbody
::: paragraph
You can implement custom health checks in Camel components (currently
only for consumers).
:::

::: paragraph
To do this, you implement the interface `HealthCheckAware` on your
consumer class, which should return the custom health check in the
getter method.
:::

::: paragraph
In the custom health check implementation, you can perform the check as
shown in the previous section. Camel will then use these custom
component health checks when it performs **routes** health checks.
:::
::::::
:::::::

:::::: sect1
## Examples {#_examples}

::::: sectionbody
::: paragraph
There are examples for Camel at:
:::

::: ulist
- Camel Standalone:
  [main-health](https://github.com/apache/camel-examples/tree/main/main-health)

- Camel Spring Boot:
  [health-checks](https://github.com/apache/camel-spring-boot-examples/tree/main/health-checks)

- Camel Quarkus:
  [health](https://github.com/apache/camel-quarkus-examples/tree/main/health)
:::
:::::
::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
