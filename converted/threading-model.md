::: {#header}
# Threading Model
:::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
::::::: {#preamble}
:::::: sectionbody
::: paragraph
The threading model in Camel is based on a pluggable reactive routing
engine, and thread pools from the JDK concurrency API.
:::

::: paragraph
This page focuses on thread pools. Camel leverages thread pools in
several places such as:
:::

::: ulist
- Several [EIP](components:eips:enterprise-integration-patterns.html)
  patterns support using thread pools for concurrency

- [SEDA](components::seda-component.html) component for asynchronous
  connectivity

- [Threads](components:eips:threads-eip.html) EIP in Camel routes

- Some components use thread pools out of the box, such as
  [JMS](components::jms-component.html) or
  [Jetty](components::jetty-component.html)
:::
::::::
:::::::

:::::::::::::::::::::::::: sect1
## Thread pool profiles {#_thread_pool_profiles}

::::::::::::::::::::::::: sectionbody
::: paragraph
By default, when a thread pool is to be created by Camel, then the pool
configuration is based upon a profile, the *default thread pool profile*
:::

::: paragraph
The default profile is pre-configured out of the box with the following
settings:
:::

+-----------------+-----------------+-----------------------------------+
| Option          | Default         | Description                       |
+=================+=================+===================================+
| **poolSize**    | `10`            | Sets the default core pool size   |
|                 |                 | (threads to keep minimum in pool) |
+-----------------+-----------------+-----------------------------------+
| **              | `60`            | Sets the default keep alive time  |
| keepAliveTime** |                 | (in seconds) for inactive threads |
+-----------------+-----------------+-----------------------------------+
| **maxPoolSize** | `20`            | Sets the default maximum pool     |
|                 |                 | size                              |
+-----------------+-----------------+-----------------------------------+
| *               | `1000`          | Sets the default maximum number   |
| *maxQueueSize** |                 | of tasks in the work queue. Use   |
|                 |                 | -1 for an unbounded queue.        |
+-----------------+-----------------+-----------------------------------+
| **allowCore     | `true`          | Sets default whether to allow     |
| ThreadTimeOut** |                 | core threads to timeout           |
+-----------------+-----------------+-----------------------------------+
| **r             | `CallerRuns`    | Sets the default handler for      |
| ejectedPolicy** |                 | tasks which cannot be executed by |
|                 |                 | the thread pool. Has four         |
|                 |                 | options:                          |
|                 |                 | `Abort, Ca                        |
|                 |                 | llerRuns, Discard, DiscardOldest` |
|                 |                 | which corresponds to the same     |
|                 |                 | four options provided out of the  |
|                 |                 | box in the JDK.                   |
+-----------------+-----------------+-----------------------------------+

::: paragraph
What that means is that for example when you use
[Multicast](components:eips:multicast-eip.html) with
`parallelProcessing=true` enabled, then it would create a thread pool
based on the profile above.
:::

::: paragraph
You can define as many thread pool profiles as you like. But there must
be only **one** default profile. A custom thread pool profile will
inherit from the default profile. Which means that any option you do not
explicit define will fallback and use the option from the default
profile.
:::

:::::::::::: sect2
### Configuring default thread pool profile {#_configuring_default_thread_pool_profile}

::: paragraph
In Spring XML you can configure thread pool profile with
`threadPoolProfile` as shown:
:::

:::: listingblock
::: content
``` highlight
<threadPoolProfile id="defaultThreadPoolProfile"
    defaultProfile="true"
    poolSize="5"
    maxPoolSize="10"/>
```
:::
::::

::: paragraph
And in Java DSL
:::

:::: listingblock
::: content
``` highlight
ThreadPoolProfile profile = camelContext.getExecutorServiceManager().getDefaultThreadPoolProfile();
profile.setPoolSize(5);
profile.setMaxPoolSize(10);
```
:::
::::

::: paragraph
And with camel-main, Spring Boot or Quarkus you can configure this in
the `application.properties|yaml` file:
:::

:::: listingblock
::: content
``` highlight
## configure default thread pool profile
camel.threadpool.pool-size = 5
camel.threadpool.max-pool-size = 5
```
:::
::::
::::::::::::

:::::::::: sect2
### Using thread pool profiles {#_using_thread_pool_profiles}

::: paragraph
Suppose you want to use a custom thread pool profile for a Multicast EIP
pattern in a Camel route you can do it using the `executorServiceRef`
attribute as shown in Spring XML:
:::

:::: listingblock
::: content
``` highlight
<camelContext>

    <threadPoolProfile id="fooProfile"
                       poolSize="20" maxPoolSize="50" maxQueueSize="-1"/>

    <route>
       <multicast aggregationStrategy="myStrategy" executorServiceRef="fooProfile">
          ...
       </multicast>
    </route>
</camelContext>
```
:::
::::

::: paragraph
What Camel will do at runtime is to lookup in the
[Registry](registry.html) for a `ExecutorService` with the id
`fooProfile`. If none found it will fallback and see if there is a
`ThreadPoolProfile` defined with that id. In this example there is a
profile, so the profile is used as base settings for creating a new
`ExecutorService` which is handed back to the
[Multicast](components:eips:multicast-eip.html) EIP to use in the Camel
route.
:::

::: paragraph
In Java DSL you can use `ThreadPoolProfileBuilder` to create a profile
and then register the profile:
:::

:::: listingblock
::: content
``` highlight
ThreadPoolProfileBuilder builder = new ThreadPoolProfileBuilder("fooProfile");
builder.poolSize(20).maxPoolSize(50).maxQueueSize(-1);

camelContext.getExecutorServiceManager().registerThreadPoolProfile(builder.build());
```
:::
::::
::::::::::
:::::::::::::::::::::::::
::::::::::::::::::::::::::

::::: sect1
## Creating custom thread pools {#_creating_custom_thread_pools}

:::: sectionbody
::: paragraph
You can also use the `<threadPool>` tag in Spring XML to create a
specific thread pool (`ExecutorService`). Notice that any options you do
not explicit define, will have Camel to use the default thread pool
profile as fallback. For example if you omit setting the `maxQueueSize`
then Camel will fallback and use the value from the default thread pool
profiles, which by default is `1000`.
:::
::::
:::::

::::::::::::::::::: sect1
## Customizing thread names {#_customizing_thread_names}

:::::::::::::::::: sectionbody
::: paragraph
On the `ExecutorServiceManager` you can configure the thread name
pattern using the `setThreadNamePattern` method, which defines the
thread names used when a thread pool creates a thread.
:::

::: paragraph
The default pattern is:
:::

:::: listingblock
::: content
``` highlight
Camel (#camelId#) thread ##counter# - #name#
```
:::
::::

::: paragraph
In the pattern you can use the following placeholders
:::

::: ulist
- `#camelId#` - The [CamelContext](camelcontext.html) name

- `#counter#` An unique incrementing counter

- `#name#` - The thread name

- `#longName#` - The long thread name which can include endpoint
  parameters etc.
:::

::: paragraph
In Spring XML the pattern can be set with `threadNamePattern` attribute
as shown:
:::

:::: listingblock
::: content
``` highlight
<camelContext threadNamePattern="Riding the thread #counter#">
  <route>
    <from uri="seda:start"/>
    <to uri="log:result"/>
    <to uri="mock:result"/>
  </route>
</camelContext>
```
:::
::::

::: paragraph
In Java DSL you can set the pattern as follows:
:::

:::: listingblock
::: content
``` highlight
CamelContext camel = ...
camel.getExecutorServiceManager().setThreadNamePattern("Riding the thread #counter#")
```
:::
::::

::: paragraph
And with camel-main, Spring Boot or Quarkus you can configure this in
the `application.properties|yaml` file:
:::

:::: listingblock
::: content
``` highlight
## camel-main or quarkus
camel.main.thread-name-pattern = Riding the thread #counter#

## spring boot
camel.springboot.thread-name-pattern = Riding the thread #counter#
```
:::
::::
::::::::::::::::::
:::::::::::::::::::

::::::::: sect1
## Shutting down thread pools {#_shutting_down_thread_pools}

:::::::: sectionbody
::: paragraph
All thread pools created by Camel will be properly shutdown when
`CamelContext` shutdowns which ensures no leaks in the pools in case you
run in a server environment with hot deployments and the likes.
:::

::: paragraph
The `ExecutorServiceManager` has APIs for shutting down thread pools
gracefully and aggressively. It is encouraged to use this API for
creating and shutting down thread pools.
:::

::: paragraph
The method `shutdownGraceful(executorService)` from
`ExecutorServiceManager` will shutdown graceful at first, until a
timeout value is hit. After that it shuts down aggressively, again using
the timeout value to wait for the operation to complete. This means you
can wait at most 2 x timeout for shutting down the thread pool.
:::

::: paragraph
The timeout value is by default `10000` millis. You can configure a
custom value on the `ExecutorServiceManager` if needed. During shutdown
Camel will log every 2 seconds at INFO level progress of shutting down
the thread pool. For example in case a shutdown takes a while, then
there is activity in the logs.
:::

::: paragraph
The APIs on `ExecutorServiceManager` that is related to shutting down a
thread pool is as follows:
:::

+-----------------+-----------------------------------------------------+
| Method          | Description                                         |
+=================+=====================================================+
| shutdown        | Marks the thread pool as shutdown (like calling the |
|                 | `ExecutorService.shutdown()` method).               |
+-----------------+-----------------------------------------------------+
| shutdownNow     | Forces the thread pool to shut down now (like       |
|                 | calling the `ExecutorService.shutdownNow()`         |
|                 | method).                                            |
+-----------------+-----------------------------------------------------+
| s               | Marks the thread pool as shutdown, and graceful     |
| hutdownGraceful | shutdown the pool, by waiting for tasks to          |
|                 | complete. A default timeout value of 10 sec is      |
|                 | used, before shutdown becomes aggressive using      |
|                 | `shutdownNow`, forcing threads to shut down         |
|                 | quicker.                                            |
+-----------------+-----------------------------------------------------+
| shutdownGr      | As shutdownGraceful but with custom timeout value   |
| aceful(timeout) |                                                     |
+-----------------+-----------------------------------------------------+
| a               | To wait graceful for the termination of a thread    |
| waitTermination | pool (eg to wait for its tasks to complete). Will   |
|                 | wait until all tasks are completed or timed out.    |
+-----------------+-----------------------------------------------------+
::::::::
:::::::::

:::::: sect1
## JMX Management {#_jmx_management}

::::: sectionbody
::: paragraph
All the thread pools that Camel creates are managed and thus you can see
them in JMX under the `threadpools` tree.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | This requires to enabled JMX by   |
| Note                              | including `camel-management` JAR  |
| :::                               | in the classpath.                 |
+-----------------------------------+-----------------------------------+
:::
:::::
::::::

::::::::: sect1
## Component developers {#_component_developers}

:::::::: sectionbody
::: paragraph
If you develop your own Camel component and are in need of a thread
pool, then it is advised to use the
`ExecutorServiceStrategy`/`ExecutorServiceManager` to create the thread
pool you need.
:::

:::: sect2
### ExecutorServiceStrategy {#_executorservicestrategy}

::: paragraph
Camel provides a pluggable strategy to hook in your own thread pool
provider, for example from a WorkManager in a J2EE server etc.\
See the `org.apache.camel.spi.ExecutorServiceStrategy` interface which
you should implement and hook into the WorkManager.
:::
::::

:::: sect2
### ExecutorServiceManager {#_executorservicemanager}

::: paragraph
To hook in custom thread pool providers (e.g. for J2EE servers) a
`ThreadPoolFactory` interface can be implemented. The implementation can
be set in the `ExecutorServiceManager`.
:::
::::
::::::::
:::::::::

:::::: sect1
## Virtual Threads {#_virtual_threads}

::::: sectionbody
::: paragraph
Starting from Java 21, the default `ThreadPoolFactory` can build
`ExecutorService` and `ScheduledExecutorService` that use [virtual
threads](https://openjdk.org/jeps/425) instead of platform threads. But
as it is an experimental feature, it is not enabled by default, you need
to set the System property `camel.threads.virtual.enabled` to `true` and
run Camel using Java 21 or above to enable it.
:::

::: paragraph
Be aware that even if it is enabled, there are some use cases where
platform threads are still used, for example, if the thread factory is
configured to create non-daemon threads since virtual threads can only
be daemons, or when the `ExecutorService` or `ScheduledExecutorService`
to build cannot have more than one thread or finally when `corePoolSize`
is set to zero and `maxQueueSize` is set to a value less or equal to
`0`.
:::
:::::
::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
