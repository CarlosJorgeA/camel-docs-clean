::: {#header}
# Mapped Diagnostic Context (MDC)
:::

:::::::::::::::::::::: {#content}
::::::: {#preamble}
:::::: sectionbody
::: paragraph
The Mapped Diagnostic Context is a technology used in Java to provide a
set of customized information into each log trace. The major logging
frameworks implements it, and, although it may have certain limitations,
this technology is used to enhance the logging and monitoring of a Java
application (Camel applications included).
:::

::: paragraph
The main limitation of this technology is the fact that it stores values
on a context that is available at thread level. Since Camel is an
application that manages multiple thread, when it deals with
asynchronous calls, the context propagation may not work correctly.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | the framework should generally    |
| Note                              | handle MDC correctly. However,    |
| :::                               | there could be components (eg,    |
|                                   | tracing components) and other     |
|                                   | asynchronous parts of the system  |
|                                   | that still require the            |
|                                   | implementation of the context     |
|                                   | propagation: please report if you |
|                                   | notice anything wrong.            |
+-----------------------------------+-----------------------------------+
:::
::::::
:::::::

::::::: sect1
## How to configure in Camel application {#_how_to_configure_in_camel_application}

:::::: sectionbody
::: paragraph
The first thing you need to do is to enable the
`camel.main.useMdcLogging=true`. This flag will automatically include in
the MDC context the following Exchange information:
:::

::: ulist
- camel.breadcrumbId

- camel.exchangeId

- camel.messageId

- camel.correlationId

- camel.routeId

- camel.stepId

- camel.contextId

- camel.transactionKey
:::

::: paragraph
You can use the above variables for MDC depending on the logging
framework you're using. For example, if you're using log4j2, then, the
variable will be like `%X{camel.exchangeId}`. Other logging frameworks
should have a similar approach, just check its specific documentation.
:::
::::::
:::::::

:::::::: sect1
## User values {#_user_values}

::::::: sectionbody
::: paragraph
If you're using Java DSL you can include any customized information by
adding that using low level MDC API:
:::

:::: listingblock
::: content
``` highlight
        org.slf4j.MDC.put("myKey", "myValue");
```
:::
::::

::: paragraph
Each MDC should be now able to include that information.
:::
:::::::
::::::::

::::: sect1
## Context propagation {#_context_propagation}

:::: sectionbody
::: paragraph
If you're using some asynchronous component, then, you may need to
configure the application to enable the MDC context propagation. For
that reason you need to add the `camel.main.mdcLoggingKeysPattern`
configuration. This configuration will drive the process of copying the
MDC context on the thread that will execute your Exchange
asynchronously.
:::
::::
:::::
::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
