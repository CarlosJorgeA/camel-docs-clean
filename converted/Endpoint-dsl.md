::: {#header}
# Endpoint DSL
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
::::::: {#preamble}
:::::: sectionbody
::: paragraph
Endpoint-DSL is a builder API that allows using type-safe endpoint
[URL](uris.html) configurations.
:::

::: paragraph
The Endpoint DSL is exclusively available as part of the Java DSL.
:::

::: paragraph
The DSL can be accessed in several ways, but the main one is to switch
to using an `EndpointRouteBuilder` instead of the usual `RouteBuilder`.
This builder provides access to all of Camel endpoint builders which are
defined through inheritance on the
`org.apache.camel.builder.endpoint.EndpointRouteBuilder`.
:::
::::::
:::::::

::::::::::::::::::::::::::::::::::::::::: sect1
## Using Endpoint DSL {#_using_endpoint_dsl}

:::::::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
The following is an example of an FTP route using the standard
`RouteBuilder` Java DSL:
:::

:::: listingblock
::: content
``` highlight
public class MyRoutes extends RouteBuilder {
    @Override
    public void configure() {
        from("ftp://foo@myserver?password=secret&recursive=true&" +
                "ftpClient.dataTimeout=30000&" +
                "ftpClientConfig.serverLanguageCode=fr")
                .to("bean:doSomething");
    }
}
```
:::
::::

::: paragraph
The same Java statement can be rewritten in the following more type-safe
and readable way using the new `EndpointRouteBuilder` that allows using
the Endpoint-DSL:
:::

:::: listingblock
::: content
``` highlight
public class MyRoutes extends EndpointRouteBuilder {
    @Override
    public void configure() throws Exception {
        from(ftp("myserver").account("foo").password("secret").recursive(true)
            .advanced()
                .ftpClientParameters(Collections.singletonMap("dataTimeout", 30000))
                .ftpClientConfig(Collections.singletonMap("serverLanguageCode", "fr")))
            .to(bean("something"));
    }
}
```
:::
::::

:::: sect2
### Type Safety {#_type_safety}

::: paragraph
Similar to the [Component DSL](component-dsl.html), uses the meta-model,
which is extracted from the source and written in various JSON files, to
generate a fluent DSL for each component. This fluent DSL provides type
safety for parameters.
:::
::::

::::::::: sect2
### Using custom component names {#_using_custom_component_names}

::: paragraph
The Endpoint-DSL uses the default name of the
[Component](component.html), so in the example above the name is `ftp`.
There can be use-cases where you may have multiple Camel
[components](components::index.html) of the same type registered with
different names. An example is if you have two JMS broker systems (for
example ActiveMQ and WebSphereMQ).
:::

::: paragraph
Then you can set up two Camel JMS components with unique names such as:
`myAMQ` and `myWMQ`
:::

::: paragraph
The Endpoint-DSL can use these names with the `jms` fluent builder as
shown:
:::

:::: listingblock
::: content
``` highlight
from(jms("myWMQ", "cheese").concurrentConsumers(5))
    .to(jms("myAMQ", "smelly"));
```
:::
::::

::: paragraph
Notice how we can refer to their names as the first parameter in the
`jms` fluent builder. The example would then consume messages from
WebSphereMQ queue named cheese and route to ActiveMQ on a queue named
smelly.
:::
:::::::::

::::::: sect2
### Headers\' name {#_headers_name}

::: paragraph
The endpoint-dsl can also be used to be assisted when selecting the name
of a header to set or to get. The headers\' name builder is accessible
directly from the method of the class `EndpointRouteBuilder` without
argument whose name is the scheme of the target component.
:::

::: paragraph
In the example below the method `file()` available from
`EndpointRouteBuilder`, gives access to the methods corresponding to the
name of the headers of the file component. Here the method `fileName()`
is called to get the name of the header for the name of the file.
:::

:::: listingblock
::: content
``` highlight
public class MyRoutes extends EndpointRouteBuilder {
    @Override
    public void configure() {
        from(/*some endpoint*/)
            // Some route start
            .setHeader(file().fileName(), constant("foo.txt"))
            // Some route end
            ;
    }
}
```
:::
::::
:::::::

::::::::::::::::::: sect2
### Using Endpoint-DSL outside route builders {#_using_endpoint_dsl_outside_route_builders}

::: paragraph
You can use the type-safe endpoint-dsl outside route builders with:
:::

::: ulist
- With the `FluentProducerTemplate` to send messages

- Creating an `Endpoint`
:::

::: paragraph
For example to send a message to Kafka you can use the
`FluentProducerTemplate`
:::

:::: listingblock
::: content
``` highlight
import static org.apache.camel.builder.endpoint.StaticEndpointBuilders.kafka;

context.createFluentProducerTemplate()
    .to(kafka("start").clientId("myClient").brokers("{{myBrokers}}").partitionKey("myKey"))
    .withBody("Hello World")
    .send();
```
:::
::::

::: paragraph
To use the endpoint-dsl with kafka you need to static import `kafka`
from the class:
`org.apache.camel.builder.endpoint.StaticEndpointBuilders` which has all
the Camel components.
:::

::: paragraph
An endpoint can also be created in Java code via the endpoint-dsl as
shown:
:::

:::: listingblock
::: content
``` highlight
import static org.apache.camel.builder.endpoint.StaticEndpointBuilders.paho;

Endpoint mqtt = paho("sensor").clientId("myClient").userName("scott").password("tiger")
    .resolve(context);
```
:::
::::

::: paragraph
You can then set all the options via the type-safe DSL and then the
endpoint can be resolved (created) by calling `resolve` with
`CamelContext` as parameter.
:::

::: paragraph
If you want to inject an endpoint into your POJO or `RouteBuilder` class
using endpoint-dsl, then this can be done similar to the previous
example, but with one important difference:
:::

:::: listingblock
::: content
``` highlight
import org.apache.camel.BindToRegistry;
import org.apache.camel.EndpointInject;
import org.apache.camel.Produce;
import org.apache.camel.builder.EndpointProducerBuilder;
import static org.apache.camel.builder.endpoint.StaticEndpointBuilders.paho;

@BindToRegistry
public class MyPojo {

    @Produce
    private FluentProducerTemplate producer;

    private final EndpointProducerBuilder mqtt = paho("sensor").clientId("myClient").userName("scott").password("tiger");

    public void sendToSensor(String data) {
        producer.withBody(data).to(mqtt).send();
    }

}
```
:::
::::

::: paragraph
The `MyPojo` is a class that with standalone Camel can be discovered and
registered with the `@BindToRegistry` annotation. If you use Spring
Boot, or Quarkus etc then you should use their kind of annotations for
this.
:::

::: paragraph
That's not the point of this example, it is the Endpoint DSL to
configure the MQTT endpoint (camel-paho). The endpoint is configured the
same way as before by the type-safe endpoint-dsl by static importing the
`paho`, and then use its fluent builder methods to configure. Notice how
the returned type is `EndpointProducerBuilder`. That type is not an
`Endpoint` instance as it's not resolved (i.e. it's only a builder for
an endpoint). This means that the builder can be code and compiled
before `CamelContext` is created and started.
:::

::: paragraph
At runtime, we want to use this endpoint to send messages to MQTT
server; this is done in the `sendToSensor` method where we are using
`FluentProducerTemplate` that is capable of using the
`EndpointProducerBuilder` as the endpoint in shown with: `.to(mqtt)`.
:::
:::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::

:::::::: sect1
## Dependency {#_dependency}

::::::: sectionbody
::: paragraph
Maven users will need to add the following dependency to their `pom.xml`
for this component:
:::

::::: listingblock
::: title
pom.xml
:::

::: content
``` highlight
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-endpointdsl</artifactId>
    <version>x.x.x</version>
</dependency>
```
:::
:::::
:::::::
::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
