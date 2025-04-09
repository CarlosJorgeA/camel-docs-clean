::: {#header}
# Bean Binding
:::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
:::::: {#preamble}
::::: sectionbody
::: paragraph
Bean Binding in Camel defines both which methods are invoked and also
how the [Message](components:eips:message.html) is converted into the
parameters of the method invoked.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | This requires to include          |
| Note                              | `camel-bean` as dependency on the |
| :::                               | classpath.                        |
+-----------------------------------+-----------------------------------+
:::
:::::
::::::

:::::::: sect1
## Choosing the method to invoke {#_choosing_the_method_to_invoke}

::::::: sectionbody
::: paragraph
The binding of a Camel [Message](components:eips:message.html) to a bean
method call can occur in different ways, in the following order of
importance:
:::

::: ulist
- You can qualify parameter types to select exactly which method to use
  among overloads with the same name (see below for more details).

- You can specify parameter values directly in the method option (see
  below for more details).

- you can explicitly specify the method name in the [DSL](dsl.html) or
  when using [POJO Consuming](pojo-consuming.html) or [POJO
  Producing](pojo-producing.html).

- if the bean has a method annotated with the `@Handler` annotation,
  then that method is selected.

- if the bean can be converted to a [Processor](processor.html) using
  the [Type Converter](type-converter.html) mechanism, then this is used
  to process the message. The ActiveMQ component used this mechanism (in
  Camel 3) to allow any JMS MessageListener to be invoked directly by
  Camel without having to write any integration glue code. You can use
  the same mechanism to integrate Camel into any other
  messaging/remoting frameworks.

- the type of the body is used to find a matching method; an error is
  thrown if a single method cannot be chosen unambiguously.

- you can also use `Exchange` as the parameter itself

- the return type cannot be `Exchange`. However, you can use `Exchange`
  as input parameter, and let the method be `void`, or return some other
  Object type (cannot be `Exchange`).

- if the bean class is private (or package-private), interface methods
  will be preferred since Camel can't invoke class methods on such beans
:::

::: paragraph
In cases when Camel cannot choose a method to invoke, an
`AmbiguousMethodCallException` is thrown.
:::

::: paragraph
By default, the return value is set in the outbound message body.
:::
:::::::
::::::::

:::::::::::::: sect1
## Asynchronous processing {#_asynchronous_processing}

::::::::::::: sectionbody
::: paragraph
You can return a `CompletionStage` implementation (e.g.
`CompletableFuture`) to implement asynchronous processing.
:::

::: paragraph
Please be sure to properly complete the CompletionStage with the result
or exception, including any timeout handling. Exchange processing would
wait for completion and would not impose any timeouts automatically.
It's extremely useful to monitor
`org.apache.camel.spi.InflightRepository` for any hanging messages.
:::

::: paragraph
Note that completing with `"null"` won't set out body message body to
`null`, but would keep message intact. This is useful to support methods
that don't modify exchange and return `CompletableFuture<Void>`. To set
body to null, just add `Exchange` method parameter and directly modify
exchange messages.
:::

::: paragraph
Examples:
:::

::: paragraph
Simple asynchronous processor, modifying message body.
:::

:::: listingblock
::: content
``` highlight
public CompletableFuture<String> doSomethingAsync(String body)
```
:::
::::

::: paragraph
Composite processor that do not modify exchange
:::

:::: listingblock
::: content
``` highlight
 public CompletableFuture<Void> doSomethingAsync(String body) {
     return CompletableFuture.allOf(doA(body), doB(body), doC());
 }
```
:::
::::
:::::::::::::
::::::::::::::

:::::::::::::::::::::::::::: sect1
## Parameter binding {#_parameter_binding}

::::::::::::::::::::::::::: sectionbody
::: paragraph
When a method has been chosen for invocation, Camel will bind to the
parameters of the method.
:::

::: paragraph
The following Camel-specific types are automatically bound:
:::

::: ulist
- `org.apache.camel.Exchange`

- `org.apache.camel.Message`

- `org.apache.camel.CamelContext`

- `org.apache.camel.TypeConverter`

- `org.apache.camel.spi.Registry`

- `java.lang.Exception`
:::

::: paragraph
So, if you declare any of these types, they will be provided by Camel.
**Note that `Exception` will bind to the caught exception in the
[Exchange](exchange.html)** - so it's often usable if you employ a
[Bean](components::bean-component.html) to handle, e.g., an
`onException` route.
:::

::: paragraph
What is most interesting is that Camel will also try to bind the body of
the [Exchange](exchange.html) to the first parameter of the method
signature (albeit not any of the types above). So if, for instance, we
declare a parameter as `String body`, then Camel will bind the message
body to this type. Camel will also automatically convert to the type
declared in the method signature.
:::

::: paragraph
Let's review some examples:
:::

::: paragraph
Below is a simple method with a body binding. Camel will bind the IN
body to the `body` parameter and convert it to a `String`.
:::

:::: listingblock
::: content
``` highlight
public String doSomething(String body)
```
:::
::::

::: paragraph
In the following sample we got one of the automatically bound types as
well. For instance, a `Registry` that we can use to lookup beans.
:::

:::: listingblock
::: content
``` highlight
public String doSomething(String body, Registry registry)
```
:::
::::

::: paragraph
We can use [Exchange](exchange.html) as well:
:::

:::: listingblock
::: content
``` highlight
public String doSomething(String body, Exchange exchange)
```
:::
::::

::: paragraph
You can also have multiple types:
:::

:::: listingblock
::: content
``` highlight
public String doSomething(String body, Exchange exchange, TypeConverter converter)
```
:::
::::

::: paragraph
And imagine you use a [Pojo](components::bean-component.html) to handle
a given custom exception `InvalidOrderException` - we can then bind that
as well:
:::

:::: listingblock
::: content
``` highlight
public String badOrder(String body, InvalidOrderException invalid)
```
:::
::::

::: paragraph
Notice that we can bind to it even if we use a subtype of
`java.lang.Exception` as Camel still knows it's an exception and can
bind the cause (if any exists).
:::

::: paragraph
So what about headers and other stuff? Well, now it gets a bit tricky.
We can use annotations to help us or specify the binding in the method
name option.
:::

::: paragraph
See the following sections for more detail.
:::
:::::::::::::::::::::::::::
::::::::::::::::::::::::::::

::::::::::::::::: sect1
## Binding Annotations {#_binding_annotations}

:::::::::::::::: sectionbody
::: paragraph
You can use the [Parameter Binding
Annotations](parameter-binding-annotations.html) to customize how
parameter values are created from the
[Message](components:eips:message.html)
:::

::::::::: sect2
### Examples {#_examples}

::: paragraph
For example, a [Bean](components:eips:bean-eip.html) such as:
:::

:::: listingblock
::: content
``` highlight
public class Bar {
    public String doSomething(String body) {
        // process the in body and return whatever you want
        return "Bye World";
    }
}
```
:::
::::

::: paragraph
Or the Exchange example. Notice that the return type must be **void**
when there is only a single parameter of the type
`org.apache.camel.Exchange`:
:::

:::: listingblock
::: content
``` highlight
 public class Bar {
     public void doSomething(Exchange exchange) {
         // process the exchange
         exchange.getIn().setBody("Bye World");
     }
 }
```
:::
::::
:::::::::

::::::: sect2
### Using \@Handler {#_using_handler}

::: paragraph
You can mark a method in your bean with the `@Handler` annotation to
indicate that this method should be used for [Bean Binding](#).
:::

::: paragraph
This has an advantage as you don't need to specify a method name in the
Camel route, and therefore do not run into problems after renaming the
method in an IDE that can't find all its references.
:::

:::: listingblock
::: content
``` highlight
public class Bar {
    @Handler
    public String doSomething(String body) {
        // process the in body and return whatever you want
        return "Bye World";
    }
}
```
:::
::::
:::::::
::::::::::::::::
:::::::::::::::::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Parameter binding using method option {#_parameter_binding_using_method_option}

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
Camel uses the following rules to determine if it's a parameter value in
the method option
:::

::: ulist
- The value is either `true` or `false` which denotes a boolean value

- The value is a numeric value such as `123` or `7`

- The value is a String enclosed with either single or double quotes

- The value is null which denotes a `null` value

- It can be evaluated using the
  [Simple](components:languages:simple-language.html) language, which
  means you can use, e.g., `${body}`, `${header.foo}` and others
  [Simple](components:languages:simple-language.html) tokens. Notice the
  tokens must be enclosed with `${ }`.

- The value ends with `.class` then it's a type declaration instead -
  see the next section about specifying types for overloaded methods.
:::

::: paragraph
When invoking a [Bean](components:eips:bean-eip.html) you can instruct
Camel to invoke a specific method by providing the method name:
:::

:::: listingblock
::: content
``` highlight
.bean(OrderService.class, "doSomething")
```
:::
::::

::: paragraph
Here we tell Camel to invoke the *`doSomething`* method. Camel handles
the parameters\' binding. Now suppose the method has 2 parameters, and
the second parameter is a boolean where we want to pass in a true value:
:::

:::: listingblock
::: content
``` highlight
public void doSomething(String payload, boolean highPriority) {
    ...
}
```
:::
::::

::: paragraph
This can be done as follows:
:::

:::: listingblock
::: content
``` highlight
.bean(OrderService.class, "doSomething(*, true)")
```
:::
::::

::: paragraph
In the example above, we defined the first parameter using the wild card
symbol `*`, which tells Camel to bind this parameter to any type, and
let Camel figure this out. The second parameter has a fixed value of
`true`. Instead of the wildcard symbol, we can instruct Camel to use the
message body as shown:
:::

:::: listingblock
::: content
``` highlight
.bean(OrderService.class, "doSomething(${body}, true)")
```
:::
::::

::: paragraph
The syntax of the parameters is using the
[Simple](components:languages:simple-language.html) language so we have
to use `${ }` placeholders in the body to refer to the message body.
:::

::: paragraph
If you want to pass in a `null` value, then you can explicitly define
this in the method option as shown below:
:::

:::: listingblock
::: content
``` highlight
.to("bean:orderService?method=doSomething(null, true)")
```
:::
::::

::: paragraph
Specifying `null` as a parameter value instructs Camel to force passing
a `null` value.
:::

::: paragraph
Besides the message body, you can pass in the message headers as a
`java.util.Map`:
:::

:::: listingblock
::: content
``` highlight
.bean(OrderService.class, "doSomethingWithHeaders(${body}, ${headers})")
```
:::
::::

::: paragraph
You can also pass in other fixed values besides booleans. For example,
you can pass in a String and an integer:
:::

:::: listingblock
::: content
``` highlight
.bean(MyBean.class, "echo('World', 5)")
```
:::
::::

::: paragraph
In the example above, we invoke the echo method with two parameters. The
first has the content \'World\' (without quotes), and the second has the
value of 5. Camel will automatically convert these values to the
parameters\' types.
:::

::: paragraph
Having the power of the
[Simple](components:languages:simple-language.html) language allows us
to bind to message headers and other values such as:
:::

:::: listingblock
::: content
``` highlight
.bean(OrderService.class, "doSomething(${body}, ${header.high})")
```
:::
::::

::: paragraph
You can also use the OGNL support of the
[Simple](components:languages:simple-language.html) expression language.
Now suppose the message body is an object that has a method named
`asXml`. To invoke the `asXml` method we can do as follows:
:::

:::: listingblock
::: content
``` highlight
.bean(OrderService.class, "doSomething(${body.asXml}, ${header.high})")
```
:::
::::

::: paragraph
Instead of using `.bean` as shown in the examples above, you may want to
use `.to` instead as shown:
:::

:::: listingblock
::: content
``` highlight
.to("bean:orderService?method=doSomething(${body.asXml}, ${header.high})")
```
:::
::::

:::::::::::::::: sect2
### Using type qualifiers to select among overloaded methods {#_using_type_qualifiers_to_select_among_overloaded_methods}

::: paragraph
If you have a [Bean](components:eips:bean-eip.html) with overloaded
methods, you can now specify parameter types (must use `.class` style,
e.g. `com.foo.MyClass.class`) in the method name so Camel can match the
method you intend to use.
:::

::: paragraph
Given the following bean:
:::

:::: listingblock
::: content
``` highlight
 from("direct:start")
    .bean(MyBean.class, "hello(String.class)")
    .to("mock:result");
```
:::
::::

::: paragraph
Then the `MyBean` has 2 overloaded methods with the names `hello` and
`times`. So if we want to use the method which has two parameters, we
can do as follows in the Camel route:
:::

:::: listingblock
::: content
``` highlight
from("direct:start")
    .bean(MyBean.class, "hello(String.class, String.class)")
    .to("mock:result");
```
:::
::::

::: paragraph
We can also use a `*` as wildcard, so we can just say we want to execute
the method with two parameters we do:
:::

:::: listingblock
::: content
``` highlight
 from("direct:start")
    .bean(MyBean.class, "hello(*,*)")
    .to("mock:result");
```
:::
::::

::: paragraph
By default, Camel will match the type name using the simple name, e.g.,
any leading package name will be disregarded. However, if you want to
match using the FQN, then specify the FQN type and Camel will leverage
that. So if you have a parameter of type `com.foo.MyOrder` and you want
to match against the FQN, and **not** the simple name \"MyOrder\", then
follow this example:
:::

:::: listingblock
::: content
``` highlight
.bean(OrderService.class, "doSomething(com.foo.MyOrder.class)")
```
:::
::::
::::::::::::::::

::::::::::: sect2
### Declaring parameter type and value {#_declaring_parameter_type_and_value}

::: paragraph
**Available as of Camel 4.0**
:::

::: paragraph
Camel 3.x only supports either specifying parameter binding or type per
parameter in the method name option. You **cannot** specify both at the
same time, such as:
:::

:::: listingblock
::: content
``` highlight
doSomething(com.foo.MyOrder.class ${body}, boolean ${header.high}, int 123)
```
:::
::::

::: paragraph
However, we have implemented support for this in Camel 4, where you can
declare both using *name.class value* syntax as shown:
:::

:::: listingblock
::: content
``` highlight
doSomething(com.foo.MyOrder.class ${body}, boolean.class ${header.high}, int.class 123)
```
:::
::::

::: paragraph
Notice that you **MUST** use `name.class` when declaring the type, also
for String, int, boolean, etc.
:::
:::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
