::: {#header}
# Error Handler
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
:::::: {#preamble}
::::: sectionbody
::: paragraph
Camel supports pluggable
[ErrorHandler](https://www.javadoc.io/doc/org.apache.camel/camel-base/current/org/apache/camel/processor/ErrorHandler.html)
strategies to deal with errors processing an [Event Driven
Consumer](components:eips:eventDrivenConsumer-eip.html).
:::

::: paragraph
An alternative is to specify the error handling directly in the
[DSL](dsl.html) using the [Exception Clause](exception-clause.html).
:::
:::::
::::::

::::: sect1
## Exception Clause {#_exception_clause}

:::: sectionbody
::: paragraph
Using Error Handler combined with Exception Clause is a very powerful
combination. We encourage end-users to use this combination in your
error handling strategies. See samples and Exception Clause.
:::
::::
:::::

:::::: sect1
## Using try ...​ catch ...​ finally {#_using_try_catch_finally}

::::: sectionbody
::: paragraph
Related to error handling is the [Try Catch
Finally](try-catch-finally.html) as DSL you can use directly in your
route. Its basically a mimic of the regular try catch finally in the
Java language but with more power.
:::

::: paragraph
The current implementations Camel provides out of the box are:
:::
:::::
::::::

::::: sect1
## Non transacted {#_non_transacted}

:::: sectionbody
::: ulist
- DefaultErrorHandler is the default error handler in Camel. This error
  handler does not support a dead letter queue, it will propagate
  exceptions back to the caller, as if there were no error handler at
  all. It has a limited set of features.

- Dead Letter Channel which supports attempting to redeliver the message
  exchange a number of times before sending it to a dead letter endpoint

- NoErrorHandler for no error handling
:::
::::
:::::

:::::: sect1
## Transacted {#_transacted}

::::: sectionbody
::: ulist
- TransactionErrorHandler is the default error handler in Camel for
  transacted routes. See the Transactional Client EIP pattern.
:::

::: paragraph
These error handlers can be applied in the DSL to an entire set of rules
or a specific routing rule as we show in the next examples. Error
handling rules are inherited on each routing rule within a single
RouteBuilder
:::
:::::
::::::

::::::::::::::::::::: sect1
## Short Summary of the provided Error Handlers {#_short_summary_of_the_provided_error_handlers}

:::::::::::::::::::: sectionbody
:::: sect2
### DefaultErrorHandler {#_defaulterrorhandler}

::: paragraph
The DefaultErrorHandler is the default error handler in Camel. Unlike
[Dead Letter Channel](components:eips:dead-letter-channel.html) it does
not have any dead letter queue, and do **not** handle exceptions by
default.
:::
::::

::::::: sect2
### Dead Letter Channel {#_dead_letter_channel}

::: paragraph
The Dead Letter Channel will redeliver at most 6 times using 1 second
delay, and if the exchange failed it will be logged at ERROR level.
:::

::: paragraph
You can configure the default dead letter endpoint to use: or in XML
DSL:
:::

:::: listingblock
::: content
``` highlight
<camel:errorHandler id="deadLetterErrorHandler" type="DeadLetterChannel" deadLetterUri="log:dead">

<camel:camelContext errorHandlerRef="deadLetterErrorHandler">
  ...
</camel:camelContext>
```
:::
::::
:::::::

::::::::: sect2
### No Error Handler {#_no_error_handler}

::: paragraph
The no error handler is to be used for disabling error handling.
:::

:::: listingblock
::: content
``` highlight
errorHandler(noErrorHandler());
```
:::
::::

::: paragraph
or in XML DSL:
:::

:::: listingblock
::: content
``` highlight
<camel:errorHandler id="noErrorHandler" type="NoErrorHandler"/>

<camel:camelContext errorHandlerRef="noErrorHandler">
  ...
</camel:camelContext>
```
:::
::::
:::::::::

::::: sect2
### TransactionErrorHandler {#_transactionerrorhandler}

::: paragraph
The TransactionErrorHandler is the default error handler in Camel for
transacted routes.
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | If you have marked a route as     |
| Tip                               | transacted using the              |
| :::                               | **transacted** DSL then Camel     |
|                                   | will automatically use a          |
|                                   | TransactionErrorHandler. It will  |
|                                   | try to lookup the global/per      |
|                                   | route configured error handler    |
|                                   | and use it if it is a             |
|                                   | `TransactionErrorHandlerBuilder`  |
|                                   | instance. If not Camel will       |
|                                   | automatically create a temporary  |
|                                   | TransactionErrorHandler that      |
|                                   | overrules the default error       |
|                                   | handler. This is convention over  |
|                                   | configuration.                    |
+-----------------------------------+-----------------------------------+
:::
:::::
::::::::::::::::::::
:::::::::::::::::::::

:::::: sect1
## Features support by various Error Handlers {#_features_support_by_various_error_handlers}

::::: sectionbody
::: paragraph
Here is a breakdown of which features are supported by the Error
Handler(s):
:::

+-------------+--------------------------------------------------------+
| Feature     | Supported by the following Error Handler               |
+=============+========================================================+
| all scopes  | DefaultErrorHandler, TransactionErrorHandler, Dead     |
|             | Letter Channel                                         |
+-------------+--------------------------------------------------------+
| onException | DefaultErrorHandler, TransactionErrorHandler, Dead     |
|             | Letter Channel                                         |
+-------------+--------------------------------------------------------+
| onWhen      | DefaultErrorHandler, TransactionErrorHandler, Dead     |
|             | Letter Channel                                         |
+-------------+--------------------------------------------------------+
| continued   | DefaultErrorHandler, TransactionErrorHandler, Dead     |
|             | Letter Channel                                         |
+-------------+--------------------------------------------------------+
| handled     | DefaultErrorHandler, TransactionErrorHandler, Dead     |
|             | Letter Channel                                         |
+-------------+--------------------------------------------------------+
| Custom      | DefaultErrorHandler, TransactionErrorHandler, Dead     |
| Exce        | Letter Channel                                         |
| ptionPolicy |                                                        |
+-------------+--------------------------------------------------------+
| useO        | DefaultErrorHandler, TransactionErrorHandler, Dead     |
| riginalBody | Letter Channel                                         |
+-------------+--------------------------------------------------------+
| retryWhile  | DefaultErrorHandler, TransactionErrorHandler, Dead     |
|             | Letter Channel                                         |
+-------------+--------------------------------------------------------+
| o           | DefaultErrorHandler, TransactionErrorHandler, Dead     |
| nRedelivery | Letter Channel                                         |
+-------------+--------------------------------------------------------+
| Redel       | DefaultErrorHandler, TransactionErrorHandler, Dead     |
| iveryPolicy | Letter Channel                                         |
+-------------+--------------------------------------------------------+
| asyncDelaye | DefaultErrorHandler, TransactionErrorHandler, Dead     |
| dRedelivery | Letter Channel                                         |
+-------------+--------------------------------------------------------+
| redeliverWh | DefaultErrorHandler, TransactionErrorHandler, Dead     |
| ileStopping | Letter Channel                                         |
+-------------+--------------------------------------------------------+
| dead letter | Dead Letter Channel                                    |
| queue       |                                                        |
+-------------+--------------------------------------------------------+
| onPre       | DefaultErrorHandler, Dead Letter Channel               |
| pareFailure |                                                        |
+-------------+--------------------------------------------------------+

::: paragraph
See [Exception Clause](exception-clause.html) documentation for
additional documentation of the features above.
:::
:::::
::::::

:::::::::::: sect1
## Scopes {#_scopes}

::::::::::: sectionbody
::: paragraph
The error handler is scoped as either:
:::

::: ulist
- CamelContext - *Globally* in XML or *globally only* within the same
  `RouteBuilder` in Java DSL

- Route - *Individually per route*
:::

::: paragraph
The following example shows how you can register a global error handler
for the `RouteBuilder`:
:::

:::: listingblock
::: content
``` highlight
RouteBuilder builder = new RouteBuilder() {
    public void configure() {
        errorHandler(deadLetterChannel("seda:error"));

        // here is our regular route
        from("seda:a").to("seda:b");
    }
};
```
:::
::::

::: paragraph
The following example shows how you can register a route specific error
handler
:::

:::: listingblock
::: content
``` highlight
RouteBuilder builder = new RouteBuilder() {
    public void configure() {
        // this route is using a nested error handler
        from("seda:a")
            // here we configure the error handler
            .errorHandler(deadLetterChannel("seda:error"))
            // and we continue with the routing here
            .to("seda:b");

        // this route will use the default error handler
        from("seda:b").to("seda:c");
    }
};
```
:::
::::
:::::::::::
::::::::::::

::::::::::::::::::: sect1
## Spring based configuration {#_spring_based_configuration}

:::::::::::::::::: sectionbody
::: paragraph
**Java DSL vs. Spring DSL** The error handler is configured a bit
differently in Java DSL and Spring DSL. Spring DSL relies more on
standard Spring bean configuration whereas Java DSL uses fluent
builders.
:::

::: paragraph
The error handler can be configured as a spring bean and scoped in:
:::

::: ulist
- global (the `<camelContext>` tag)

- per route (the `<route>` tag)

- or per policy (the `<policy>`/`<transacted>` tag)
:::

::: paragraph
The error handler is configured with the `errorHandlerRef` attribute.
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | **Error Handler Hierarchy**\      |
| Tip                               | The error handlers are inherited, |
| :::                               | so if you only have set a global  |
|                                   | error handler, then it is used    |
|                                   | everywhere. But you can override  |
|                                   | this in a route and use another   |
|                                   | error handler.                    |
+-----------------------------------+-----------------------------------+
:::

:::::::::::: sect2
### Spring-based configuration sample {#_spring_based_configuration_sample}

::: paragraph
In this sample, we configure a [Dead Letter
Channel](components:eips:dead-letter-channel.html) on the route that
should redeliver at most 3 times and use a little delay before retrying.
First we configure the reference to **myDeadLetterErrorHandler** using
the `errorHandlerRef` attribute on the `route` tag.
:::

:::: listingblock
::: content
``` highlight
    <camelContext xmlns="http://camel.apache.org/schema/spring">
        <!-- set the errorHandlerRef to our DeadLetterChannel, this applies for this route only -->
        <route errorHandlerRef="myDeadLetterErrorHandler">
            <from uri="direct:in"/>
            <process ref="myFailureProcessor"/>
            <to uri="mock:result"/>
        </route>
    </camelContext>
```
:::
::::

::: paragraph
Then we configure **myDeadLetterErrorHandler** that is our Dead Letter
Channel. This configuration is standard Spring using the bean element.
And finally we have another spring bean for the redelivery policy where
we can configure the options for how many times to redeliver, delays
etc.
:::

:::: listingblock
::: content
``` highlight
    <!-- here we configure our DeadLetterChannel -->
    <bean id="myDeadLetterErrorHandler" class="org.apache.camel.builder.DeadLetterChannelBuilder">
        <!-- exchanges is routed to mock:dead in cased redelivery failed -->
        <property name="deadLetterUri" value="mock:dead"/>
        <!-- reference the redelivery policy to use -->
        <property name="redeliveryPolicy" ref="myRedeliveryPolicyConfig"/>
    </bean>

    <!-- here we set the redelivery settings -->
    <bean id="myRedeliveryPolicyConfig" class="org.apache.camel.processor.errorhandler.RedeliveryPolicy">
        <!-- try redelivery at most 3 times, after that the exchange is dead and its routed to the mock:dead endpoint -->
        <property name="maximumRedeliveries" value="3"/>
        <!-- delay 250ms before redelivery -->
        <property name="redeliveryDelay" value="250"/>
    </bean>
```
:::
::::

::: paragraph
In **Camel 4.6** you can now inline `<errorHandler>` directly in the
routes. The example above can be done as follows:
:::

:::: listingblock
::: content
``` highlight
    <camelContext xmlns="http://camel.apache.org/schema/spring">
        <route>
            <errorHandler>
                <deadLetterChannel deadLetterUri="mock:dead">
                    <redeliveryPolicy maximumRedeliveries="3" redeliveryDelay="250"/>
                </deadLetterChannel>
            </errorHandler>
            <from uri="direct:in"/>
            <process ref="myFailureProcessor"/>
            <to uri="mock:result"/>
        </route>
    </camelContext>
```
:::
::::
::::::::::::
::::::::::::::::::
:::::::::::::::::::

:::::: sect1
## Using the transactional error handler {#_using_the_transactional_error_handler}

::::: sectionbody
::: paragraph
The transactional error handler is based on spring transaction. This
requires the usage of the camel-spring or camel-jta component.
:::

::: paragraph
See [Transactional Client](components:eips:transactional-client.html)
that has many samples for how to use and transactional behavior and
configuration with this error handler.
:::
:::::
::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
