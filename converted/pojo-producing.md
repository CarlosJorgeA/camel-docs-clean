::: {#header}
# POJO producing
:::

::::::::::::::::::::::::::: {#content}
::::::: {#preamble}
:::::: sectionbody
::: paragraph
There are two different ways to send messages to any Camel
[Endpoint](endpoint.html) from a POJO:
:::

::: ulist
- Using `@Produce` or `@EndpointInject`

- Or to hide using an interface
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | ::: paragraph                     |
| Note                              | The following steps use the       |
| :::                               | ActiveMQ component which is not   |
|                                   | yet supported on Camel 4.         |
|                                   | :::                               |
+-----------------------------------+-----------------------------------+
:::
::::::
:::::::

::::::::::: sect1
## Using \@Produce {#_using_produce}

:::::::::: sectionbody
::: paragraph
To allow sending of messages from POJOs you can use the `@Produce`
annotation. This will inject a `org.apache.camel.ProducerTemplate` so
that the bean can send messages.
:::

::: {.admonitionblock .important}
+-----------------------------------+-----------------------------------+
| ::: title                         | The `@Produce` POJO annotations   |
| Important                         | are not part of any Camel routes, |
| :::                               | and you cannot use errorHandler   |
|                                   | or onException with that.         |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
For example to send a message to the foo queue on ActiveMQ:
:::

:::: listingblock
::: content
``` highlight
public class Foo {
  @Produce("activemq:foo")
  ProducerTemplate producer;

  public void doSomething() {
    if (whatever) {
      producer.sendBody("<hello>world!</hello>");
    }
  }
}
```
:::
::::

::: paragraph
The downside of this is that your code is now dependent on a Camel API,
the `ProducerTemplate`. The next section describes how to remove this
dependency.
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | See [POJO                         |
| Tip                               | Consuming](pojo-consuming.html)   |
| :::                               | for how to use a property on the  |
|                                   | bean as endpoint configuration,   |
|                                   | e.g., using the `property`        |
|                                   | attribute on `@Produce` or        |
|                                   | `@EndpointInject`.                |
+-----------------------------------+-----------------------------------+
:::
::::::::::
:::::::::::

:::::::::::: sect1
## Hiding the Camel APIs From Your Code {#_hiding_the_camel_apis_from_your_code}

::::::::::: sectionbody
::: paragraph
You can hide Camel APIs from your application code. You can add the
`@Produce` annotation to an injection point (a field or property setter)
using some interface you use in your business logic. Example:
:::

:::: listingblock
::: content
``` highlight
public interface MyListener {
    // this method is request/reply (InOut) because the method has a return value
    // to use one way (InOnly) then the method should be a void method
    String sayHello(String name);
}

public class MyBean {
    @Produce("activemq:foo")
    protected MyListener producer;

    public void doSomething() {
        // lets send a message and get a response back
        String response = producer.sayHello("James");
    }
}
```
:::
::::

::: paragraph
Here Camel will automatically inject a smart client side proxy at the
`@Produce` annotation - an instance of the `MyListener` interface.
:::

::: paragraph
When we invoke methods on this interface the method call is turned into
an object and is sent to the endpoint; in this case the ActiveMQ
endpoint to queue **`foo`**. Because the `sayHello` method has a return
type (`String`) then Camel will use [Request
Reply](components:eips:requestReply-eip.html) (InOut) messaging.
:::

:::: listingblock
::: content
``` highlight
public interface MyListener {
    void sayHello(String name);
}
```
:::
::::

::: paragraph
If the method is a `void` method, then Camel will use [Event
Message](components:eips:event-message.html) (InOnly) messaging.
:::
:::::::::::
::::::::::::
:::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
