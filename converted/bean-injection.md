::: {#header}
# Bean Injection
:::

::::::::::::::::::::::::: {#content}
:::::: {#preamble}
::::: sectionbody
::: paragraph
We support the injection of various resources using `@EndpointInject` or
`@BeanInject`. This can be used to inject
:::

::: ulist
- [Endpoint](endpoint.html) instances which can be used for testing when
  used with [Mock](components::mock-component.html) endpoints; see the
  [Testing](testing.html) for an example.

- [ProducerTemplate](producertemplate.html) instances for [POJO
  Producing](pojo-producing.html)

- client side proxies for [POJO Producing](pojo-producing.html)
:::
:::::
::::::

:::::::::::::: sect1
## Using \@BeanInject {#_using_beaninject}

::::::::::::: sectionbody
::: paragraph
You can inject beans (obtained from the [Registry](registry.html)) into
your beans such as `RouteBuilder` classes.
:::

::: paragraph
For example to inject a bean named foo, you can enlist the bean in the
[Registry](registry.html) such as in a Spring XML file:
:::

:::: listingblock
::: content
``` highlight
<bean id="foo" class="com.foo.MyFooBean"/>
```
:::
::::

::: paragraph
And then in a Java `RouteBuilder` class, you can inject the bean using
`@BeanInject` as shown below:
:::

:::: listingblock
::: content
``` highlight
public class MyRouteBuilder extends RouteBuilder {

   @BeanInject("foo")
   MyFooBean foo;

   public void configure() throws Exception {
     ..
   }
}
```
:::
::::

::: paragraph
If you omit the name, then Camel does a lookup by type, and injects the
bean if there is exactly only one bean of that type enlisted in the
[Registry](registry.html).
:::

:::: listingblock
::: content
``` highlight
   @BeanInject
   MyFooBean foo;
```
:::
::::
:::::::::::::
::::::::::::::

::::: sect1
## Bean Injection with Quarkus {#_bean_injection_with_quarkus}

:::: sectionbody
::: paragraph
When using Camel with Spring Boot, or Quarkus, then the `@Inject`, or
`@Named` annotations can be used to inject Camel resources as well.
:::
::::
:::::

::::: sect1
## Bean Injection with Spring Boot {#_bean_injection_with_spring_boot}

:::: sectionbody
::: paragraph
Camel has first-class support for Spring Boot, and you can use the
Spring annotations such as `@Autowired` to also inject Camel resources.
:::
::::
:::::
:::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
