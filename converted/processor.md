::: {#header}
# Processor
:::

::::::::::::::::::::::::::::::::::::::::::::: {#content}
::::: {#preamble}
:::: sectionbody
::: paragraph
The
[Processor](https://www.javadoc.io/doc/org.apache.camel/camel-api/current/org/apache/camel/Processor.html)
interface is used to implement consumers of message exchanges or to
implement a [Message
Translator](components:eips:message-translator.html), and other
use-cases.
:::
::::
:::::

:::::::::::::::::::::::::::::::: sect1
## Using a processor in a route {#_using_a_processor_in_a_route}

::::::::::::::::::::::::::::::: sectionbody
::: paragraph
Once you have written a class which implements processor like this:
:::

:::: listingblock
::: content
``` highlight
public class MyProcessor implements Processor {

    public void process(Exchange exchange) throws Exception {
        // do something...
    }

}
```
:::
::::

::: paragraph
Then you can easily call this processor from a Java such as:
:::

:::: listingblock
::: content
``` highlight
from("activemq:myQueue").process(new MyProcessor());
```
:::
::::

::: paragraph
Notice that the processor is referred to by the class type
`MyProcessor.class` in the route. Camel will during startup automatic
create one new instance of the processor using [Injector](injector.html)
to be used during routing messages.
:::

::: paragraph
In XML DSL however the `<process`\> tag requires, referring to an
existing processor instance which can be done:
:::

::: paragraph
You can then easily use this inside a route by declaring the bean in
Spring, say via the XML:
:::

:::: listingblock
::: content
``` highlight
<bean id="myProcessor" class="com.acme.MyProcessor"/>
```
:::
::::

::: paragraph
And then use the bean id in the Camel route:
:::

:::: listingblock
::: content
``` highlight
<route>
  <from uri="activemq:myQueue"/>
  <process ref="myProcessor"/>
</route>
```
:::
::::

::: paragraph
And in Java DSL:
:::

:::: listingblock
::: content
``` highlight
from("activemq:myQueue").process("myProcessor");
```
:::
::::

::::::::::::: sect2
### Referring to beans using #class syntax {#_referring_to_beans_using_class_syntax}

::: paragraph
In XML DSL you can also refer to the processor by its class name using
`#class:` as prefix as shown:
:::

:::: listingblock
::: content
``` highlight
<route>
  <from uri="activemq:myQueue"/>
  <process ref="#class:com.acme.MyProcessor"/>
</route>
```
:::
::::

::: paragraph
This also works in Java DSL:
:::

:::: listingblock
::: content
``` highlight
from("activemq:myQueue").process("#class:com.acme.MyProcessor");
```
:::
::::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | For more details about the        |
| Note                              | `#class:` prefix (and others)     |
| :::                               | then see [Property                |
|                                   | Binding](property-binding.html).  |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
However in Java DSL you would often use the type safe way and
instantiate the Processor directly as previously shown:
:::

:::: listingblock
::: content
``` highlight
from("activemq:myQueue").process(new MyProcessor());
```
:::
::::
:::::::::::::
:::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::

:::::::: sect1
## Why use process when you can use to instead? {#_why_use_process_when_you_can_use_to_instead}

::::::: sectionbody
::: paragraph
The process can be used in routes as an anonymous inner class such:
:::

:::: listingblock
::: content
``` highlight
    from("activemq:myQueue").process(new Processor() {
        public void process(Exchange exchange) throws Exception {
            String payload = exchange.getIn().getBody(String.class);
            // do something with the payload and/or exchange here
           exchange.getIn().setBody("Changed body");
       }
    }).to("activemq:myOtherQueue");
```
:::
::::

::: paragraph
This is usable for quickly whirling up some code. If the code in the
inner class gets a bit more complicated it is of course advised to
refactor it into a separate class. This approach is better if you do not
want to use this processor again. From reusability perspective, it is
not recommended to use this approach with anonymous inner classes.
:::
:::::::
::::::::

::::: sect1
## See Also {#_see_also}

:::: sectionbody
::: ulist
- [Consumer Template](manual::consumertemplate.html): to learn how to
  consume data (including within processors)

- [Producer Template](manual::producertemplate.html): to learn how to
  produce data (including within processors)
:::
::::
:::::
:::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
