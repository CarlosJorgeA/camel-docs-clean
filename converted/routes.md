::: {#header}
# Routes
:::

::::::::::::::::::::::::::::::::::::: {#content}
::::::::::::: {#preamble}
:::::::::::: sectionbody
::: paragraph
In Apache Camel, a *route* is a set of processing steps that are applied
to a message as it travels from a source to a destination. A route
typically consists of a series of processing steps that are connected in
a linear sequence.
:::

::: paragraph
A Camel *route* is where the integration flow is defined. For example,
you can write a Camel route to specify how two systems can be
integrated. You can also specify how the data can be manipulated,
routed, or mediated between the systems.
:::

::: paragraph
The routes are typically defined using a simple, declarative syntax that
is easy to read and understand.
:::

::: paragraph
For instance, you could write a *route* to consume files from an FTP
server and send them to an [ActiveMQ](http://activemq.apache.org)
messaging system. A *route* to do so, using [Java DSL](java-dsl.html),
would look like this:
:::

:::: listingblock
::: content
``` highlight
from("ftp:myserver/folder")
  .to("activemq:queue:cheese");
```
:::
::::

::: paragraph
Camel *routes* can be defined using a variety of [domain-specific
languages (DSLs)](dsl.html), such as Java, Spring XML, or YAML. For
example, you could write the *route* described above using XML:
:::

:::: listingblock
::: content
``` highlight
<route>
  <from uri="ftp:myserver/folder"/>
  <to uri="activemq:queue:cheese"/>
</route>
```
:::
::::
::::::::::::
:::::::::::::

:::::::::: sect1
## Writing Routes in Java using the Java DSL {#_writing_routes_in_java_using_the_java_dsl}

::::::::: sectionbody
::: paragraph
You can create a route using the Java language by extending the
[`RouteBuilder` class](manual::route-builder.html), and implementing the
`configure` method.
:::

::: paragraph
Here's an example:
:::

:::: listingblock
::: content
``` highlight
RouteBuilder builder = new RouteBuilder() {
    public void configure() {
        from("direct:a").to("direct:b");
    }
};
```
:::
::::

::: paragraph
As you can see from the code snippet above, Camel uses
[URIs](manual::uris.html) to wire endpoints together.
:::

::: paragraph
We refer to this way of writing route as using the [Java
DSL](manual::java-dsl.html).
:::
:::::::::
::::::::::

:::::::::::::: sect1
## Route Precondition {#_route_precondition}

::::::::::::: sectionbody
::: paragraph
The routes can be included or not according to the result of a test. You
can express the condition for the tests using the simple language. Camel
evaluates this condition only once during the initialization phase.
:::

::: paragraph
Here's an example that includes the route only if the parameter `format`
has been set to `xml`:
:::

:::: listingblock
::: content
``` highlight
from("direct:in").precondition("'{{format}}' == 'xml'")
   .unmarshal().jaxb()
   .to("direct:out");
```
:::
::::

::: paragraph
You can write the same route described above using the [XML
DSL](components:others:java-xml-io-dsl.html):
:::

:::: listingblock
::: content
``` highlight
<route precondition="'{{format}}' == 'xml'">
  <from uri="direct:in"/>
  <unmarshal><jaxb/></unmarshal>
  <to uri="direct:out"/>
</route>
```
:::
::::

::: paragraph
You can also write the same route described above using the [YAML
DSL](components:others:yaml-dsl.html):
:::

:::: listingblock
::: content
``` highlight
- route:
    precondition: "'{{format}}' == 'xml'"
    from:
      uri: "direct:in"
      steps:
        - unmarshal:
            jaxb: {}
        - to: "direct:out"
```
:::
::::
:::::::::::::
::::::::::::::

::::: sect1
## More Information {#_more_information}

:::: sectionbody
::: paragraph
Check the [list of supported languages](dsl.html) that you can use for
writing Camel routes.
:::
::::
:::::
:::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
