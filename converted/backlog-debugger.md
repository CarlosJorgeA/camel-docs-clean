::: {#header}
# BacklogDebugger
:::

:::::::::::::::::: {#content}
::::::: {#preamble}
:::::: sectionbody
::: paragraph
Camel supports a backlog debugger that is used for live debugging of
messages that are routed in Camel. The backlog debugger has additional
functionality for easier debugging aimed at tooling, than the Debugger.
The backlog debugger is exposed in JMX in the tracer node with the name
`BacklogDebugger`. The JMX API is defined in the
`org.apache.camel.api.management.mbean.ManagedBacklogDebuggerMBean`
interface.
:::

::: paragraph
You can enable or disable the BacklogDebugger dynamically, by calling
`enableDebugger` or `disableDebugger` methods.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | This requires to enabled JMX by   |
| Note                              | including `camel-management` JAR  |
| :::                               | in the classpath.                 |
+-----------------------------------+-----------------------------------+
:::
::::::
:::::::

:::: sect1
## Options {#_options}

::: sectionbody
+------+-------------+-------------------------------------------------+
| Op   | Default     | Description                                     |
| tion |             |                                                 |
+======+=============+=================================================+
| `b   | `true`      | Whether to include the message body of file     |
| odyI |             | based messages. The overhead is that the file   |
| nclu |             | content has to be read from the file.           |
| deFi |             |                                                 |
| les` |             |                                                 |
+------+-------------+-------------------------------------------------+
| `bod | `false`     | Whether to include the message body of stream   |
| yInc |             | based messages. If enabled then beware the      |
| lude |             | stream may not be re-readable later. See more   |
| Stre |             | about Stream Caching.                           |
| ams` |             |                                                 |
+------+-------------+-------------------------------------------------+
| `b   | `32kb`      | To limit the message body to a maximum size in  |
| odyM |             | the traced message. Use 0 or negative value to  |
| axCh |             | use unlimited size.                             |
| ars` |             |                                                 |
+------+-------------+-------------------------------------------------+
| `in  | `true`      | Whether to include the exchange properties in   |
| clud |             | the traced message.                             |
| eExc |             |                                                 |
| hang |             |                                                 |
| ePro |             |                                                 |
| pert |             |                                                 |
| ies` |             |                                                 |
+------+-------------+-------------------------------------------------+
| `i   | `true`      | Whether to include the exchange variables in    |
| nclu |             | the traced message.                             |
| deEx |             |                                                 |
| chan |             |                                                 |
| geVa |             |                                                 |
| riab |             |                                                 |
| les` |             |                                                 |
+------+-------------+-------------------------------------------------+
| `    | `false`     | Whether the debugger is standby. If a debugger  |
| stan |             | is in standby then the debugger is activated    |
| dby` |             | during startup and are ready to be enabled      |
|      |             | manually via JMX or calling the enableDebugger  |
|      |             | method.                                         |
+------+-------------+-------------------------------------------------+
| `    | `false`     | Whether the debugger is enabled or not.         |
| enab |             |                                                 |
| led` |             |                                                 |
+------+-------------+-------------------------------------------------+
| `sin | `false`     | Whether currently in single step mode of a      |
| gleS |             | single Exchange.                                |
| tepM |             |                                                 |
| ode` |             |                                                 |
+------+-------------+-------------------------------------------------+
| `sin | `false`     | In single step mode, then when the exchange is  |
| gleS |             | complete, then simulate a breakpoint as last,   |
| tepL |             | that allows to suspend and watch the exchange   |
| ast` |             | when complete (you can see message body as      |
|      |             | response, failed exception etc).                |
+------+-------------+-------------------------------------------------+
:::
::::

:::: sect1
## Operations {#_operations}

::: sectionbody
+------+-------------+-------------------------------------------------+
| Op   | Type        | Description                                     |
| tion |             |                                                 |
+======+=============+=================================================+
| `ad  | `void`      | To add a breakpoint at the given node.          |
| dBre |             |                                                 |
| akpo |             |                                                 |
| int( |             |                                                 |
| node |             |                                                 |
| Id)` |             |                                                 |
+------+-------------+-------------------------------------------------+
| `ad  | `void`      | To add a conditional breakpoint at the given    |
| dCon |             | node. The predicate is created from the         |
| diti |             | language parameter.                             |
| onal |             |                                                 |
| Brea |             |                                                 |
| kpoi |             |                                                 |
| nt(n |             |                                                 |
| odeI |             |                                                 |
| d, l |             |                                                 |
| angu |             |                                                 |
| age, |             |                                                 |
|  pre |             |                                                 |
| dica |             |                                                 |
| te)` |             |                                                 |
+------+-------------+-------------------------------------------------+
| `di  | `void`      | To disable a breakpoint.                        |
| sabl |             |                                                 |
| eBre |             |                                                 |
| akpo |             |                                                 |
| int( |             |                                                 |
| node |             |                                                 |
| Id)` |             |                                                 |
+------+-------------+-------------------------------------------------+
| `    | `void`      | To disable the debugger                         |
| disa |             |                                                 |
| bleD |             |                                                 |
| ebug |             |                                                 |
| ger` |             |                                                 |
+------+-------------+-------------------------------------------------+
| `    | `String`    | To dump the debugged messages from the give     |
| dump |             | node id in XML format.                          |
| Trac |             |                                                 |
| edMe |             |                                                 |
| ssag |             |                                                 |
| esAs |             |                                                 |
| Xml( |             |                                                 |
| node |             |                                                 |
| Id)` |             |                                                 |
+------+-------------+-------------------------------------------------+
| `d   | `String`    | To dump the debugged messages from the give     |
| umpT |             | node id in XML format, optionally including the |
| race |             | exchange properties.                            |
| dMes |             |                                                 |
| sage |             |                                                 |
| sAsX |             |                                                 |
| ml(n |             |                                                 |
| odeI |             |                                                 |
| d, b |             |                                                 |
| oole |             |                                                 |
| an)` |             |                                                 |
+------+-------------+-------------------------------------------------+
| `e   | `void`      | To activate a breakpoint which has been         |
| nabl |             | disabled.                                       |
| eBre |             |                                                 |
| akpo |             |                                                 |
| int( |             |                                                 |
| node |             |                                                 |
| Id)` |             |                                                 |
+------+-------------+-------------------------------------------------+
| `ena | `void`      | To enable the debugger                          |
| bleD |             |                                                 |
| ebug |             |                                                 |
| ger` |             |                                                 |
+------+-------------+-------------------------------------------------+
| `e   | `String`    | To evaluate an expression in any supported      |
| valu |             | language (e.g. Simple, CSimple, etc.) and       |
| ateE |             | convert the result to String                    |
| xpre |             |                                                 |
| ssio |             |                                                 |
| nAtB |             |                                                 |
| reak |             |                                                 |
| poin |             |                                                 |
| t(no |             |                                                 |
| deId |             |                                                 |
| , la |             |                                                 |
| ngua |             |                                                 |
| ge,  |             |                                                 |
| expr |             |                                                 |
| essi |             |                                                 |
| on)` |             |                                                 |
+------+-------------+-------------------------------------------------+
| `e   | `Object`    | To evaluate an expression in any supported      |
| valu |             | language (e.g. Simple, CSimple, etc.) and       |
| ateE |             | return the result as a given result type        |
| xpre |             |                                                 |
| ssio |             |                                                 |
| nAtB |             |                                                 |
| reak |             |                                                 |
| poin |             |                                                 |
| t(no |             |                                                 |
| deId |             |                                                 |
| , la |             |                                                 |
| ngua |             |                                                 |
| ge,  |             |                                                 |
| expr |             |                                                 |
| essi |             |                                                 |
| on,  |             |                                                 |
| resu |             |                                                 |
| ltTy |             |                                                 |
| pe)` |             |                                                 |
+------+-------------+-------------------------------------------------+
| `get | `S          | To get a set of all the nodes which has a       |
| Brea | et<String>` | breakpoint added.                               |
| kpoi |             |                                                 |
| nts` |             |                                                 |
+------+-------------+-------------------------------------------------+
| `get | `long`      | Gets the total number of debugged messages.     |
| Debu |             |                                                 |
| gger |             |                                                 |
| Coun |             |                                                 |
| ter` |             |                                                 |
+------+-------------+-------------------------------------------------+
| `ge  | `S          | To get a set of all the nodes which has         |
| tSus | et<String>` | suspended breakpoints (eg an Exchange at the    |
| pend |             | breakpoint which is suspended).                 |
| edBr |             |                                                 |
| eakp |             |                                                 |
| oint |             |                                                 |
| Node |             |                                                 |
| Ids` |             |                                                 |
+------+-------------+-------------------------------------------------+
| `r   | `void`      | To remove the breakpoint from the given node    |
| emov |             | id.                                             |
| eBre |             |                                                 |
| akpo |             |                                                 |
| int( |             |                                                 |
| node |             |                                                 |
| Id)` |             |                                                 |
+------+-------------+-------------------------------------------------+
| `r   | `void`      | To reset the debugger counter.                  |
| eset |             |                                                 |
| Debu |             |                                                 |
| gger |             |                                                 |
| Coun |             |                                                 |
| ter` |             |                                                 |
+------+-------------+-------------------------------------------------+
| `re  | `void`      | To resume all suspended breakpoints.            |
| sume |             |                                                 |
| All` |             |                                                 |
+------+-------------+-------------------------------------------------+
| `r   | `void`      | To resume a suspend breakpoint, which will then |
| esum |             | continue routing the Exchange.                  |
| eBre |             |                                                 |
| akpo |             |                                                 |
| int( |             |                                                 |
| node |             |                                                 |
| Id)` |             |                                                 |
+------+-------------+-------------------------------------------------+
| `set | `void`      | To update/add the Exchange property on the      |
| Exch |             | suspended Exchange at the node.                 |
| ange |             |                                                 |
| Prop |             |                                                 |
| erty |             |                                                 |
| OnBr |             |                                                 |
| eakp |             |                                                 |
| oint |             |                                                 |
| (nod |             |                                                 |
| eId, |             |                                                 |
| exch |             |                                                 |
| ange |             |                                                 |
| Prop |             |                                                 |
| erty |             |                                                 |
| Name |             |                                                 |
| ,val |             |                                                 |
| ue)` |             |                                                 |
+------+-------------+-------------------------------------------------+
| `se  | `long`      | Fallback Timeout in seconds (300 seconds as     |
| tFal |             | default) when block the message processing in   |
| lbac |             | Camel. A timeout used for waiting for a message |
| kTim |             | to arrive at a given breakpoint. \`             |
| eout |             |                                                 |
| (val |             |                                                 |
| ue)` |             |                                                 |
+------+-------------+-------------------------------------------------+
| `    | `void`      | To update the message body on the suspended     |
| setM |             | Exchange at the node.                           |
| essa |             |                                                 |
| geBo |             |                                                 |
| dyOn |             |                                                 |
| Brea |             |                                                 |
| kpoi |             |                                                 |
| nt(n |             |                                                 |
| odeI |             |                                                 |
| d,bo |             |                                                 |
| dy)` |             |                                                 |
+------+-------------+-------------------------------------------------+
| `se  | `void`      | To update/add the message header on the         |
| tMes |             | suspended Exchange at the node.                 |
| sage |             |                                                 |
| Head |             |                                                 |
| erOn |             |                                                 |
| Brea |             |                                                 |
| kpoi |             |                                                 |
| nt(n |             |                                                 |
| odeI |             |                                                 |
| d,he |             |                                                 |
| ader |             |                                                 |
| Name |             |                                                 |
| ,val |             |                                                 |
| ue)` |             |                                                 |
+------+-------------+-------------------------------------------------+
| `ste | `void`      | To start single step mode from a suspended      |
| pBre |             | breakpoint at the given node. Then invoke       |
| akpo |             | `step` to step to next node in the route.       |
| int( |             |                                                 |
| node |             |                                                 |
| Id)` |             |                                                 |
+------+-------------+-------------------------------------------------+
| `s   | `void`      | To step to next node when in single step mode.  |
| tep` |             |                                                 |
+------+-------------+-------------------------------------------------+
| `va  | `String`    | Used for validating if a given predicate is     |
| lida |             | valid or not. Returns null if valid, otherwise  |
| teCo |             | a string with the error message.                |
| ndit |             |                                                 |
| iona |             |                                                 |
| lBre |             |                                                 |
| akpo |             |                                                 |
| int` |             |                                                 |
+------+-------------+-------------------------------------------------+
| `    | `String`    | Returns message history at the given node Id in |
| mess |             | XML format                                      |
| ageH |             |                                                 |
| isto |             |                                                 |
| ryOn |             |                                                 |
| Brea |             |                                                 |
| kpoi |             |                                                 |
| ntAs |             |                                                 |
| Xml( |             |                                                 |
| node |             |                                                 |
| Id)` |             |                                                 |
+------+-------------+-------------------------------------------------+
:::
::::

::::: sect1
## Enabling {#_enabling}

:::: sectionbody
::: paragraph
You would need to enable backlogger debugger using the JMX API.
:::
::::
:::::

::::: sect1
## See Also {#_see_also}

:::: sectionbody
::: paragraph
See [Debugger](debugger.html)
:::
::::
:::::
::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
