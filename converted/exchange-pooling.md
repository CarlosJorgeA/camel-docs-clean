::: {#header}
# Exchange Pooling
:::

::::::::::::::::::::::::::::::::::::::: {#content}
::::::: {#preamble}
:::::: sectionbody
::: paragraph
The routing engine keeps the state of each message, tracking the message
flow, and where the messages are going next. The routing engine also
handles complex tasks such as error handling, capturing metrics, and
many other things. A small number of Java objects are allocated on the
heap for each processing step during routing.
:::

::: paragraph
Because of this routing bookkeeping and processing, Apache Camel
requires a tiny resource overhead when routing messages.
:::

::: paragraph
With the advance of cloud computing, where workloads are precisely
measured, then Camel has undergone a series of core optimizations to
reduce its overhead.
:::
::::::
:::::::

::::::::: sect1
## Pooling objects to reduce object allocations {#_pooling_objects_to_reduce_object_allocations}

:::::::: sectionbody
::: paragraph
The most significant object being reused is the
`org.apache.camel.Exchange` object. This object is the root object that
holds the message with its payload, headers, metadata, and other
content.
:::

::: paragraph
Besides pooling exchanges, the internal objects used by the routing
engine are also pooled and recycled. This can dramatically reduce the
object allocations from Camel core itself.
:::

::: paragraph
There will always be object allocations from the actual message content
(payload and headers), and also from the Camel components in use and its
3rd party libraries. The footprint of Camel core and its routing engine
is close to zero when pooling is enabled.
:::

::: paragraph
This all sounds marvelous so what are the negative aspects? The price to
pay for object pooling is the complexity of managing the pool (acquiring
and returning objects to the pool). However, all of this is taken care
of by Camel itself. The object pools are using JDK `ConcurrentMap`
instances and take up very little memory. Managing the pool causes a
small CPU overhead associated with adding and removing objects from the
pools, and resetting the objects before reuse.
:::

::: paragraph
In most cases, it may be possible to sacrifice the very tiny CPU cost in
exchange for reduced object allocations. This can improve responsiveness
and reduce latency by preventing the JVM garbage collection from running
more frequently (under some circumstances, the garbage collection
overhead can cause your application to halt or delay processing messages
due to stop-the-world pauses and other processing overhead associated
with it).
:::
::::::::
:::::::::

::::::::::::::::: sect1
## Enabling Exchange Pooling {#_enabling_exchange_pooling}

:::::::::::::::: sectionbody
::: paragraph
The *object pooling* is currently disabled by default. If you are using
Camel Main (from Camel Core), Camel Spring Boot, or Camel Quarkus, then
you can enable this in the `application.properties`:
:::

:::: listingblock
::: content
``` highlight
camel.main.exchange-factory = pooled
```
:::
::::

::::::: sect2
### Enabling Exchange Pooling on Camel 3.x {#_enabling_exchange_pooling_on_camel_3_x}

::: paragraph
If you are using a different runtime or want to do that
programmatically, you can do so in Camel version 3 (Camel 3.x) using the
`setExchangeFactory` method from the `ExtendedCamelContext`. Here's an
example of how to do that using Java:
:::

:::: listingblock
::: content
``` highlight
// suppose a Camel context object declared in the scope
CamelContext context = ...

context.adapt(ExtendedCamelContext.class).setExchangeFactory(new PooledExchangeFactory());
```
:::
::::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | ::: paragraph                     |
| Note                              | The `ExtendedCamelContext` is an  |
| :::                               | internal API of Camel and, as     |
|                                   | such, may not offer the same      |
|                                   | level of backward compatibility   |
|                                   | as the public APIs. Whenever      |
|                                   | possible, configure the exchange  |
|                                   | pooling via application           |
|                                   | properties.                       |
|                                   | :::                               |
+-----------------------------------+-----------------------------------+
:::
:::::::

::::::: sect2
### Enabling Exchange Pooling on Camel 4.x {#_enabling_exchange_pooling_on_camel_4_x}

::: paragraph
If you are using a different runtime or if you want to do that
programmatically, you can do so in Camel 4 and newer versions using the
`setExchangeFactory` method from the `ExtendedCamelContext`. Here's an
example of how to do that using Java:
:::

:::: listingblock
::: content
``` highlight
CamelContext context = ...
context.getExchangeExtension().setExchangeFactory(new PooledExchangeFactory());
```
:::
::::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | ::: paragraph                     |
| Note                              | The `ExtendedCamelContext` is an  |
| :::                               | internal API of Camel and, as     |
|                                   | such, may not offer the same      |
|                                   | level of backward compatibility   |
|                                   | as the public APIs. Whenever      |
|                                   | possible, configure the exchange  |
|                                   | pooling via application           |
|                                   | properties.                       |
|                                   | :::                               |
+-----------------------------------+-----------------------------------+
:::
:::::::
::::::::::::::::
:::::::::::::::::

::::: sect1
## Configuration Options {#_configuration_options}

:::: sectionbody
::: paragraph
You can configure exchange pooling with the following options:
:::

+-----------------+-----------------------------------+-----------------+
| Option          | Description                       | Default         |
+=================+===================================+=================+
| e               | Whether to use pooling or not.    | prototype       |
| xchange-factory | Possible values are prototype or  |                 |
|                 | pooled                            |                 |
+-----------------+-----------------------------------+-----------------+
| exchange-f      | Maximum number of elements in the | 100             |
| actory-capacity | pool                              |                 |
+-----------------+-----------------------------------+-----------------+
| excha           | Whether to capture usage          | false           |
| nge-factory-sta | statistics                        |                 |
| tistics-enabled |                                   |                 |
+-----------------+-----------------------------------+-----------------+
::::
:::::

::::: sect1
## Management {#_management}

:::: sectionbody
::: paragraph
If object pooling is enabled, then Camel provides a JMX MBean which
allows us to introspect the pools and their usage via JMX. This requires
adding `camel-management` JAR to the classpath.
:::
::::
:::::

::::: sect1
## Examples {#_examples}

:::: sectionbody
::: paragraph
We have provided a few examples which we are using for performance
profiling. You can check the basic
[timer-log](https://github.com/apache/camel-performance-tests/tree/main/profiling/timer-log)
if you need an example.
:::
::::
:::::
:::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
