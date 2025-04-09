::: {#header}
# Service Registry
:::

::::::::::::::::::::::::::::::::::::::::: {#content}
::::: {#preamble}
:::: sectionbody
::: paragraph
Service registration is a key part of service discovery which Camel
leverages through the *Service Call EIP* and support to ease the process
to expose routes in a cloud environment and consume them with minimal
configuration.
:::
::::
:::::

:::::::: sect1
## Service Registry Set-Up {#_service_registry_set_up}

::::::: sectionbody
::: paragraph
A `ServiceRegistry` is just like any other camel service so set it up
you only need to register your implementations to the `CamelContext`:
:::

:::: listingblock
::: content
``` highlight
ServiceRegistry service = new MyServiceRegistry();

context.addService(service);
```
:::
::::

::: paragraph
The configuration of the *Service Registry* depends on the
implementation you have chosen. Out of the box camel provides the
following implementations:
:::

+-----------------+-----------------+-----------------------------------+
| Type            | Module          | Class                             |
+=================+=================+===================================+
| consul          | camel-consul    | org.apache.camel.component.c      |
|                 |                 | onsul.cloud.ConsulServiceRegistry |
+-----------------+-----------------+-----------------------------------+
| spring-cloud    | cam             | org.a                             |
|                 | el-spring-cloud | pache.camel.component.spring.clou |
|                 |                 | d.CamelSpringCloudServiceRegistry |
+-----------------+-----------------+-----------------------------------+
| zookeeper       | camel-zookeeper | o                                 |
|                 |                 | rg.apache.camel.component.zookeep |
|                 |                 | er.cloud.ZooKeeperServiceRegistry |
+-----------------+-----------------+-----------------------------------+
:::::::
::::::::

::::::::::::::::::::::::::::::: sect1
## Service Registry Usage {#_service_registry_usage}

:::::::::::::::::::::::::::::: sectionbody
::: paragraph
The *Service Registry SPI* is leveraged by the following new
implementations:
:::

::::::::::::::::: sect2
### ServiceRegistryRoutePolicy {#_serviceregistryroutepolicy}

::: paragraph
This is an implementation of a [Route Policy](route-policy.html) that
register/deregister routes to a given `ServiceRegistry` according to
route's life-cycle:
:::

:::: listingblock
::: content
``` highlight
RoutePolicy policy = new ServiceRegistrationRoutePolicy()

// bind the policy to one or more routes
from("undertow:http://0.0.0.0:8080")
    .routePolicy(policy)
    .log("Route ${routeId} has been invoked");
```
:::
::::

::: paragraph
To apply the same policy to all the routes a dedicated
`RoutePolicyFactory` can be used:
:::

:::: listingblock
::: content
``` highlight
// add the service registry route policy factory to context
context.addRoutePolicyFactory(new ServiceRegistrationRoutePolicyFactory()));
```
:::
::::

::: paragraph
To configure how the service is exposed you can add route specific
properties like:
:::

:::: listingblock
::: content
``` highlight
// bind the policy to one or more routes
from("undertow:http://0.0.0.0:8080")
    .routePolicy(policy)
    .routeProperty(ServiceDefinition.SERVICE_META_NAME, "my-service")
    .routeProperty(ServiceDefinition.SERVICE_META_ID, "my-id")
    .routeProperty(ServiceDefinition.SERVICE_META_PORT, "8080")
    .log("Route ${routeId} has been invoked");
```
:::
::::

::: paragraph
Service name and service id can also be provided by *routeId* and
*routeGroup*:
:::

:::: listingblock
::: content
``` highlight
// bind the policy to one or more routes
from("undertow:http://0.0.0.0:8080")
    .routePolicy(policy)
    .routeGroup("my-service")
    .routeId("my-id")
    .routeProperty(ServiceDefinition.SERVICE_META_PORT, "8080")
    .log("Route ${routeId} has been invoked");
```
:::
::::

::: paragraph
Any property prefixed with *service.* is automatically added to the
service's metadata.
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | Some component such has           |
| Tip                               | camel-undertow and those based on |
| :::                               | camel-http-common implement       |
|                                   | *DiscoverableService* and they    |
|                                   | can automatically provide the     |
|                                   | metadata needed for service       |
|                                   | registration.                     |
+-----------------------------------+-----------------------------------+
:::
:::::::::::::::::

::::::::::::: sect2
### Service Component {#_service_component}

::: paragraph
The [Service](components::service-component.html) component is similar
to a `ServiceRegistrationRoutePolicyFactory` but is capable of tagging
routes that need to be registered to the `ServiceRegistry` by prefixing
the related endpoints with `service:name` according to the following
syntax:
:::

:::: listingblock
::: content
``` highlight
service:serviceName:delegateUri[?options]
```
:::
::::

::: paragraph
Let's explain this with an example:
:::

:::: listingblock
::: content
``` highlight
from("service:my-service:undertow:http://0.0.0.0:8080")
    .log("Route ${routeId} has been invoked");
```
:::
::::

::: paragraph
To configure how the service is exposed you can add service specific
endpoint options such as:
:::

:::: listingblock
::: content
``` highlight
from("service:my-service:undertow:http://0.0.0.0:8080?service.id=my-service-id")
    .log("Route ${routeId} has been invoked");
```
:::
::::

::: paragraph
Any option prefixed with *service.* is automatically added to the
service's metadata.
:::
:::::::::::::
::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
