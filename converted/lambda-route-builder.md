::: {#header}
# LambdaRouteBuilder
:::

::::::::::::::::::::::::::: {#content}
:::::::: {#preamble}
::::::: sectionbody
::: paragraph
The `LambdaRouteBuilder` is a functional interface which is used for
creating a routing rule using the [DSL](dsl.html), using Java lambda
style.
:::

:::: listingblock
::: content
``` highlight
rb -> rb.from("timer:foo").log("Hello Lambda");
```
:::
::::

::: paragraph
Instances of `LambdaRouteBuilder` are discovered and transformed into
`RouteBuilder` instances which are added to the CamelContext.
:::
:::::::
::::::::

::::::::: sect1
## Usage {#_usage}

:::::::: sectionbody
::: paragraph
To use `LambdaRouteBuilder` you need to create a method that returns
`LambdaRouteBuilder` which then allows to use Java lambda style to
define a single route.
:::

::: paragraph
In the example below the method `myRoute` is used to create a Camel
route that consumes from Kafka and sends the messages to JMS.
:::

::: paragraph
To make the route discoverable by Camel during startup, then the method
must be annotated. The method should be annotated with `@BindToRegistry`
in standalone mode with `camel-main`, `@Bean` in case of Spring Boot or
`@Produce` in case of Quarkus.
:::

:::: listingblock
::: content
``` highlight
public class MyConfiguration {
    @BindToRegistry
    public LambdaRouteBuilder myRoute() {
        return rb -> rb.from("kafka:cheese").to("jms:queue:foo");
    }
}
```
:::
::::
::::::::
:::::::::

::::::::::::: sect1
## LambdaEndpointRouteBuilder {#_lambdaendpointroutebuilder}

:::::::::::: sectionbody
::: paragraph
The [Endpoint DSL](Endpoint-dsl.html) can also be used as a lambda route
builder with the
`org.apache.camel.builder.endpoint.LambdaEndpointRouteBuilder` class
from the `camel-endpointdsl` JAR.
:::

:::: listingblock
::: content
``` highlight
public class MyConfiguration {
    @BindToRegistry
    public LambdaEndpointRouteBuilder myRoute() {
        return rb -> rb.from(rb.kafka("cheese")).to(rb.jms("queue:foo"));
    }
}
```
:::
::::

::: paragraph
The `LambdaEndpointRouteBuilder` has *type safe* endpoint but requires
to prefix with the instance name (`rb`) when choosing an endpoint name.
Notice above how to select the kafka endpoint
:::

:::: listingblock
::: content
``` highlight
rb.from(rb.kafka("cheese"))
```
:::
::::

::: paragraph
With the regular `LambdaRouteBuilder` it's just a `String` type, so the
`rb` prefix is not needed anymore:
:::

:::: listingblock
::: content
``` highlight
rb.from("kafka:cheese")
```
:::
::::
::::::::::::
:::::::::::::
:::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
