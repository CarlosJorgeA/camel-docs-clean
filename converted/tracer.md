::: {#header}
# Tracer
:::

::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
:::::: {#preamble}
::::: sectionbody
::: paragraph
Camel's tracer is used for logging message details during routing, where
you can see the route path of each message as they happen. Details of
the message is also logged such as the message body, and headers.
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | There is an alternative tracer    |
| Tip                               | that captures the messages in a   |
| :::                               | [Backlog                          |
|                                   | Tracer](backlog-tracer.html).     |
+-----------------------------------+-----------------------------------+
:::
:::::
::::::

:::::::::::::::::::::::::::::::::::::::::::::: sect1
## Enabling Tracing {#_enabling_tracing}

::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
In Java you set tracing on `CamelContext`:
:::

:::: listingblock
::: content
``` highlight
context.setTracing(true);
```
:::
::::

::: paragraph
And in XML DSL:
:::

:::: listingblock
::: content
``` highlight
<camelContext trace="true" xmlns="http://activemq.apache.org/camel/schema/spring">
...
</camelContext>
```
:::
::::

::: paragraph
And in Spring Boot
:::

:::: listingblock
::: content
``` highlight
camel.springboot.tracing = true
```
:::
::::

:::::::::::::::: sect2
### Setting Tracing in Standby mode {#_setting_tracing_in_standby_mode}

::: paragraph
By default Camel optimizes and opt-out tracing. Therefore, you would
either have to enable tracing from the startup, or turn on standby mode,
to allow tracing to be enabled later during runtime.
:::

::: paragraph
To set tracing in standby mode you can do:
:::

:::: listingblock
::: content
``` highlight
context.setTracingStandby(true);
```
:::
::::

::: paragraph
And in XML DSL:
:::

:::: listingblock
::: content
``` highlight
<camelContext trace="standby" xmlns="http://activemq.apache.org/camel/schema/spring">
...
</camelContext>
```
:::
::::

::: paragraph
And in Spring Boot
:::

:::: listingblock
::: content
``` highlight
camel.springboot.tracing-standby = true
```
:::
::::

::: paragraph
If tracer is in standby mode, then tracing is made available, and can be
enabled during runtime. This requires to either use JMX or enable via
Java code:
:::

:::: listingblock
::: content
``` highlight
Tracer tracer = context.getTracer();
tracer.setEnabled(true);
```
:::
::::
::::::::::::::::

::::::::::::::::::::: sect2
### Trace Logging Formatting {#_trace_logging_formatting}

::: paragraph
The tracer formats the execution of exchanges to log lines. They are
logged at `INFO` level in the log category: `org.apache.camel.Tracing`.
:::

::: paragraph
The message information from the Exchange is formatted using
`ExchangeFormatter` and the default implementation has many options you
can configure accordingly to the
[javadoc](https://www.javadoc.io/doc/org.apache.camel/camel-support/latest/org/apache/camel/support/processor/DefaultExchangeFormatter.html).
:::

::: paragraph
The tracer outputs the logging with a prefix with the following
information:
:::

::: ulist
- arrow - (direction whether input or output)

- routeId - the current route

- label - the current EIP node
:::

::: paragraph
This output is assembled using the following default format:
:::

::: ulist
- %-4.4s \[%-12.12s\] \[%-33.33s\]
:::

::: paragraph
The default format can be customized using, for exameple to use wider
columns:
:::

:::: listingblock
::: content
``` highlight
context.setTracingLoggingFormat("%-4.4s [%-30.30s] [%-50.50s]");
```
:::
::::

::: paragraph
And in XML DSL:
:::

:::: listingblock
::: content
``` highlight
<camelContext trace="true" traceLoggingFormat="%-4.4s [%-30.30s] [%-50.50s]">
...
</camelContext>
```
:::
::::

::: paragraph
And in Spring Boot
:::

:::: listingblock
::: content
``` highlight
camel.springboot.tracing-logging-format = %-4.4s [%-30.30s] [%-50.50s]
```
:::
::::

::: paragraph
And in Camel Main / Quarkus
:::

:::: listingblock
::: content
``` highlight
camel.main.tracing-logging-format = %-4.4s [%-30.30s] [%-50.50s]
```
:::
::::
:::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
