::: {#header}
# Exception Clause
:::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
:::::::::::: {#preamble}
::::::::::: sectionbody
::: paragraph
You can use the *Exception Clause* in the Java [DSL](dsl.html) to
specify the error handling you require on a per exception type basis
using the **`onException()`** method. To get started we give quick
sample before digging into how it works.
:::

::: paragraph
For example if you want to perform a specific piece of processing if a
certain exception is raised you can do this simply via:
:::

:::: listingblock
::: content
``` highlight
onException(ValidationException.class)
    .to("activemq:validationFailed");

from("seda:inputA")
    .to("validation:foo/bar.xsd", "activemq:someQueue");

from("seda:inputB")
    .to("direct:foo")
    .to("rnc:mySchema.rnc", "activemq:anotherQueue");
```
:::
::::

::: paragraph
Here if the processing of **`seda:inputA`** or **`seda:inputB`** cause a
**`ValidationException`** to be thrown (such as due to the XSD
validation of the [Validation](components::validator-component.html)
component), then the message will be sent to the
**`activemq:validationFailed`** queue.
:::

::: paragraph
You can define multiple **`onException`** clauses for different
behavior:
:::

:::: listingblock
::: content
``` highlight
onException(ValidationException.class)
    .to("activemq:validationFailed");

onException(ShipOrderException.class)
    .to("activemq:shipFailed");

from("seda:order")
    .to("bean:processOrder");
```
:::
::::
:::::::::::
::::::::::::

:::::::: sect1
## Scopes {#_scopes}

::::::: sectionbody
::: paragraph
Exception clauses is scoped as either:
:::

::: ulist
- global (for Java DSL that is per **`RouteBuilder`** instances, to
  reuse, see note below)

- or route specific
:::

::: paragraph
Where the **global** are the simplest and most easy to understand. In
the advanced section we dig into the route specific and even combining
them. However
:::

::: paragraph
Global scope for Java DSL is per **`RouteBuilder`** instance, so if you
want to share among multiple **`RouteBuilder`** classes, then create a
base abstract **`RouteBuilder`** class and put the error handling logic
in its **`configure`** method. And then extend this class, and make sure
to class **`super.configure()`**. We are just using the Java inheritance
technique.
:::
:::::::
::::::::

:::::::::::::::::::::::::: sect1
## How Does Camel Select Which Clause Should Handle a Given Thrown Exception? {#_how_does_camel_select_which_clause_should_handle_a_given_thrown_exception}

::::::::::::::::::::::::: sectionbody
::: paragraph
Camel uses **`DefaultExceptionPolicyStrategy`** to determine a strategy
how an exception being thrown should be handled by which
**`onException`** clause. The strategy is:
:::

::: ulist
- the order in which the **`onException`** is configured takes
  precedence. Camel will test from first...​last defined.

- Camel will start from the bottom (nested caused by) and recursive up
  in the exception hierarchy to find the first matching
  **`onException`** clause.

- **`instanceof`** test is used for testing the given exception with the
  **`onException`** clause defined exception list. An exact
  **`instanceof`** match will always be used, otherwise the
  **`onException`** clause that has an exception that is the closets
  super of the thrown exception is selected (recurring up the exception
  hierarchy).
:::

::: paragraph
This is best illustrated with an exception:
:::

:::: listingblock
::: content
``` highlight
onException(IOException.class)
    .maximumRedeliveries(3);

onException(OrderFailedException.class)
    .maximumRedeliveries(2);
```
:::
::::

::: paragraph
In the sample above we have defined two exceptions in which
**`IOException`** is first, so Camel will pickup this exception if there
is a match. **`IOException`** that is more general is selected then.
:::

::: paragraph
So if an exception is thrown with this hierarchy:
:::

:::: literalblock
::: content
    + RuntimeCamelException (wrapper exception by Camel)
        + OrderFailedException
            + IOException
                + FileNotFoundException
:::
::::

::: paragraph
Then Camel will try testing the exception in this order:
**`FileNotFoundException`**, **`IOException`**,
**`OrderFailedException`** and **`RuntimeCamelException`**. As we have
defined a **`onException(IOException.class)`** Camel will select this as
it's the **closest** match.
:::

::: paragraph
If we add a third **`onException`** clause with the
**`FileNotFoundException`**
:::

:::: listingblock
::: content
``` highlight
onException(IOException.class)
    .maximumRedeliveries(3);

onException(OrderFailedException.class)
    .maximumRedeliveries(2);

onException(FileNotFoundException.class)
    .handled(true)
    .to("log:nofile");
```
:::
::::

::: paragraph
Then with the previous example Camel will now use the last
**`onException(FileNotFoundException.class)`** as its an **exact**
match. Since this is an exact match it will override the general
**`IOException`** that was used before to handle the same exception
thrown.
:::

::: paragraph
Now a new situation if this exception was thrown instead:
:::

:::: literalblock
::: content
    + RuntimeCamelException (wrapper exception by Camel)
        + OrderFailedException
            + OrderNotFoundException
:::
::::

::: paragraph
Then the **`onException(OrderFailedException.class)`** will be
selected - no surprise here.
:::

::: paragraph
And this last sample demonstrates the **`instanceof`** test aspect in
which Camel will select an exception if it's an instance of the defined
exception in the **`onException`** clause. Illustrated as:
:::

:::: literalblock
::: content
    + RuntimeCamelException (wrapper exception by Camel)
        + SocketException
:::
::::

::: paragraph
Since **`SocketException`** is an **`instanceof IOException`**, Camel
will select the **`onException(IOException.class)`** clause.
:::
:::::::::::::::::::::::::
::::::::::::::::::::::::::

:::::::::::::::::: sect1
## Configuring RedeliveryPolicy (redeliver options) {#_configuring_redeliverypolicy_redeliver_options}

::::::::::::::::: sectionbody
::: paragraph
[RedeliveryPolicy](https://www.javadoc.io/doc/org.apache.camel/camel-base/current/org/apache/camel/processor/errorhandler/RedeliveryPolicy.html)
requires to use the [Dead Letter
Channel](components:eips:dead-letter-channel.html) as the [Error
Handler](error-handler.html). Dead Letter Channel supports attempting to
redeliver the message exchange a number of times before sending it to a
dead letter endpoint. See [Dead Letter
Channel](components:eips:dead-letter-channel.html) for further
information about redeliver and which redeliver options exists.
:::

::::::::::::::: sect2
### No redelivery is default for onException {#_no_redelivery_is_default_for_onexception}

::: paragraph
By default, any [Exception Clause](#) will **not** redeliver! (as it
sets the `maximumRedeliveries` option to 0).
:::

::: paragraph
Sometimes you want to configure the redelivery policy on a per exception
type basis. By default in the top examples, if an
**`org.apache.camel.ValidationException`** occurs then the message will
not be redelivered; however if some other exception occurs, e.g.,
**`IOException`** or whatever, the route will be retried according to
the settings from the [Dead Letter
Channel](components:eips:dead-letter-channel.html).
:::

::: paragraph
However if you want to customize any methods on the
[RedeliveryPolicy](https://www.javadoc.io/doc/org.apache.camel/camel-base/current/org/apache/camel/processor/errorhandler/RedeliveryPolicy.html)
object, you can do this via the fluent API. So lets retry in case of
**`org.apache.camel.ValidationException`** up till two times.
:::

::: paragraph
**Java DSL**:
:::

:::: listingblock
::: content
``` highlight
onException(ValidationException.class)
    .maximumRedeliveries(2);
```
:::
::::

::: paragraph
**XML DSL**:
:::

:::: listingblock
::: content
``` highlight
<onException>
    <exception>com.mycompany.ValidationException</exception>
    <redeliveryPolicy maximumRedeliveries="2"/>
</onException>
```
:::
::::

::: paragraph
You can customize any of the
[RedeliveryPolicy](https://www.javadoc.io/doc/org.apache.camel/camel-base/current/org/apache/camel/processor/errorhandler/RedeliveryPolicy.html)
so we can for instance set a different delay of **`5000`** millis:
:::

:::: listingblock
::: content
``` highlight
<onException>
    <exception>com.mycompany.ValidationException</exception>
    <redeliveryPolicy maximumRedeliveries="2" delay="5000"/>
</onException>
```
:::
::::
:::::::::::::::
:::::::::::::::::
::::::::::::::::::

:::::::: sect1
## Point of Entry for Redelivery Attempts {#_point_of_entry_for_redelivery_attempts}

::::::: sectionbody
::: paragraph
All redelivery attempts start at the point of the failure. So the route:
:::

:::: listingblock
::: content
``` highlight
onException(ConnectException.class)
    .from("direct:start")
    .process("processor1")
    .process("processor2") // <--- throws a ConnectException
    .to("mock:theEnd")
```
:::
::::

::: paragraph
Will retry from **`processor2`** - not the complete route.
:::
:::::::
::::::::

::::::: sect1
## Reusing RedeliveryPolicy {#_reusing_redeliverypolicy}

:::::: sectionbody
::: paragraph
You can reference a **`RedeliveryPolicy`** so you can reuse existing
configurations and use standard spring bean style configuration that
supports property placeholders.
:::

:::: listingblock
::: content
``` highlight
<bean id="myRedeliveryPolicy" class="org.apache.camel.processor.RedeliveryPolicy">
    <property name="maximumRedeliveries" value="${myprop.max}"/>
</bean>

<!-- here we reference our redelivery policy defined above -->
<onException redeliveryPolicyRef="myRedeliveryPolicy">
    <!-- you can define multiple exceptions just adding more exception elements as show below -->
    <exception>com.mycompany.MyFirstException</exception>
    <exception>com.mycompany.MySecondException</exception>
</onException>
```
:::
::::
::::::
:::::::

:::::: sect1
## Asynchronous Delayed Redelivery {#_asynchronous_delayed_redelivery}

::::: sectionbody
::: paragraph
Camel has a feature to *not block* while waiting for a delayed
redelivery to occur. However if you use transacted routes then Camel
will block as its mandated by the transaction manager to execute all the
work in the same thread context. You can enable the non blocking
asynchronous behavior by the **`asyncDelayedRedelivery`** option. This
option can be set on the **`errorHandler`**, **`onException`** or the
redelivery policies.
:::

::: paragraph
By default, the error handler will create and use a scheduled thread
pool to trigger redelivery in the future. You can also configure the
**`executorServiceRef`** on the [Error Handler](error-handler.html) to
indicate a reference to either a shared thread pool you can enlist in
the registry, or a thread pool profile in case you want to be able to
control pool settings.
:::
:::::
::::::

:::::::::: sect1
## Catching Multiple Exceptions {#_catching_multiple_exceptions}

::::::::: sectionbody
::: paragraph
Multiple exception can be caught as shown:
:::

:::: listingblock
::: content
``` highlight
onException(MyBusinessException.class, MyOtherBusinessException.class)
    .maximumRedeliveries(2)
    .to("activemq:businessFailed");
```
:::
::::

::: paragraph
And in XML DSL you just add another exception element:
:::

:::: listingblock
::: content
``` highlight
<onException>
    <exception>com.mycompany.MyBusinessException</exception>
    <exception>com.mycompany.MyOtherBusinessException</exception>
    <redeliveryPolicy maximumRedeliveries="2"/>
    <to uri="activemq:businessFailed"/>
</onException>
```
:::
::::
:::::::::
::::::::::

::::::::::: sect1
## Using a Processor as a Failure Handler {#_using_a_processor_as_a_failure_handler}

:::::::::: sectionbody
::: paragraph
We want to handle certain exceptions in a specific way, so we add a
**`onException`** clause for the particular exception.
:::

:::: listingblock
::: content
``` highlight
// here we register exception cause for MyFunctionException
// when this exception occurs we want it to be processed by our
// processor
onException(MyFunctionalException.class)
  .process(new MyFunctionFailureHandler())
  .stop();
```
:::
::::

::: paragraph
So what happens is that whenever a **`MyFunctionalException`** is thrown
it is being routed to our processor **`MyFunctionFailureHandler`**. So
you can say that the exchange is diverted when a
**`MyFunctionalException`** is thrown during processing. It's important
to distinct this as perfectly valid. The default redelivery policy from
the [Dead Letter Channel](components:eips:dead-letter-channel.html) will
not kick in, so our processor receives the Exchange directly, without
any redeliver attempted. In our processor we need to determine what to
do. Camel regards the Exchange as **failure handled**. So our processor
is the end of the route. So lets look the code for our processor.
:::

:::: listingblock
::: content
``` highlight
    public static class MyFunctionFailureHandler implements Processor {

        @Override
        public void process(Exchange exchange) throws Exception {
            // the caused by exception is stored in a property on the exchange
            Throwable caused = exchange.getProperty(Exchange.EXCEPTION_CAUGHT, Throwable.class);
            assertNotNull(caused);
            // here you can do what you want, but Camel regards this exception as
            // handled, and this processor as a failure handler, so it won't do redeliveries.
            // So this is the end of this route.
        }
    }
```
:::
::::

::: paragraph
Notice how we get the **caused by** exception using a property on the
Exchange. This is where Camel stores any caught exception during
processing. So you can fetch this property and check what the exception
message and do what you want.
:::
::::::::::
:::::::::::

:::::::::: sect1
## Marking Exceptions as Handled {#_marking_exceptions_as_handled}

::::::::: sectionbody
::: paragraph
See also the section [Handle and Continue
Exceptions](#_handle_and_continue_exceptions) below.
:::

::: paragraph
Using **`onException`** to handle known exceptions is a very powerful
feature in Camel. You can mark the exception as being handled with the
**handle** DSL, so the caller will not receive the caused exception as a
response. The handle is a [Predicate](predicate.html) that is overloaded
to accept three types of parameters:
:::

::: ulist
- Boolean

- [Predicate](predicate.html)

- [Expression](expression.html) that will be evaluated as a
  [Predicate](predicate.html) using this rule set: If the expression
  returns a Boolean, it is used directly. For any other response, it is
  regarded as `true` if the response is `not null`.
:::

::: paragraph
For instance to mark all **`ValidationException`** as being handled we
can do this:
:::

:::: listingblock
::: content
``` highlight
onException(ValidationException)
    .handled(true);
```
:::
::::
:::::::::
::::::::::

:::::::::::::::: sect1
## Example Using Handled {#_example_using_handled}

::::::::::::::: sectionbody
::: paragraph
In this route below we want to do special handling of all
**`OrderFailedException`** as we want to return a customized response to
the caller. First we setup our routing as:
:::

:::: listingblock
::: content
``` highlight
    // we do special error handling for when OrderFailedException is
    // thrown
    onException(OrderFailedException.class)
        // we mark the exchange as handled so the caller doesn't
        // receive the
        // OrderFailedException but whatever we want to return
        // instead
        .handled(true)
        // this bean handles the error handling where we can
        // customize the error
        // response using java code
        .bean(OrderService.class, "orderFailed")
        // and since this is an unit test we use mocks for testing
        .to("mock:error");

    // this is just the generic error handler where we set the
    // destination
    // and the number of redeliveries we want to try
    errorHandler(deadLetterChannel("mock:error").maximumRedeliveries(1));

    // this is our route where we handle orders
    from("direct:start")
        // this bean is our order service
        .bean(OrderService.class, "handleOrder")
        // this is the destination if the order is OK
        .to("mock:result");
```
:::
::::

::: paragraph
Then we have our service bean that is just a plain POJO demonstrating
how you can use [Bean Integration](bean-integration.html) in Camel to
avoid being tied to the Camel API:
:::

:::: listingblock
::: content
``` highlight
    /**
     * Order service as a plain POJO class
     */
    public static class OrderService {

        /**
         * This method handle our order input and return the order
         */
        public Object handleOrder(@Headers Map headers, @Body String payload) throws OrderFailedException {
            headers.put("customerid", headers.get("customerid"));
            if ("Order: kaboom".equals(payload)) {
                throw new OrderFailedException("Cannot order: kaboom");
            } else {
                headers.put("orderid", "123");
                return "Order OK";
            }
        }

        /**
         * This method creates the response to the caller if the order could not
         * be processed
         */
        public Object orderFailed(@Headers Map headers, @Body String payload) {
            headers.put("customerid", headers.get("customerid"));
            headers.put("orderid", "failed");
            return "Order ERROR";
        }
    }
```
:::
::::

::: paragraph
And finally the exception that is being thrown is just a regular
exception:
:::

:::: listingblock
::: content
``` highlight
    public static class OrderFailedException extends Exception {

        private static final long serialVersionUID = 1L;

        public OrderFailedException(String message) {
            super(message);
        }

    }
```
:::
::::

::: paragraph
So what happens?
:::

::: paragraph
If we sent an order that is being processed OK then the caller will
receive an Exchange as reply containing **`Order OK`** as the payload
and **`orderid=123`** in a header.
:::

::: paragraph
If the order could **not** be processed and thus an
**`OrderFailedException`** was thrown the caller will **not** receive
this exception but our customized response that we have fabricated in
the **`orderFailed`** method in our **`OrderService`**. So the caller
receives an Exchange with the payload **`Order ERROR`** and a
**`orderid=failed`** in a header.
:::
:::::::::::::::
::::::::::::::::

::::::: sect1
## Using Handled with Spring XML DSL {#_using_handled_with_spring_xml_dsl}

:::::: sectionbody
::: paragraph
The same route as above in Spring XML DSL:
:::

:::: listingblock
::: content
``` highlight
 <!-- setup our error handler as the deal letter channel -->
<bean id="errorHandler" class="org.apache.camel.builder.DeadLetterChannelBuilder">
    <property name="deadLetterUri" value="mock:error"/>
</bean>

<!-- this is our POJO bean with our business logic defined as a plain spring bean -->
<bean id="orderService" class="org.apache.camel.spring.processor.onexception.OrderService" />

<!-- this is the camel context where we define the routes -->
<!-- define our error handler as a global error handler -->
<camelContext errorHandlerRef="errorHandler" xmlns="http://camel.apache.org/schema/spring">

  <onException>
    <!-- the exception is full qualified names as plain strings -->
    <!-- there can be more just add a 2nd, 3rd exception element (unbounded) -->
    <exception>org.apache.camel.spring.processor.onexception.OrderFailedException</exception>
    <!-- we can set the redelivery policy here as well -->
    <redeliveryPolicy maximumRedeliveries="1" />
    <!-- mark this as handled -->
    <handled>
      <constant>true</constant>
    </handled>
    <!-- let our order service handle this exception, call the orderFailed method -->
    <bean ref="orderService" method="orderFailed" />
    <!-- and since this is a unit test we use mock for assertions -->
    <to uri="mock:error" />
  </onException>

  <route>
    <!-- the route -->
    <from uri="direct:start" />
    <!-- in the normal route then route to our order service and call handleOrder method -->
    <bean ref="orderService" method="handleOrder" />
    <!-- and since this is a unit test we use mock for assertions -->
    <to uri="mock:result" />
  </route>

</camelContext>
```
:::
::::
::::::
:::::::

::::::::::::: sect1
## Handling and Sending a Fixed Response Back to the Client {#_handling_and_sending_a_fixed_response_back_to_the_client}

:::::::::::: sectionbody
::: paragraph
In the route above we handled the exception but routed it to a different
endpoint. What if you need to alter the response and send a fixed
response back to the original caller (the client). No secret here just
do as you do in normal Camel routing, use
[transform](components:eips:message-translator.html) to set the
response, as shown in the sample below:
:::

:::: listingblock
::: content
``` highlight
// we catch MyFunctionalException and want to mark it as handled
// (= no failure returned to client)
// but we want to return a fixed text response, so we transform
// OUT body as Sorry.
onException(MyFunctionalException.class)
  .handled(true)
  .transform().constant("Sorry");
```
:::
::::

::: paragraph
We modify the sample slightly to return the original caused exception
message instead of the fixed text `Sorry`:
:::

:::: listingblock
::: content
``` highlight
// we catch MyFunctionalException and want to mark it as handled
// (= no failure returned to client)
// but we want to return a fixed text response, so we transform
// OUT body and return the exception message
onException(MyFunctionalException.class)
  .handled(true)
  .transform(exceptionMessage());
```
:::
::::

::: paragraph
And we can use the [Simple](components:languages:simple-language.html)
language to set a readable error message with the caused exception
message:
:::

:::: listingblock
::: content
``` highlight
// we catch MyFunctionalException and want to mark it as handled
// (= no failure returned to client)
// but we want to return a fixed text response, so we transform
// OUT body and return a nice message
// using the simple language where we want insert the exception
// message
onException(MyFunctionalException.class)
  .handled(true)
  .transform().simple("Error reported: ${exception.message} - cannot process this message.");
```
:::
::::
::::::::::::
:::::::::::::

:::::::::::::::: sect1
## Handle and Continue Exceptions {#_handle_and_continue_exceptions}

::::::::::::::: sectionbody
::: paragraph
The option `continued` allows you to both **`handle`** and
**`continue`** routing in the original route as if the exception did not
occur.
:::

::: paragraph
For example: to ignore and continue when the **`IDontCareException`**
was thrown we can do this:
:::

:::: listingblock
::: content
``` highlight
onException(IDontCareException.class)
    .continued(true);
```
:::
::::

::: paragraph
You can maybe compare continued with a having a **`try …​ catch`** block
around each step and then just ignore the exception. Using continued
makes it easier in Camel as you otherwise had to use [Try Catch
Finally](try-catch-finally.html) style for this kind of use case.
:::

::::::::: sect2
### Example Using continued {#_example_using_continued}

::: paragraph
In this route below we want to do special handling of all
**`IllegalArgumentException`** as we just want to continue routing.
:::

:::: listingblock
::: content
``` highlight
onException(IllegalArgumentException.class).continued(true);

from("direct:start")
  .to("mock:start")
  .throwException(new IllegalArgumentException("Forced"))
  .to("mock:result");
```
:::
::::

::: paragraph
And the same example in Spring XML DSL:
:::

:::: listingblock
::: content
``` highlight
 <camelContext xmlns="http://camel.apache.org/schema/spring">

        <onException>
            <exception>java.lang.IllegalArgumentException</exception>
            <!-- tell Camel to handle and continue when this exception was thrown -->
            <continued><constant>true</constant></continued>
        </onException>

        <route>
            <from uri="direct:start"/>
            <to uri="mock:start"/>
            <throwException message="Forced" exceptionType="java.lang.IllegalArgumentException"/>
            <to uri="mock:result"/>
        </route>

    </camelContext>
```
:::
::::
:::::::::
:::::::::::::::
::::::::::::::::

:::::: sect1
## What is the Difference Between Handled and Continued? {#_what_is_the_difference_between_handled_and_continued}

::::: sectionbody
::: paragraph
If handled is true, then the thrown exception will be *handled* and
Camel will **not** continue routing in the original route, but break
out. However you can configure a route in the **`onException`** which
will be used instead. You use this route if you need to create some
custom response message back to the caller, or do any other processing
because that exception was thrown.
:::

::: paragraph
If continued is true, then Camel will catch the exception and in fact
just ignore it and continue routing in the original route. However if
you have a route configured in the **`onException`** it will route that
route first, before it will continue routing in the original route.
:::
:::::
::::::

:::::::::::: sect1
## Using `useOriginalMessage` {#_using_useoriginalmessage}

::::::::::: sectionbody
::: paragraph
The option **`useOriginalMessage`** is used for routing the original
input message instead of the current message that potential is modified
during routing.
:::

::: paragraph
For example: if you have this route:
:::

:::: listingblock
::: content
``` highlight
from("jms:queue:order:input")
    .to("bean:validateOrder");
    .to("bean:transformOrder")
    .to("bean:handleOrder");
```
:::
::::

::: paragraph
The route listen for JMS messages and validates, transforms and handle
it. During this the [Exchange](exchange.html) payload is
transformed/modified. So in case something goes wrong and we want to
move the message to another JMS destination, then we can add an
**`onException`**. But when we move the [Exchange](exchange.html) to
this destination we do not know in which state the message is in. Did
the error happen in before the **`transformOrder`** or after? So to be
sure we want to move the original input message we received from
`jms:queue:order:input`. So we can do this by enabling the
**`useOriginalMessage`** option as shown below:
:::

:::: listingblock
::: content
``` highlight
// will use original input message (body and headers)
onException(MyOrderException.class)
    .useOriginalMessage()
    .handled(true)
    .to("jms:queue:order:failed");
```
:::
::::

::: paragraph
Then the messages routed to the **`jms:queue:order:failed`** is the
original input. If we want to manually retry we can move the JMS message
from the failed to the input queue, with no problem as the message is
the same as the original we received.
:::
:::::::::::
::::::::::::

::::::: sect1
## `useOriginalMessage` with Spring DSL {#_useoriginalmessage_with_spring_dsl}

:::::: sectionbody
::: paragraph
The **`useOriginalMessage`** option is defined as a boolean attribute on
the **`<onException>`** XML tag in Spring DSL. So the definition above
would be:
:::

:::: listingblock
::: content
``` highlight
<onException useOriginalMessage="true">
    <exception>com.mycompany.MyOrderException</exception>
    <handled><constant>true</constant></handled>
    <to uri="jms:queue:order:failed"/>
</onException>
```
:::
::::
::::::
:::::::

::::: sect1
## Boundary of original message {#_boundary_of_original_message}

:::: sectionbody
::: paragraph
The original input means the input message that are bounded by the
current unit of work. An unit of work typically spans one route, or
multiple routes if they are connected using internal endpoints such as
direct or seda. When messages are passed via external endpoints such as
JMS or HTTP then the consumer will create a new unit of work, with the
message it received as input as the original input. Also, some EIP
patterns such as splitter, multicast, will create a new unit of work
boundary for the messages in their sub-route (i.e. the split message);
however these EIPs have an option named shareUnitOfWork which allows
combining with the parent unit of work in regard to error handling and
therefore use the parent original message.
:::
::::
:::::

::::::::: sect1
## Using `useOriginalBody` {#_using_useoriginalbody}

:::::::: sectionbody
::: paragraph
The useOriginalBody is similar to useOriginalMessage as documented
above. You may want to use useOriginalBody when you want to be able to
enrich the message with custom headers and preserve the original message
body before sending to an error handler or dead letter channel.
:::

::: paragraph
For example: if you have this route:
:::

:::: listingblock
::: content
``` highlight
// will use original input body
onException(MyOrderException.class)
    .useOriginalBody()
    .handled(true)
    .to("jms:queue:order:failed");

from("jms:queue:order:input")
    .setHeader("application", constant("OrderApp"))
    .to("bean:validateOrder");
    .to("bean:transformOrder")
    .to("bean:handleOrder");
```
:::
::::

::: paragraph
Then the message has been enriched with a header named application after
the original message was received by the JMS endpoint. And in case of an
error `onException` will handle the exception and use the original
message body and the headers from the current message as-is, which means
the headers will include the application header.
:::
::::::::
:::::::::

::::::::::::::::::::::::: sect1
## Advanced Usage of Exception Clause {#_advanced_usage_of_exception_clause}

:::::::::::::::::::::::: sectionbody
::: paragraph
Camel supports advanced configuration of exception clauses.
:::

::::::::: sect2
### Using Global and Per Route Exception Clauses {#_using_global_and_per_route_exception_clauses}

::: paragraph
You can define exception clauses either as:
:::

::: ulist
- global

- or route specific
:::

::: paragraph
We start off with the sample that we change over time. First off we use
only global exception clauses:
:::

:::: listingblock
::: content
``` highlight
// default should errors go to mock:error
errorHandler(deadLetterChannel("mock:error").redeliveryDelay(0));

// if a MyTechnicalException is thrown we will not try to
// redeliver and we mark it as handled
// so the caller does not get a failure
// since we have no to then the exchange will continue to be
// routed to the normal error handler
// destination that is mock:error as defined above
onException(MyTechnicalException.class).maximumRedeliveries(0).handled(true);

// if a MyFunctionalException is thrown we do not want Camel to
// redelivery but handle it our self using
// our bean myOwnHandler, then the exchange is not routed to the
// default error (mock:error)
onException(MyFunctionalException.class).maximumRedeliveries(0).handled(true).to("bean:myOwnHandler");

// here we route message to our service bean
from("direct:start").choice().when().xpath("//type = 'myType'").to("bean:myServiceBean").end().to("mock:result");
```
:::
::::

::: paragraph
In the next sample we change the global exception policies to be pure
route specific.
:::
:::::::::

::::::::::::: sect2
### Must use `.end()` for route specific exception policies {#_must_use_end_for_route_specific_exception_policies}

::: paragraph
\[IMPORTANT\] This requires to end the **`onException`** route with
**`.end()`** to indicate where it stops and when the regular route
continues.
:::

:::: listingblock
::: content
``` highlight
// default should errors go to mock:error
errorHandler(deadLetterChannel("mock:error"));

// here we start the routing with the consumer
from("direct:start")

    // if a MyTechnicalException is thrown we will not try to
    // redeliver and we mark it as handled
    // so the caller does not get a failure
    // since we have no to then the exchange will continue to be
    // routed to the normal error handler
    // destination that is mock:error as defined above
    // we MUST use .end() to indicate that this sub block is
    // ended
    .onException(MyTechnicalException.class).maximumRedeliveries(0).handled(true).end()

    // if a MyFunctionalException is thrown we do not want Camel
    // to redelivery but handle it our self using
    // our bean myOwnHandler, then the exchange is not routed to
    // the default error (mock:error)
    // we MUST use .end() to indicate that this sub block is
    // ended
    .onException(MyFunctionalException.class).maximumRedeliveries(0).handled(true).to("bean:myOwnHandler").end()

    // here we have the regular routing
    .choice().when().xpath("//type = 'myType'").to("bean:myServiceBean").end().to("mock:result");
```
:::
::::

::: paragraph
And now it gets complex as we combine global and route specific
exception policies as we introduce a second route in the sample:
:::

:::: listingblock
::: content
``` highlight
// global error handler
// as its based on a unit test we do not have any delays between
// and do not log the stack trace
errorHandler(deadLetterChannel("mock:error").redeliveryDelay(0).logStackTrace(false));

// shared for both routes
onException(MyTechnicalException.class).handled(true).maximumRedeliveries(2).to("mock:tech.error");

from("direct:start")
    // route specific on exception for MyFunctionalException
    // we MUST use .end() to indicate that this sub block is
    // ended
    .onException(MyFunctionalException.class).maximumRedeliveries(0).end().to("bean:myServiceBean").to("mock:result");

from("direct:start2")
    // route specific on exception for MyFunctionalException
    // that is different than the previous route
    // here we marked it as handled and send it to a different
    // destination mock:handled
    // we MUST use .end() to indicate that this sub block is
    // ended
    .onException(MyFunctionalException.class).handled(true).maximumRedeliveries(0).to("mock:handled").end().to("bean:myServiceBean").to("mock:result");
```
:::
::::

::: paragraph
Notice that we can define the same exception **`MyFunctionalException`**
in both routes, but they are configured differently and thus is handled
different depending on the route. You can of course also add a new
**`onException`** to one of the routes so it has an additional exception
policy.
:::

::: paragraph
And finally we top this by throwing in a nested error handler as well,
as we add the 3rd route shown below:
:::

:::: listingblock
::: content
``` highlight
from("direct:start3")
    // route specific error handler that is different than the
    // global error handler
    // here we do not redeliver and send errors to mock:error3
    // instead of the global endpoint
    .errorHandler(deadLetterChannel("mock:error3").maximumRedeliveries(0))

    // route specific on exception to mark MyFunctionalException
    // as being handled
    .onException(MyFunctionalException.class).handled(true).end()
    // however we want the IO exceptions to redeliver at most 3
    // times
    .onException(IOException.class).maximumRedeliveries(3).end().to("bean:myServiceBean").to("mock:result");
```
:::
::::
:::::::::::::

:::: sect2
### Global exception policies and nested error handlers {#_global_exception_policies_and_nested_error_handlers}

::: paragraph
The sample above with both nested error handlers and both global and per
route exception clauses is a bit advanced. It's important to get the
fact straight that the **global** exception clauses is really global so
they also applies for nested error handlers. So if a
**`MyTechnicalException`** is thrown then it's the global exception
policy that is selected.
:::
::::
::::::::::::::::::::::::
:::::::::::::::::::::::::

::::::::: sect1
## Using Fine Grained Selection Using `onWhen` Predicate {#_using_fine_grained_selection_using_onwhen_predicate}

:::::::: sectionbody
::: paragraph
You can attach an [Expression](expression.html) to the exception clause
to have fine grained control when a clause should be selected or not. As
it's an [Expression](expression.html) you can use any kind of code to
perform the test. Here is a sample:
:::

:::: listingblock
::: content
``` highlight
errorHandler(deadLetterChannel("mock:error").redeliveryDelay(0).maximumRedeliveries(3));

// here we define our onException to catch MyUserException when
// there is a header[user] on the exchange that is not null
onException(MyUserException.class).onWhen(header("user").isNotNull()).maximumRedeliveries(1)
    // setting delay to zero is just to make unit testing faster
    .redeliveryDelay(0).to(ERROR_USER_QUEUE);

// here we define onException to catch MyUserException as a kind
// of fallback when the above did not match.
// Notice: The order how we have defined these onException is
// important as Camel will resolve in the same order as they
// have been defined
onException(MyUserException.class).maximumRedeliveries(2)
    // setting delay to zero is just to make unit testing faster
    .redeliveryDelay(0).to(ERROR_QUEUE);
```
:::
::::

::: paragraph
In the sample above we have two **`onException`**\'s defined. The first
has an **`onWhen`** expression attached to only trigger if the message
has a header with the key user that is not null. If so this clause is
selected and is handling the thrown exception. The second clause is a
for coarse gained selection to select the same exception being thrown
but when the expression is evaluated to false.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | ::: paragraph                     |
| Note                              | This is not required, if the      |
| :::                               | second clause is omitted, then    |
|                                   | the default error handler will    |
|                                   | kick in.                          |
|                                   | :::                               |
+-----------------------------------+-----------------------------------+
:::
::::::::
:::::::::

::::::::::: sect1
## Using onRedelivery Processor {#_using_onredelivery_processor}

:::::::::: sectionbody
::: paragraph
[Dead Letter Channel](components:eips:dead-letter-channel.html) has
support for **`onRedelivery`** to allow custom processing of a Message
before its being redelivered. It can be used to add some customer header
or whatnot. In Camel 2.0 we have added this feature to [Exception
Clause](#) as well, so you can use per exception scoped on redelivery.
Camel will fallback to use the one defined on [Dead Letter
Channel](components:eips:dead-letter-channel.html) if any, if none
exists on the [Exception Clause](#). See [Dead Letter
Channel](components:eips:dead-letter-channel.html) for more details on
**`onRedelivery`**.
:::

::: paragraph
In the code below we want to do some custom code before redelivering any
**`IOException`**. So we configure an **`onException`** for the
**`IOException`** and set the **`onRedelivery`** to use our custom
processor:
:::

:::: listingblock
::: content
``` highlight
// when we redeliver caused by an IOException we want to do some
// special code before the redeliver attempt
onException(IOException.class)
    // try to redeliver at most 3 times
    .maximumRedeliveries(3)
    // setting delay to zero is just to make unit testing faster
    .redeliveryDelay(0).onRedelivery(new MyIORedeliverProcessor());
```
:::
::::

::: paragraph
And in our custom processor we set a special timeout header to the
message. You can of course do anything what you like in your code.
:::

:::: listingblock
::: content
``` highlight
// This is our processor that is executed before every redelivery attempt
// here we can do what we want in the java code, such as altering the
// message
public static class MyRedeliverProcessor implements Processor {

    @Override
    public void process(Exchange exchange) throws Exception {
        // the message is being redelivered so we can alter it

        // we just append the redelivery counter to the body
        // you can of course do all kind of stuff instead
        String body = exchange.getIn().getBody(String.class);
        int count = exchange.getIn().getHeader("CamelRedeliveryCounter", Integer.class);

        exchange.getIn().setBody(body + count);
    }
}
```
:::
::::
::::::::::
:::::::::::

:::::::::: sect1
## Using onRedelivery in Spring XML DSL {#_using_onredelivery_in_spring_xml_dsl}

::::::::: sectionbody
::: paragraph
In Spring DSL you need to use the **`onRedeliveryRef`** attribute to
refer to a spring bean id that is your custom processor:
:::

:::: listingblock
::: content
``` highlight
<onException onRedeliveryRef="myIORedeliverProcessor">
    <exception>java.io.IOException</exception>
</onException>
```
:::
::::

::: paragraph
And our processor is just a regular spring bean (we use **`$`** for the
inner class as this code is based on unit testing):
:::

:::: listingblock
::: content
``` highlight
 <bean id="myRedeliveryProcessor"
          class="org.apache.camel.processor.DeadLetterChannelOnExceptionOnRedeliveryTest$MyRedeliverProcessor"/>
```
:::
::::
:::::::::
::::::::::

::::::::::::: sect1
## Using onExceptionOccurred Processor {#_using_onexceptionoccurred_processor}

:::::::::::: sectionbody
::: paragraph
[Dead Letter Channel](components:eips:dead-letter-channel.html) has
support for **`onExceptionOccurred`** to allow custom processing of a
Message just after the exception was thrown. It can be used to do some
custom logging or whatnot. The difference between **`onRedelivery`**
processor and **`onExceptionOccurred`** processor, is that the former is
processed just before a redelivery attempt is being performed, that
means it will not happen right after an exception was thrown. For
example if the error handler has been configured to perform 5 seconds
delay between redelivery attempts, then the redelivery processor is
invoked 5 seconds after the exception was thrown. On the other hand the
**`onExceptionOccurred`** processor is always invoked right after the
exception was thrown, and also if redelivery has been disabled.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | ::: paragraph                     |
| Note                              | Any new exceptions thrown from    |
| :::                               | the **`onExceptionOccurred`**     |
|                                   | processor is logged as **`WARN`** |
|                                   | and ignored, to not override the  |
|                                   | existing exception.               |
|                                   | :::                               |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
In the code below we want to do some custom logging when an exception
happened. Therefore we configure an **`onExceptionOccurred`** to use our
custom processor:
:::

:::: {.listingblock .java}
::: content
``` highlight
errorHandler(defaultErrorHandler()
    .maximumRedeliveries(3)
    .redeliveryDelay(5000)
    .onExceptionOccurred(myProcessor));
```
:::
::::

:::::: sect2
### Using onRedelivery in Spring XML DSL {#_using_onredelivery_in_spring_xml_dsl_2}

::: paragraph
In Spring DSL you need to use the **`onExceptionOccurredRef`** attribute
to refer to a spring bean id that is your custom processor:
:::

:::: listingblock
::: content
``` highlight
<bean id="myProcessor" class="com.foo.MyExceptionLoggingProcessor"/>

<camelContext errorHandlerRef="eh" xmlns="http://camel.apache.org/schema/spring">
    <errorHandler id="eh" type="DefaultErrorHandler" onExceptionOccurredRef="myProcessor">
        <redeliveryPolicy maximumRedeliveries="3" redeliveryDelay="5000"/>
    </errorHandler>
    ...
</camelContext>
```
:::
::::
::::::
::::::::::::
:::::::::::::

::::::::::: sect1
## Using Fine Grained Retry Using retryWhile Predicate {#_using_fine_grained_retry_using_retrywhile_predicate}

:::::::::: sectionbody
::: paragraph
When you need fine grained control for determining if an exchange should
be retried or not you can use the **`retryWhile`** predicate. Camel will
redeliver until the predicate returns false.
:::

::: paragraph
Example:
:::

:::: listingblock
::: content
``` highlight
// we want to use a predicate for retries so we can determine in
// our bean when retry should stop, notice it will overrule the global
// error handler where we defined at most 1 redelivery attempt. Here we will
// continue until the predicate returns false
onException(MyFunctionalException.class).retryWhile(method("myRetryHandler")).handled(true).transform().constant("Sorry");
```
:::
::::

::: paragraph
Where the bean **`myRetryHandler`** is computing if we should retry or
not:
:::

:::: listingblock
::: content
``` highlight
public class MyRetryBean {

    // using bean binding we can bind the information from the exchange to
    // the types we have in our method signature
    public boolean retry(@Header(Exchange.REDELIVERY_COUNTER) Integer counter) {
        // NOTE: counter is the redelivery attempt, will start from 1
        // we can of course do what ever we want to determine the result but
        // this is a unit test so we end after 3 attempts
        return counter < 3;
    }
}
```
:::
::::
::::::::::
:::::::::::

:::::::::: sect1
## Using Custom ExceptionPolicyStrategy {#_using_custom_exceptionpolicystrategy}

::::::::: sectionbody
::: paragraph
The default
[ExceptionPolicyStrategy](https://www.javadoc.io/doc/org.apache.camel/camel-core-processor/current/org/apache/camel/processor/errorhandler/ExceptionPolicyStrategy.html)
in Camel should be sufficient in nearly all use-cases. However, if you
need to use your own (use only for rare and advanced use-cases) this can
be configured as the sample below illustrates:
:::

:::: listingblock
::: content
``` highlight
// configure the error handler to use my policy instead of the default from Camel
errorHandler(deadLetterChannel("mock:error").exceptionPolicyStrategy(new MyPolicy()));
```
:::
::::

::: paragraph
Using our own strategy **`MyPolicy`** we can change the default behavior
of Camel with our own code to resolve which exception type from above
should be handling the given thrown exception.
:::

:::: listingblock
::: content
``` highlight
public static class MyPolicy implements ExceptionPolicyStrategy {

    @Override
    public ExceptionPolicyKey getExceptionPolicy(Set<ExceptionPolicyKey> exceptionPolicies, Exchange exchange, Throwable exception) {
        // This is just an example that always forces the exception type configured
        // with MyPolicyException to win.
        return new ExceptionPolicyKey(null, MyPolicyException.class, null);
    }
}
```
:::
::::
:::::::::
::::::::::

::::::::::: sect1
## Using the Exception Clause in Spring XML DSL {#_using_the_exception_clause_in_spring_xml_dsl}

:::::::::: sectionbody
::: paragraph
You can use all of the above mentioned exception clause features in the
Spring XML DSL as well. Here are a few examples:
:::

::: ulist
- Global scoped
:::

:::: listingblock
::: content
``` highlight
<!-- setup our error handler as the deal letter channel -->
<bean id="errorHandler" class="org.apache.camel.builder.DeadLetterChannelBuilder">
    <property name="deadLetterUri" value="mock:error"/>
</bean>

<!-- this is our POJO bean with our business logic defined as a plain spring bean -->
<bean id="orderService" class="org.apache.camel.spring.processor.onexception.OrderService" />

<!-- this is the camel context where we define the routes -->
<!-- define our error handler as a global error handler -->
<camelContext errorHandlerRef="errorHandler" xmlns="http://camel.apache.org/schema/spring">

  <onException>
    <!-- the exception is full qualified names as plain strings -->
    <!-- there can be more just add a 2nd, 3rd exception element (unbounded) -->
    <exception>org.apache.camel.spring.processor.onexception.OrderFailedException</exception>
    <!-- we can set the redelivery policy here as well -->
    <redeliveryPolicy maximumRedeliveries="1" />
    <!-- mark this as handled -->
    <handled>
      <constant>true</constant>
    </handled>
    <!-- let our order service handle this exception, call the orderFailed method -->
    <bean ref="orderService" method="orderFailed" />
    <!-- and since this is a unit test we use mock for assertions -->
    <to uri="mock:error" />
  </onException>

  <route>
    <!-- the route -->
    <from uri="direct:start" />
    <!-- in the normal route then route to our order service and call handleOrder method -->
    <bean ref="orderService" method="handleOrder" />
    <!-- and since this is a unit test we use mock for assertions -->
    <to uri="mock:result" />
  </route>

</camelContext>
```
:::
::::

::: ulist
- Route specific scoped
:::

:::: listingblock
::: content
``` highlight
<!-- setup our error handler as the deal letter channel -->
<bean id="deadLetter" class="org.apache.camel.builder.DeadLetterChannelBuilder">
    <property name="deadLetterUri" value="mock:dead"/>
</bean>

<!-- the default error handler used in the 2nd route -->
<bean id="defaultErrorHandler" class="org.apache.camel.builder.DefaultErrorHandlerBuilder"/>

<!-- this is our POJO bean with our business logic defined as a plain spring bean -->
<bean id="orderService" class="org.apache.camel.spring.processor.onexception.OrderService"/>

<!-- this is the camel context where we define the routes -->
<camelContext xmlns="http://camel.apache.org/schema/spring">

    <route errorHandlerRef="deadLetter">
        <from uri="direct:start"/>
        <onException>
            <exception>org.apache.camel.spring.processor.onexception.OrderFailedException</exception>
            <redeliveryPolicy maximumRedeliveries="1"/>
            <handled>
                <constant>true</constant>
            </handled>
            <bean ref="orderService" method="orderFailed"/>
            <to uri="mock:error"/>
        </onException>
        <bean ref="orderService" method="handleOrder"/>
        <to uri="mock:result"/>
    </route>

    <!-- The exception clause specified in the first route will not be used in this route -->
    <route errorHandlerRef="defaultErrorHandler">
        <from uri="direct:start_with_no_handler"/>
        <bean ref="orderService" method="handleOrder"/>
        <to uri="mock:result"/>
    </route>

</camelContext>
```
:::
::::
::::::::::
:::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
