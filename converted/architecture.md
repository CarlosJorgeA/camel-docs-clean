::: {#header}
# Architecture
:::

::::::::::::::::: {#content}
::::::::: {#preamble}
:::::::: sectionbody
::: paragraph
The following diagram shows a high-level view of the main concepts that
make up Camel's architecture.
:::

:::: imageblock
::: content
![image](images/camel-architecture.png)
:::
::::

::: paragraph
At the center of the diagram you have the *heart* of Apache Camel; the
[CamelContext](camelcontext.html). The `CamelContext` is \"Camel\" ...​
the runtime Camel, that contains and holds everything together.
:::

::: paragraph
[Routes](routes.html) are defined using one of Camel's [DSLs](dsl.html).
[Processors](processor.html) are used to transform and manipulate
messages during routing as well as to implement all the
[EIP](components:eips:enterprise-integration-patterns.html)s, which have
corresponding names in the DSLs. [Components](component.html) are the
extension points in Camel for adding connectivity to other systems. To
expose these systems to the rest of Camel, components provide an
[endpoint](endpoint.html) interface.
:::
::::::::
:::::::::

::::::::: sect1
## Routes 101 {#_routes_101}

:::::::: sectionbody
::: paragraph
You use Camel for integration, and a key concept in Camel is
[routes](routes.html) which tells Camel how messages should be routed
between systems.
:::

::: paragraph
A route has exactly one input [endpoint](endpoint.html), and 0, 1 or
more output [endpoints](endpoint.html).
:::

::: paragraph
You use Camel [DSL](dsl.html) to *code* the [routes](routes.html). For
example the route below is coded in [Java DSL](java-dsl.html):
:::

:::: listingblock
::: content
``` highlight
public class MyRoute extends RouteBuilder {

    public void configure() throws Exception {
        from("ftp:myserver/folder")
            .to("activemq:queue:cheese");
    }
}
```
:::
::::
::::::::
:::::::::
:::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
