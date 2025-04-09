::: {#header}
# Parameter Binding Annotations
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
:::::::: {#preamble}
::::::: sectionbody
::: paragraph
The bean parameter binding annotations from Camel are as follows:
:::

+-----------------------+----------------------+----------------------+
| Annotation            | Meaning              | Parameter            |
+=======================+======================+======================+
| `o                    | To bind to an        |                      |
| rg.apache.camel.Body` | inbound message body |                      |
+-----------------------+----------------------+----------------------+
| `org                  | To bind to a message | String name of the   |
| .apache.camel.Header` | header               | header               |
+-----------------------+----------------------+----------------------+
| `org.                 | To bind to the Map   |                      |
| apache.camel.Headers` | of the message       |                      |
|                       | headers              |                      |
+-----------------------+----------------------+----------------------+
| `org.a                | To bind to a named   | String name of the   |
| pache.camel.Variable` | variable             | variable             |
+-----------------------+----------------------+----------------------+
| `org.ap               | To bind to the       |                      |
| ache.camel.Variables` | variables map        |                      |
+-----------------------+----------------------+----------------------+
| `org.apache.ca        | To bind to a named   | String name of the   |
| mel.ExchangeProperty` | property on the      | property             |
|                       | exchange             |                      |
+-----------------------+----------------------+----------------------+
| `org.apache.came      | To bind to the       |                      |
| l.ExchangeProperties` | exchange property    |                      |
|                       | map on the exchange  |                      |
+-----------------------+----------------------+----------------------+
| `org.apache.cam       | To bind to an        |                      |
| el.ExchangeException` | Exception set on the |                      |
|                       | exchange             |                      |
+-----------------------+----------------------+----------------------+

::: paragraph
These annotations can be used with the
[Bean](components::bean-component.html) component or when invoking beans
in the [DSL](dsl.html)
:::

::: paragraph
Annotations can be used to define an [Expression](expression.html) or to
extract various headers, properties or payloads from a
[Message](components:eips:message.html) when invoking a bean method (see
[Bean Integration](bean-integration.html) for more detail of how to
invoke bean methods) together with being useful to help disambiguate
which method to invoke.
:::

::: paragraph
If no annotations are used then Camel assumes that a single parameter is
the body of the message. Camel will then use the [Type
Converter](type-converter.html) mechanism to convert from the expression
value to the actual type of the parameter.
:::
:::::::
::::::::

:::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Using bean parameter binding annotations {#_using_bean_parameter_binding_annotations}

::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
In this example below we have a `@Consume` consumer (like message
driven) that consumes JMS messages from the activemq queue. We use the
`@Header` and `@Body` parameter binding annotations to bind from the
JMSMessage to the method parameters.
:::

:::: listingblock
::: content
``` highlight
public class MyBean {

    @Consume("activemq:my.queue")
    public void doSomething(@Header("JMSCorrelationID") String correlationID, @Body String body) {
        // process the inbound message here
    }

}
```
:::
::::

::: paragraph
In the above Camel will extract the value of
`Message.getJMSCorrelationID()`, then using the [Type
Converter](type-converter.html) to adapt the value to the type of the
parameter if required - it will inject the parameter value for the
**correlationID** parameter. Then the payload of the message will be
converted to a String and injected into the **body** parameter.
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | You don't necessarily need to use |
| Tip                               | the `@Consume` annotation if you  |
| :::                               | don't want to as you could also   |
|                                   | make use of the Camel             |
|                                   | [DSL](dsl.html) to route to the   |
|                                   | bean's method as well.            |
+-----------------------------------+-----------------------------------+
:::

:::::::::::::::: sect2
### Using the DSL to invoke the bean method {#_using_the_dsl_to_invoke_the_bean_method}

::: paragraph
Here is another example which does not use [POJO
Consuming](pojo-consuming.html) annotations but instead uses the
[DSL](dsl.html) to route messages to the bean method
:::

:::: listingblock
::: content
``` highlight
public class MyBean {

    public void doSomething(@Header("JMSCorrelationID") String correlationID, @Body String body) {
        // process the inbound message here
    }

}
```
:::
::::

::: paragraph
The routing DSL then looks like this
:::

:::: listingblock
::: content
``` highlight
from("activemq:someQueue").
  to("bean:myBean");
```
:::
::::

::: paragraph
Here **myBean** would be looked up in the [Registry](registry.html) then
the body of the message would be used to try figure out what method to
call.
:::

::: paragraph
If you want to be explicit you can use:
:::

:::: listingblock
::: content
``` highlight
from("activemq:someQueue").
  to("bean:myBean?methodName=doSomething");
```
:::
::::

::: paragraph
And here we have a nifty example for you to show some great power in
Camel. You can mix and match the annotations with the normal parameters,
so we can have this example with annotations and the Exchange also:
:::

:::: listingblock
::: content
``` highlight
public class MyBean {

    public void doSomething(@Header("user") String user, @Body String body, Exchange exchange) {
        exchange.getIn().setBody(body + "MyBean");
    }

}
```
:::
::::
::::::::::::::::

::::::::::::::::::::::::::::: sect2
### Annotation Based Expression Language {#_annotation_based_expression_language}

::: paragraph
You can also use any of the [Languages](languages.html) supported in
Camel to bind expressions to method parameters when using [Bean
Integration](bean-integration.html). For example, you can use any of
these annotations:
:::

+-----------------------------------+-----------------------------------+
| Annotation                        | Description                       |
+===================================+===================================+
| `@Bean`                           | Inject a                          |
|                                   | [Bean](compone                    |
|                                   | nts:languages:bean-language.html) |
|                                   | expression                        |
+-----------------------------------+-----------------------------------+
| `@Constant`                       | Inject a                          |
|                                   | [Constant](components:            |
|                                   | languages:constant-language.html) |
|                                   | expression                        |
+-----------------------------------+-----------------------------------+
| `@Groovy`                         | Inject a                          |
|                                   | [Groovy](component                |
|                                   | s:languages:groovy-language.html) |
|                                   | expression                        |
+-----------------------------------+-----------------------------------+
| `@Header`                         | Inject a                          |
|                                   | [Header](component                |
|                                   | s:languages:header-language.html) |
|                                   | expression                        |
+-----------------------------------+-----------------------------------+
| `@Simple`                         | Inject an                         |
|                                   | [Simple](component                |
|                                   | s:languages:simple-language.html) |
|                                   | expression                        |
+-----------------------------------+-----------------------------------+
| `@XPath`                          | Inject an                         |
|                                   | [XPath](componen                  |
|                                   | ts:languages:xpath-language.html) |
|                                   | expression                        |
+-----------------------------------+-----------------------------------+

::: paragraph
The table above only list some of the commonly used languages. You can
find a list of all supported
[Languages](components:languages:index.html) which each have their own
annotation that can be used.
:::

::: paragraph
It is required to include the JAR of the language, for example
`camel-groovy`, or `camel-jsonpath` to use the `@JSonPath` annotation.
:::

::: paragraph
Here is an example how to use `@XPath`:
:::

:::: listingblock
::: content
``` highlight
public class Foo {

    @Consume("activemq:my.queue")
    public void doSomething(@XPath("/foo/bar/text()") String correlationID, @Body String body) {
        // process the inbound message here
    }

}
```
:::
::::

::::::::::::::::: sect3
#### Advanced example using \@Bean {#_advanced_example_using_bean}

::: paragraph
And an example of using the the `@Bean` binding annotation, where you
can call a [POJO](components::bean-component.html) to supply the
parameter value:
:::

:::: listingblock
::: content
``` highlight
public class MyBean {

    @Consume("activemq:my.queue")
    public void doSomething(@Bean("myCorrelationIdGenerator") String correlationID, @Body String body) {
        // process the inbound message here
    }
}
```
:::
::::

::: paragraph
When a message is consumed from the activemq queue, then Camel will
invoke the `doSomething` method. The parameter with `@Bean` is telling
Camel to call yet another bean that computes the correlation id
parameter:
:::

:::: listingblock
::: content
``` highlight
public class MyIdGenerator {

    private UserManager userManager;

    public String generate(@Header(name = "user") String user, @Body String payload) throws Exception {
       User user = userManager.lookupUser(user);
       String userId = user.getPrimaryId();
       String id = userId + generateHashCodeForPayload(payload);
       return id;
   }
}
```
:::
::::

::: paragraph
The [POJO](components::bean-component.html) MyIdGenerator has one public
method that accepts two parameters. We have also annotated this one with
the `@Header` and `@Body` annotations to help Camel know what to bind
here from the Exchange being processed.
:::

::: paragraph
Of course this could be simplified a lot if you for instance just have a
simple id generator. But we wanted to demonstrate that you can use the
[Bean Binding](bean-binding.html) annotations anywhere.
:::

:::: listingblock
::: content
``` highlight
public class MySimpleIdGenerator {

    public static int generate()  {
       // generate a unique id
       return 123;
   }
}
```
:::
::::

::: paragraph
And finally we just need to remember to have our bean registered in the
[Registry](registry.html):
:::

::: paragraph
For example in Spring XML:
:::

:::: listingblock
::: content
``` highlight
<bean id="myCorrelationIdGenerator" class="com.mycompany.MySimpleIdGenerator"/>
```
:::
::::
:::::::::::::::::

::::::: sect3
#### Example using Groovy {#_example_using_groovy}

::: paragraph
In this example we have an Exchange that has a User object stored in the
in header. This User object has methods to get some user information. We
want to use [Groovy](components:languages:groovy-language.html) to
inject an expression that extracts and concats the fullname of the user
into the fullName parameter.
:::

:::: listingblock
::: content
``` highlight
public class MyBean {

    public void doSomething(@Groovy("$request.header['user'].firstName $request.header['user'].familyName") String fullName, @Body String body) {
        // process the inbound message here
    }

}
```
:::
::::

::: paragraph
Groovy supports *GStrings* that is like a template where we can insert
`$` placeholders that will be evaluated by Groovy.
:::
:::::::
:::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
