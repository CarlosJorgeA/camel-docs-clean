::: {#header}
# Try, Catch and Finally
:::

:::::::::::::::::::::::::::::::::::::::: {#content}
:::::::: {#preamble}
::::::: sectionbody
::: paragraph
Camel supports the Java equivalent of try ...​ catch ...​ finally directly
in the DSL. It aims to work like its Java sisters but with more power.
:::

::: paragraph
In Camel, we prefix the keywords with `do` to avoid having same keyword
as Java. So we have:
:::

::: ulist
- `doTry`

- `doCatch`

- `doFinally`

- `end` to end the block in Java DSL
:::

::: paragraph
When using `doTry …​ doCatch …​ doFinally` then the regular Camel [Error
Handler](error-handler.html) is not in use; meaning any `onException` or
the likes does not trigger. The reason is that
`doTry …​ doCatch …​ doFinally` is in fact its own error handler and
mimics how try/catch/finally works in Java.
:::
:::::::
::::::::

::::::::::::::::::::::::::::::::: sect1
## Using doTry ...​ doCatch ...​ doFinally {#_using_dotry_docatch_dofinally}

:::::::::::::::::::::::::::::::: sectionbody
::: paragraph
In the route below we have all of them in action:
:::

:::: listingblock
::: content
``` highlight
from("direct:start")
    .doTry()
        .process(new ProcessorFail())
        .to("mock:result")
    .doCatch(IOException.class, IllegalStateException.class)
        .to("mock:catch")
    .doFinally()
        .to("mock:finally")
    .end();
```
:::
::::

::: paragraph
And in XML DSL
:::

:::: listingblock
::: content
``` highlight
<route>
  <from uri="direct:start"/>
  <doTry>
    <process ref="processorFail"/>
    <to uri="mock:result"/>
    <doCatch>
      <exception>java.io.IOException</exception>
      <exception>java.lang.IllegalStateException</exception>
      <to uri="mock:catch"/>
    </doCatch>
    <doFinally>
       <to uri="mock:finally"/>
    </doFinally>
  </doTry>
</route>
```
:::
::::

::::::::: sect2
### Using onWhen with doCatch {#_using_onwhen_with_docatch}

::: paragraph
You can use [Predicate](predicate.html)s with `doCatch` to make it
runtime determine if the block should be triggered or not. In our case,
we only want to trigger if the caused exception message contains the
**damn** word.
:::

:::: listingblock
::: content
``` highlight
from("direct:start")
    .doTry()
        .process(new ProcessorFail())
        .to("mock:result")
    .doCatch(IOException.class, IllegalStateException.class).onWhen(exceptionMessage().contains("Damn"))
        .to("mock:catch")
    .doCatch(CamelExchangeException.class)
        .to("mock:catchCamel")
    .doFinally()
        .to("mock:finally")
    .end();
```
:::
::::

::: paragraph
And in XML DSL
:::

:::: listingblock
::: content
``` highlight
<route>
  <from uri="direct:start"/>
  <doTry>
    <process ref="processorFail"/>
    <to uri="mock:result"/>
    <doCatch>
      <exception>java.io.IOException</exception>
      <exception>java.lang.IllegalStateException</exception>
      <onWhen>
        <simple>${exception.message} contains 'Damn'</simple>
      </onWhen>
      <to uri="mock:catch"/>
    </doCatch>
    <doCatch>
      <exception>org.apache.camel.CamelExchangeException</exception>
      <to uri="mock:catchCamel"/>
    </doCatch>
    <doFinally>
       <to uri="mock:finally"/>
    </doFinally>
  </doTry>
</route>
```
:::
::::
:::::::::

::::: sect2
### Use end() to end the block {#_use_end_to_end_the_block}

::: paragraph
Notice when using Java DSL we must use `end()` to indicate where the try
...​ catch ...​ finally block ends. As the example above has a finally,
then the `end()` should be at the end of the finally block. If we are
not using a finally, then the `end()` should be at the end of the
`doCatch` to indicate the end there.
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | Instead of `end()` you can use    |
| Tip                               | `endDoTry()` to end and *return   |
| :::                               | back* to the try ...​ catch scope. |
+-----------------------------------+-----------------------------------+
:::
:::::

::::::::::::::: sect2
### Using nested doTry ...​ doCatch EIPs {#_using_nested_dotry_docatch_eips}

::: paragraph
When nesting `doTry …​ doCatch` from an outer `doTry …​ doCatch` EIP, then
pay extra attention when using Java DSL as the Java programming language
is not *indent aware* so you may write Java code that is indented in a
way where you think that a catch block is associated with the other
doTry, but it is not.
:::

::: paragraph
Given the following Java DSL:
:::

:::: listingblock
::: content
``` highlight
from("direct:test").routeId("myroute")
    .doTry().
        doTry().
            throwException(new IllegalArgumentException("Forced by me"))
        .doCatch(Exception.class)
            .log("docatch 1")
            .throwException(new IllegalArgumentException("Second forced by me"))
    .doCatch(Exception.class)
        .log("docatch 2")
    .end();
```
:::
::::

::: paragraph
Then you may think that *`docatch2`* is associated on the outer doTry
because of how the code is formatted. But it is **not**, both
*`docatch1`* and *`docatch2`* are in the inner `doTry`, and the outer
`doTry` has no catch blocks.
:::

::: paragraph
So in this example, the route will throw the first exception which is
then handled in *`docatch1`* which then throws a second exception, that
is not caught.
:::

::: paragraph
So what you must do is to end the doCatch block correct (notice how we
use `endDoTry()` two times) as shown below:
:::

:::: listingblock
::: content
``` highlight
from("direct:test").routeId("myroute")
    .doTry().
        doTry().
            throwException(new IllegalArgumentException("Forced by me"))
        .doCatch(Exception.class)
            .log("docatch 1")
            .throwException(new IllegalArgumentException("Second forced by me"))
         .endDoTry() // end this doCatch block
     .endDoTry() // end the inner doTry
    .doCatch(Exception.class)
        .log("docatch 2")
    .end();
```
:::
::::

::: paragraph
And by using the `endDoTry()` we can end the block correctly, and an XML
representation of the route would be as follows:
:::

:::: listingblock
::: content
``` highlight
<route>
    <from uri="direct:test"/>
    <doTry>
        <doTry>
            <throwException id="throwException1"/>
            <doCatch id="doCatch1">
                <log id="log1" message="docatch 1"/>
                <throwException id="throwException2"/>
            </doCatch>
        </doTry>
        <doCatch id="doCatch2">
            <log id="log2" message="docatch 2"/>
        </doCatch>
    </doTry>
</route>
```
:::
::::
:::::::::::::::
::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
