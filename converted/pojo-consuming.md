::: {#header}
# POJO Consuming
:::

::::::::::::::::::::::::::::::::::::: {#content}
:::::::::::::: {#preamble}
::::::::::::: sectionbody
::: paragraph
To consume a message you use the `@Consume` annotation to mark a
particular method of a bean as being a consumer method. The value of the
annotation defines the Camel [Endpoint](endpoint.html) to consume from.
:::

::: {.admonitionblock .important}
+-----------------------------------+-----------------------------------+
| ::: title                         | The `@Consume` POJO annotations   |
| Important                         | are not part of any Camel routes, |
| :::                               | and you cannot use errorHandler   |
|                                   | or onException with that.         |
+-----------------------------------+-----------------------------------+
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

::: paragraph
For example lets invoke the `onCheese()` method with the String body of
the inbound JMS message from ActiveMQ on the cheese queue; this will use
the [Type Converter](type-converter.html) to convert the JMS
ObjectMessage or BytesMessage to a String - or just use a TextMessage
from JMS
:::

:::: listingblock
::: content
``` highlight
public class Foo {

  @Consume("activemq:cheese")
  public void onCheese(String name) {
    // do something here
  }
}
```
:::
::::

::: paragraph
The [Bean Binding](bean-binding.html) is then used to convert the
inbound [Message](components:eips:message.html) to the parameter list
used to invoke the method .
:::

::: paragraph
This basically creates a route that looks kinda like this:
:::

:::: listingblock
::: content
``` highlight
from(uri).bean(theBean, "methodName");
```
:::
::::
:::::::::::::
::::::::::::::

:::::::::::::::::::::::: sect1
## Using a property to define the endpoint {#_using_a_property_to_define_the_endpoint}

::::::::::::::::::::::: sectionbody
::: paragraph
The following annotations `@Consume`, `@Produce`, `@EndpointInject`, now
offers a `property` attribute you can use to define the endpoint as a
property on the bean. Then Camel will use the getter method to access
the property.
:::

::: paragraph
For example:
:::

:::: listingblock
::: content
``` highlight
public class MyService {
  private String serviceEndpoint;

  public void setServiceEndpoint(String uri) {
     this.serviceEndpoint = uri;
  }

  public String getServiceEndpoint() {
     return serviceEndpoint;
  }

  @Consume(property = "serviceEndpoint")
  public void onService(String input) {
     // do something
  }
}
```
:::
::::

::: paragraph
The bean `MyService` has a property named `serviceEndpoint` which has
getter/setter for the property. Now we want to use the bean for [POJO
Consuming](#), and hence why we use `@Consume` in the `onService`
method. Notice how we use the `property = "serviceEndpoint` to configure
the property that has the endpoint url.
:::

::: paragraph
If you define the bean in Spring XML, then you can configure the
property as follows:
:::

:::: listingblock
::: content
``` highlight
<bean id="myService" class="com.foo.MyService">
  <property name="serviceEndpoint" value="activemq:queue:foo"/>
</bean>
```
:::
::::

::: paragraph
This allows you to configure the bean without with any dependency
injection style.
:::

::::::::::::: sect2
### Advanced use with property naming convention {#_advanced_use_with_property_naming_convention}

::: paragraph
Camel offers a naming convention which allows you to not have to
explicit name the property. Camel uses this algorithm to find the getter
method. The method must be a `getXXX` method.
:::

::: {.olist .arabic}
1.  Use the property name if explicit given

2.  If no property name was configured, then use the method name

3.  Try to get the property with name**Endpoint** (eg with Endpoint as
    postfix)

4.  Try to get the property with the name as is (eg no postfix or
    postfix)

5.  If the property name starts with **on** then omit that, and try step
    3 and 4 again.
:::

::: paragraph
So in the example above, we could have defined the `@Consume` annotation
as:
:::

:::: listingblock
::: content
``` highlight
  @Consume(property = "service")
  public void onService(String input) {
```
:::
::::

::: paragraph
Now the property is named \"service\" which then would match step 3 from
the algorithm, and have Camel invoke the `getServiceEndpoint` method.
:::

::: paragraph
We could also have omitted the property attribute, to make it implicit
:::

:::: listingblock
::: content
``` highlight
  @Consume
  public void onService(String input) {
```
:::
::::

::: paragraph
Now Camel matches step 5, and loses the prefix **on** in the name, and
looks for \'service\' as the property. And because there is a
`getServiceEndpoint` method, Camel will use this method.
:::
:::::::::::::
:::::::::::::::::::::::
::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
