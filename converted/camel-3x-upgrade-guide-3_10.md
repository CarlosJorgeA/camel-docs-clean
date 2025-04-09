::: {#header}
# Apache Camel 3.x Upgrade Guide
:::

:::::::::::::::::::::::::::::::::: {#content}
::::: {#preamble}
:::: sectionbody
::: paragraph
This document is for helping you upgrade your Apache Camel application
from Camel 3.x to 3.y. For example if you are upgrading Camel 3.0 to
3.2, then you should follow the guides from both 3.0 to 3.1 and 3.1 to
3.2.
:::
::::
:::::

:::::::::::::::::::::::::::::: sect1
## Upgrading Camel 3.9 to 3.10 {#_upgrading_camel_3_9_to_3_10}

::::::::::::::::::::::::::::: sectionbody
::::::: sect2
### API changes {#_api_changes}

::: paragraph
The method `concurrentTasks` on
`` org.apache.camel.support.DefaultScheduledPollConsumerScheduler ` has been renamed to `concurrentConsumers ``.
:::

::: paragraph
The `org.apache.camel.cloud.ServiceFilter` and
`org.apache.camel.cloud.ServiceLoadBalancer` functional interface
methods take the current `Exchange` as additional parameter to allow for
content-based filtering of service candidates.
`RibbonServiceLoadBalancer` has no notion of a current exchange, service
filters therefore receive a dummy exchange when used with Ribbon.
:::

::: paragraph
The two methods `filterTypeInOutputs` on
`org.apache.camel.model.ProcessorDefinitionHelper` has changed to return
`Collection` instead of `Iterator`.
:::

::: paragraph
The annotation `@Consume` has removed its target for field/constructor
injection as the `@Consume` is only to be used on methods. The
annotations `@Produce` and `@EndpointInject` has removed its target for
constructor injection as that is not supported (Camel only does bean
post processing)
:::
:::::::

:::::: sect2
### camel-scheduler {#_camel_scheduler}

::: paragraph
The option `concurrentTasks` has been renamed to `poolSize` to better
reflect its purpose. The scheduler has also been fixed to only schedule
triggering by one, and not as mistakenly by causing concurrent
triggering which causes routing mistakes.
:::

::: paragraph
The option configures the pool size of the scheduled thread pool used by
the scheduler.
:::

::: paragraph
If the scheduler thread is being blocked by anywhere in the downstream
routing, and you want the scheduler to schedule with a fixed interval,
you can use the Threads EIP as queue for pending tasks.
:::
::::::

:::: sect2
### camel-jdbc {#_camel_jdbc}

::: paragraph
The `camel-jdbc` component no longer depends on Spring Framework,
instead a new `camel-spring-jdbc` component has been created. You should
use `camel-spring-jdbc` if you use Spring and need Spring Transaction
support. The `camel-jdbc` is using plain Java JDBC API only.
:::
::::

::::::: sect2
### camel-jsonpath {#_camel_jsonpath}

::: paragraph
This component now uses Jackson as the default json parser instead of
json-smart.
:::

::: paragraph
Usage of `JacksonJsonAdapter` has been slightly changed.
:::

::: paragraph
Component was trying to use `JacksonJsonAdapter` ahead of automatic
conversion of the payload to the `InputStream` in the previous version.
New version tries to convert the payload into `InpuStream` first.
`JacksonJsonAdapter` is used only if conversion to `InputStream` is not
possible.
:::

::: paragraph
Order of the basic payload types (like `String`, `Map`, `File`) has not
been changed.
:::
:::::::

:::::: sect2
### camel-huaweicloud-smn {#_camel_huaweicloud_smn}

::: paragraph
Initialization options for SMN client has slightly changed.
:::

::: paragraph
Earlier, the library configured the smn server url based on the region
parameter provided in endpoint configurations. Now, it provides more
flexibility by allowing camel users by providing fully qualified http
url for more customizability.
:::

::: paragraph
When endpoint URL is provided, it carries the higher precedence than
region based configuration. Any region configuration entered along with
endpoint url will be ignored.
:::
::::::

:::: sect2
### Camel-Azure-Storage-Blob {#_camel_azure_storage_blob}

::: paragraph
The service client is now autowired. No need to set explicitly on your
route if it is already in the registry. This is the reason why the
autoDiscoverClient option has been removed.
:::
::::

:::: sect2
### Camel-Azure-Storage-Queue {#_camel_azure_storage_queue}

::: paragraph
The service client is now autowired. No need to set explicitly on your
route if it is already in the registry.
:::
::::

:::: sect2
### Spring Boot Starters {#_spring_boot_starters}

::: paragraph
Some of the Camel Spring Boot starters have additional auto
configuration options that clashed with component. Therefore those
configurations has renamed their configuration keys:
:::

+-----------------------------------+-----------------------------------+
| **Old Key prefix**                | **New key prefix**                |
+-----------------------------------+-----------------------------------+
| camel                             | camel.cluster.atomix              |
| .component.atomix.cluster.service |                                   |
+-----------------------------------+-----------------------------------+
| camel                             | camel.cluster.consul              |
| .component.consul.cluster.service |                                   |
+-----------------------------------+-----------------------------------+
| camel.                            | ca                                |
| component.consul.service-registry | mel.cloud.consul.service-registry |
+-----------------------------------+-----------------------------------+
| cam                               | camel.cluster.file                |
| el.component.file.cluster.service |                                   |
+-----------------------------------+-----------------------------------+
| camel.component.hystrix.mapping   | camel.hystrix.mapping             |
+-----------------------------------+-----------------------------------+
| camel.compo                       | camel.cluster.jgroups             |
| nent.jgroups.lock.cluster.service |                                   |
+-----------------------------------+-----------------------------------+
| camel.compo                       | camel.cluster.jgroups-raft        |
| nent.jgroups.raft.cluster.service |                                   |
+-----------------------------------+-----------------------------------+
| camel.com                         | camel.cluster.kubernetes          |
| ponent.kubernetes.cluster.service |                                   |
+-----------------------------------+-----------------------------------+
| camel.component.servlet.mapping   | camel.servlet.mapping             |
+-----------------------------------+-----------------------------------+
| camel.c                           | camel.security.undertow           |
| omponent.undertow.spring.security |                                   |
+-----------------------------------+-----------------------------------+
| camel.co                          | camel.cluster.zookeeper           |
| mponent.zookeeper.cluster.service |                                   |
+-----------------------------------+-----------------------------------+
| camel.com                         | camel.cloud.zookeeper             |
| ponent.zookeeper.service-registry |                                   |
+-----------------------------------+-----------------------------------+
::::
:::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
