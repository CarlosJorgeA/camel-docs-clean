::: {#header}
# Java DSL
:::

::::::::::::::::::::::::::::::::::::::::::::: {#content}
:::::: {#preamble}
::::: sectionbody
::: paragraph
Apache Camel offers a Java-based DSL.
:::

::: paragraph
In the Java DSL you create a route by extending the [`RouteBuilder`
class](manual::route-builder.html), and implementing the `configure`
method.
:::
:::::
::::::

::::::::::::::::::::::::: sect1
## Java DSL example {#_java_dsl_example}

:::::::::::::::::::::::: sectionbody
::: paragraph
This is best illustrated by an example. In the code below we create a
new class called `MyRouteBuilder` that extends the
`org.apache.camel.builder.RouteBuilder` from Camel.
:::

::: paragraph
In the `configure` method the Java DSL is at our disposal.
:::

:::: listingblock
::: content
``` highlight
import org.apache.camel.builder.RouteBuilder;

/**
 * A Camel Java DSL Router
 */
public class MyRouteBuilder extends RouteBuilder {

    /**
     * Let's configure the Camel routing rules using Java code...
     */
    public void configure() {

        // here is a sample which processes the input files
        // (leaving them in place - see the 'noop' flag)
        // then performs content based routing on the message using XPath
        from("file:src/data?noop=true")
            .choice()
                .when(xpath("/person/city = 'London'"))
                    .to("file:target/messages/uk")
                .otherwise()
                    .to("file:target/messages/others");
    }

}
```
:::
::::

::: paragraph
In the `configure` method we can define Camel [Routes](routes.html).
:::

::: paragraph
In the example above we have a single route, which pickup files (the
`from`).
:::

:::: listingblock
::: content
``` highlight
from("file:src/data?noop=true")
```
:::
::::

::: paragraph
Then we use the [Content-Based Router](components:eips:choice-eip.html)
EIP (the `choice`) to route the message whether the person is from
London or not.
:::

:::: listingblock
::: content
``` highlight
.choice()
    .when(xpath("/person/city = 'London'"))
        .to("file:target/messages/uk")
    .otherwise()
        .to("file:target/messages/others");
```
:::
::::

:::::: sect2
### Using Text Blocks for long URIs {#_using_text_blocks_for_long_uris}

::: paragraph
If you have very long endpoint [uris](uris.html), then you can declare
those in Java text blocks, instead of breaking a String into multiple
added elements:
:::

:::: listingblock
::: content
``` highlight
    from("""
            debezium-postgres:customerEvents
            ?databasePassword={{myPassword}}
            &databaseDbname=myDB
            &databaseHostname=myHost
            &pollIntervalMs=2000
            &queryFetchSize=100
        """)
        .to("kafka:cheese");
```
:::
::::
::::::

:::::::: sect2
### Routes using Java lambda style {#_routes_using_java_lambda_style}

::: paragraph
Camel now supports to define Camel routes in Java DSL using Lambda
style. This can be beneficial for microservices or serverless where you
may want to quickly define a few routes.
:::

::: paragraph
For example, using lambda style you can define a Camel route that takes
messages from Kafka and send to JMS in a single line of code:
:::

:::: listingblock
::: content
``` highlight
rb -> rb.from("kafka:cheese").to("jms:queue:foo");
```
:::
::::

::: paragraph
There is a bit more to this as the lambda route must be coded in a Java
method that returns an instance of `LambdaRouteBuilder`. See more at the
[LambdaRouteBuilder](lambda-route-builder.html) documentation.
:::
::::::::
::::::::::::::::::::::::
:::::::::::::::::::::::::

:::::::::::::: sect1
## More Details {#_more_details}

::::::::::::: sectionbody
::: paragraph
For more details see [DSL](dsl.html), [Routes](routes.html), and
[Processor](processor.html).
:::

::::::::::: sect2
### The Java DSL under the hood {#_the_java_dsl_under_the_hood}

::: paragraph
As mentioned in the Getting Started guide, you can use Camel's Java DSL
in a way that almost looks like a DSL. For instance:
:::

::: paragraph
**Note**: comments afterward explain some of the constructs used in the
example.
:::

::::: listingblock
::: title
**Example of Camel's \"Java DSL\"**
:::

::: content
``` highlight
RouteBuilder builder = new RouteBuilder() {
    public void configure() {
        from("queue:a").filter(header("foo").isEqualTo("bar")).to("queue:b");

        from("queue:c").choice()
                .when(header("foo").isEqualTo("bar")).to("queue:d")
                .when(header("foo").isEqualTo("cheese")).to("queue:e")
                .otherwise().to("queue:f");
    }
};
CamelContext myCamelContext = new DefaultCamelContext();
myCamelContext.addRoutes(builder);
```
:::
:::::

::: paragraph
The first line in the above example creates an object which is an
instance of an anonymous subclass of `RouteBuilder` with the specified
`configure()` method.
:::

::: paragraph
The `CamelContext.addRoutes(RouterBuilder builder)` method invokes
`builder.setContext(this)` -- so the `RouteBuilder` object knows which
`CamelContext` object it is associated with -- and then invokes
`builder.configure()`. The body of `configure()` invokes methods such as
`from()`, `filter()`, `choice()`, `when()`, `isEqualTo()`, `otherwise()`
and `to()`.
:::

::: paragraph
The `RouteBuilder.from(String uri)` method invokes `getEndpoint(uri)` on
the `CamelContext` associated with the `RouteBuilder` object to get the
specified `Endpoint` and then puts a `FromBuilder` *wrapper* around this
`Endpoint`. The `FromBuilder.filter(Predicate predicate)` method creates
a `FilterProcessor` object for the `Predicate` (that is, condition)
object built from the `header("foo").isEqualTo("bar")` expression. In
this way, these operations incrementally build up a `Route` object (with
a `RouteBuilder` wrapper around it) and add it to the `CamelContext`
instance associated with the `RouteBuilder`.
:::
:::::::::::
:::::::::::::
::::::::::::::

::::: sect1
## More Information {#_more_information}

:::: sectionbody
::: paragraph
See [Lambda Route Builder](lambda-route-builder.html) for creating a
routing rule using the DSL, using Java lambda style.
:::
::::
:::::
:::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
