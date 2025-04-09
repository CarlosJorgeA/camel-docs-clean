::: {#header}
# RouteBuilder
:::

::::::::::::::: {#content}
::::: {#preamble}
:::: sectionbody
::: paragraph
The `RouteBuilder` is a base class which is derived from to create
routing rules using the DSL. Instances of `RouteBuilder` are then added
to the `CamelContext`.
:::
::::
:::::

:::::::: sect1
## RouteBuilder example {#_routebuilder_example}

::::::: sectionbody
::: paragraph
The following shows an example of a `RouteBuilder`:
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
:::::::
::::::::

::::: sect1
## More Information {#_more_information}

:::: sectionbody
::: paragraph
See more in [DSL](dsl.html), [Java DSL](java-dsl.html) and
[Routes](routes.html).
:::
::::
:::::
:::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
