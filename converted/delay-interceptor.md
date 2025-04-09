::: {#header}
# Delayer
:::

::::::::::::::::::::::::::: {#content}
:::::: {#preamble}
::::: sectionbody
::: paragraph
The Delayer is used for slowing processing of messages.
:::

::: paragraph
This allows you to set a fixed amount of delay between each step a
message passes in the route. It can be used to better show how things
are happening nicely and slowly, so you are not bombarded with a zillion
lines of logging output.
:::
:::::
::::::

:::::::::::::::::::::: sect1
## Using Delayer {#_using_delayer}

::::::::::::::::::::: sectionbody
::: paragraph
The delayer can be configured on two levels:
:::

::: ulist
- Camel context: *Globally*

- Route: *Individually per route*
:::

:::::: sect2
### Configuring using XML DSL {#_configuring_using_xml_dsl}

::: paragraph
Set the `delayer` attribute of the `<camelContext>` tag as shown below:
:::

:::: listingblock
::: content
``` highlight
<camelContext id="camel" delayer="500" xmlns="http://activemq.apache.org/camel/schema/spring">
    <route>
        <from uri="direct:start"/>
        <to uri="mock:result"/>
    </route>
</camelContext>
```
:::
::::
::::::

:::::: sect2
### Configuring using Java {#_configuring_using_java}

::: paragraph
You can enable delaying messages by setting the delay value on the
`CamelContext` as shown:
:::

:::: listingblock
::: content
``` highlight
camelContext.setDelayer(200);
```
:::
::::
::::::

:::::::::: sect2
### Configuring on route level {#_configuring_on_route_level}

::: paragraph
You can also configure it on both camel context and per route as you
like. Per route will override the camel context setting.
:::

::: paragraph
For example, the route below is only the first route that has a delayer
with 200 milliseconds.
:::

:::: listingblock
::: content
``` highlight
<camelContext>
   <route delayer="200">
     ...
   </route>

   <route>
     ...
   </route>
</camelContext>
```
:::
::::

::: paragraph
And in Java DSL:
:::

:::: listingblock
::: content
``` highlight
from("direct:start").delayer(200)
    .to("mock:result")
```
:::
::::
::::::::::
:::::::::::::::::::::
::::::::::::::::::::::
:::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
