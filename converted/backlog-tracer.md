::: {#header}
# BacklogTracer
:::

:::::::::::::::::::::::::::::::::: {#content}
::::: {#preamble}
:::: sectionbody
::: paragraph
Camel supports a backlog tracer interceptor that is used for capturing a
trace message of each message as they are routed in Camel. The trace
message is stored in a backlog queue, which contains the last N messages
for each node in the routes (by default 10).
:::
::::
:::::

:::::::: sect1
## What is the difference between BacklogTracer and Tracer {#_what_is_the_difference_between_backlogtracer_and_tracer}

::::::: sectionbody
::: paragraph
Camel also provides a [Tracer](tracer.html) which has similar
capabilities as this backlog tracer. The difference is that the backlog
tracer is storing a capture of the message in an internal backlog queue.
:::

::: paragraph
The [Tracer](tracer.html) is event based and logs the messages as they
happen (or route to another Camel destination).
:::

::: paragraph
Use the [Tracer](tracer.html) when all you need is to log the traced
messages as they happen.
:::

::: paragraph
The backlog tracer allows you to pull the messages from the backlog
queues on demand. The backlog tracer works better with JMX capable
tooling as it is simpler, allowing to bulk dump all its traced messages
in either a POJO or XML format.
:::
:::::::
::::::::

:::: sect1
## Options {#_options}

::: sectionbody
+------+------+--------------------------------------------------------+
| Op   | Def  | Description                                            |
| tion | ault |                                                        |
+======+======+========================================================+
| sta  | `fa  | Whether the tracer is standby. If a tracer is in       |
| ndby | lse` | standby then the tracer is activated during startup    |
|      |      | and are ready to be enabled manually via JMX or        |
|      |      | calling the enabled method.                            |
+------+------+--------------------------------------------------------+
| ena  | `fa  | Flag to enable or disable this tracer                  |
| bled | lse` |                                                        |
+------+------+--------------------------------------------------------+
| bac  | `    | Maximum number of total traced messages to keep in the |
| klog | 100` | backlog (FIFO queue). Should be between 1 - 1000.      |
| Size |      |                                                        |
+------+------+--------------------------------------------------------+
| trac | `n   | Allows to filter tracing using a pattern that matches  |
| ePat | ull` | against the node id and route id. For example use      |
| tern |      | `"to1,to2"` to match only nodes with either the name   |
|      |      | \"to1\", or \"to2\". You can use \* for wildcards. So  |
|      |      | you can do \"to\*\" to match any to. Or use            |
|      |      | \"route-foo\*\" to match any foo routes.               |
+------+------+--------------------------------------------------------+
| tra  | `n   | Allow to configure a filter as a                       |
| ceFi | ull` | [Predicate](predicate.html) using any of the Camel     |
| lter |      | [languages](languages.html). But default the           |
|      |      | [Simple](components:languages:simple-language.html)    |
|      |      | language is used. For example to filter on messages    |
|      |      | with a given header, use `${header.foo} != null`. To   |
|      |      | use                                                    |
|      |      | [Groovy](components:languages:groovy-language.html)    |
|      |      | then prefix the value with \"groovy:\". And similar    |
|      |      | for the other languages.                               |
+------+------+--------------------------------------------------------+
| tr   | `fa  | Whether tracing should trace inner details from Rest   |
| aceR | lse` | DSL. Turning this on increases the verbosity of        |
| ests |      | tracing by including events from internal routes by    |
|      |      | Rest DSL.                                              |
+------+------+--------------------------------------------------------+
| tr   | `fa  | Whether tracing should trace inner details from route  |
| aceT | lse` | templates (or kamelets). Turning this on increases the |
| empl |      | verbosity of tracing by including events from internal |
| ates |      | routes in the templates or kamelets.                   |
+------+------+--------------------------------------------------------+
| remo | `t   | Whether to remove the traced messages that was         |
| veOn | rue` | returned when invoking the dump methods.               |
| Dump |      |                                                        |
+------+------+--------------------------------------------------------+
| body | `3   | To limit the message body to a maximum size in the     |
| MaxC | 2kb` | traced message. Use 0 or negative value to use         |
| hars |      | unlimited size.                                        |
+------+------+--------------------------------------------------------+
| bo   | `fa  | Whether to include the message body of stream based    |
| dyIn | lse` | messages. If enabled then beware the stream may not be |
| clud |      | re-readable later. See more about [Stream              |
| eStr |      | Caching](stream-caching.html).                         |
| eams |      |                                                        |
+------+------+--------------------------------------------------------+
| body | `t   | Whether to include the message body of file based      |
| Incl | rue` | messages. The overhead is that the file content has to |
| udeF |      | be read from the file.                                 |
| iles |      |                                                        |
+------+------+--------------------------------------------------------+
| i    | `t   | Trace messages to include exchange properties.         |
| nclu | rue` |                                                        |
| deEx |      |                                                        |
| chan |      |                                                        |
| gePr |      |                                                        |
| oper |      |                                                        |
| ties |      |                                                        |
+------+------+--------------------------------------------------------+
| incl | `t   | Trace messages to include exchange variables.          |
| udeE | rue` |                                                        |
| xcha |      |                                                        |
| ngeV |      |                                                        |
| aria |      |                                                        |
| bles |      |                                                        |
+------+------+--------------------------------------------------------+
| incl | `t   | Trace messages to include exception if the message     |
| udeE | rue` | failed.                                                |
| xcep |      |                                                        |
| tion |      |                                                        |
+------+------+--------------------------------------------------------+
:::
::::

:::: sect1
## Operations {#BacklogTracer-Operations}

::: sectionbody
+------+-------------+-------------------------------------------------+
| Op   | Default     | Description                                     |
| tion |             |                                                 |
+======+=============+=================================================+
| get  | `long`      | Gets the total number of traced messages.       |
| Trac |             |                                                 |
| eCou |             |                                                 |
| nter |             |                                                 |
+------+-------------+-------------------------------------------------+
| getQ | `long`      | Number of traced messages in the backlog.       |
| ueue |             |                                                 |
| Size |             |                                                 |
+------+-------------+-------------------------------------------------+
| r    | `void`      | To reset the trace counter.                     |
| eset |             |                                                 |
| Trac |             |                                                 |
| eCou |             |                                                 |
| nter |             |                                                 |
+------+-------------+-------------------------------------------------+
| d    | `List<Backl | To dump the traced messages from the give node  |
| umpT | ogTracerEve | or route id.                                    |
| race | ntMessage>` |                                                 |
| dMes |             |                                                 |
| sage |             |                                                 |
| s(no |             |                                                 |
| deOr |             |                                                 |
| Rout |             |                                                 |
| eId) |             |                                                 |
+------+-------------+-------------------------------------------------+
| du   | `String`    | To dump the traced messages from the give node  |
| mpTr |             | or route id in XML format.                      |
| aced |             |                                                 |
| Mess |             |                                                 |
| ages |             |                                                 |
| AsXm |             |                                                 |
| l(no |             |                                                 |
| deOr |             |                                                 |
| Rout |             |                                                 |
| eId) |             |                                                 |
+------+-------------+-------------------------------------------------+
| dum  | `String`    | To dump the traced messages from the give node  |
| pTra |             | or route id in JSon format.                     |
| cedM |             |                                                 |
| essa |             |                                                 |
| gesA |             |                                                 |
| sJSo |             |                                                 |
| n(no |             |                                                 |
| deOr |             |                                                 |
| Rout |             |                                                 |
| eId) |             |                                                 |
+------+-------------+-------------------------------------------------+
| d    | `List<Backl | To dump all the traced messages                 |
| umpA | ogTracerEve |                                                 |
| llTr | ntMessage>` |                                                 |
| aced |             |                                                 |
| Mess |             |                                                 |
| ages |             |                                                 |
+------+-------------+-------------------------------------------------+
| du   | `String`    | To dump all the traced messages in XML format.  |
| mpAl |             |                                                 |
| lTra |             |                                                 |
| cedM |             |                                                 |
| essa |             |                                                 |
| gesA |             |                                                 |
| sXml |             |                                                 |
+------+-------------+-------------------------------------------------+
| dum  | `String`    | To dump all the traced messages in JSon format. |
| pAll |             |                                                 |
| Trac |             |                                                 |
| edMe |             |                                                 |
| ssag |             |                                                 |
| esAs |             |                                                 |
| JSon |             |                                                 |
+------+-------------+-------------------------------------------------+
:::
::::

:::::::::::::::::::: sect1
## Enabling {#_enabling}

::::::::::::::::::: sectionbody
::: paragraph
You can turn off backlog tracing on `CamelContext`
:::

:::: listingblock
::: content
``` highlight
camelContext.setBacklogTracing(true);
```
:::
::::

::: paragraph
And in Spring XML
:::

:::: listingblock
::: content
``` highlight
<camelContext backlogTrace="true">
  ...
</camelContext>
```
:::
::::

::: paragraph
And in Camel Main you can enable this in the `application.properties`
file:
:::

:::: listingblock
::: content
``` highlight
camel.main.backlog-tracing = true
```
:::
::::

::: paragraph
And in Spring Boot you can enable this in the `application.properties`
file:
:::

:::: listingblock
::: content
``` highlight
camel.springboot.backlog-tracing = true
```
:::
::::

:::::: sect2
### Tracing payloads of InputStream types {#_tracing_payloads_of_inputstream_types}

::: paragraph
Beware that when enabling backlog tracing, and the message payloads is
streaming types (such as `java.io.InputStream`), then the backlog tracer
will read the payload and make a copy as a *trace* that are stored in
the backlog tracer. Then monitoring tooling is able to view these
*traced* events.
:::

::: paragraph
When working with `InputStream` types then Camel has
[stream-caching.html](stream-caching.html) that is able to automatic
make such types *safe* to use as they are cached and able to be
*re-read*. See more details at
[stream-caching.html](stream-caching.html).
:::

::: paragraph
However, Camel's stream caching is **ONLY** for message body. Having
message headers of type `InputStream` is discouraged and not common use.
If you add custom message headers, then it is recommended to **NOT** use
streaming types, but convert these headers into `String` or `byte[]` or
other standard Java types that are in-memory and *safe* to re-read.
:::
::::::
:::::::::::::::::::
::::::::::::::::::::
::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
