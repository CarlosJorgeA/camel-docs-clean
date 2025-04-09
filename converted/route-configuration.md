::: {#header}
# Route Configuration
:::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
::::::::: {#preamble}
:::::::: sectionbody
::: paragraph
Camel 3.12 introduces route configuration which is used for separating
configurations from the routes. This can be used in situations such as
configuring different error handling across a set of routes. In previous
versions of Camel, this was more cumbersome to do, as you would either
have to copy the same configuration to a set of routes or rely on global
error handling configuration.
:::

::: paragraph
Now you can configure a number of route configurations, and then specify
on each route which configuration to use (you can use match by ids,
wildcards, and regular expression).
:::

::: paragraph
The route configuration is supported by all DSLs, so usable by: Java,
XML, Groovy and so forth.
:::

::: paragraph
In the route configuration, you can set up common strategies for:
:::

::: ulist
- [Error Handler](error-handler.html)

- [OnException](exception-clause.html)

- [OnCompletion](oncompletion.html)

- [Intercept](components:eips:intercept.html)
:::
::::::::
:::::::::

:::::::::::::::::::::::::::::::::::: sect1
## Route Configuration Builder in Java DSL {#_route_configuration_builder_in_java_dsl}

::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
With Java DSL you can use `RouteConfigurationBuilder` to specify the
configuration as shown below. The builder is similar to `RouteBuilder`
so its use is familiar.
:::

:::: listingblock
::: content
``` highlight
public class MyJavaErrorHandler extends RouteConfigurationBuilder {

    @Override
    public void configuration() throws Exception {
        routeConfiguration("javaError")
            .onException(Exception.class).handled(true)
            .log("Java WARN: ${exception.message}");
    }
}
```
:::
::::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | The `RouteConfigurationBuilder`   |
| Note                              | uses `configuration` as the       |
| :::                               | method where the configuration is |
|                                   | coded. This is on purpose, so as  |
|                                   | not to use the `configure` method |
|                                   | which the regular Java DSL        |
|                                   | `RouteBuilder` uses for coding    |
|                                   | Camel routes.                     |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
In the example above, then there is only one route configuration that
has been assigned the ID *`javaError`*. This ID allows us to refer to
this configuration later when you want to assign which routes are using
the configuration.
:::

::: paragraph
This configuration is a basic configuration that just catches and
handles all exceptions and logs a WARN message.
:::

:::::::::::::::::::: sect2
### Assigning route configurations to routes {#_assigning_route_configurations_to_routes}

::: paragraph
To use this configuration in your routes, then you can assign it with
`routeConfigurationId` as shown:
:::

:::: listingblock
::: content
``` highlight
public class MyJavaRouteBuilder extends RouteBuilder {

    @Override
    public void configure() throws Exception {
        from("timer:java?period=2s")
            // refer to the route configuration by the id to use for this route
            .routeConfigurationId("javaError")
            .setBody(method(MyJavaRouteBuilder.class, "randomNumber"))
            .log("Random number ${body}")
            .filter(simple("${body} < 30"))
                .throwException(new IllegalArgumentException("The number is too low"));
    }

    public static int randomNumber() {
        return new Random().nextInt(100);
    }
}
```
:::
::::

::: paragraph
In the `routeConfigurationId` the configuration to use is specified by
the ID, eg *`javaError`*.
:::

::: paragraph
Multiple configurations can be assigned (separated by comma), such as:
:::

:::: listingblock
::: content
``` highlight
.routeConfigurationId("javaError,myAudit")
```
:::
::::

::: paragraph
The route configuration supports matching by:
:::

::: ulist
- exact ID name. This is the sample we have seen above.

- wildcard

- regular expression.
:::

::: paragraph
Wildcards are text ending with a `*`. They are matched when the
configuration ID starts with the specified text followed by any
characters. For instance, you can do:
:::

:::: listingblock
::: content
``` highlight
.routeConfigurationId("java*,myAudit")
```
:::
::::

::: paragraph
Here we use wildcard in *`java*`* which means any configuration whose ID
starts with java is a match.
:::

::: paragraph
Match by regular expression is just like match by wildcard but using
regex instead.
:::

:::: listingblock
::: content
``` highlight
.routeConfigurationId(".*error.*")
```
:::
::::

::: paragraph
Here we want to match any configuration whose ID contains *`error`*.
:::
::::::::::::::::::::

:::::::: sect2
### Adding route configurations to CamelContext {#_adding_route_configurations_to_camelcontext}

::: paragraph
Because a `RouteConfigurationBuilder` is also a `RouteBuilder` then you
add route configurations the same way for `RouteBuilder` such as using
the API on `CamelContext`
:::

:::: listingblock
::: content
``` highlight
CamelContext context = ...
// add the route configuration
context.addRoutes(new MyJavaErrorHandler());
// add the regular route
context.addRoutes(new MyJavaRouteBuilder());
```
:::
::::

::: paragraph
If you use Spring Boot, then your Camel routes and route configurations
can be auto-discovered by the spring boot component scanning. This
requires adding the `@Component` annotation to the class.
:::

::: paragraph
See the example
[camel-spring-boot-examples/routes-configuration](https://github.com/apache/camel-spring-boot-examples/tree/main/routes-configuration).
:::
::::::::

:::: sect2
### Route configuration with Endpoint DSL {#_route_configuration_with_endpoint_dsl}

::: paragraph
The [Endpoint DSL](Endpoint-dsl.html) can also be used for route
configurations. This requires adding `camel-endpointdsl` to the
classpath, and then using
`org.apache.camel.builder.endpoint.EndpointRouteConfigurationBuilder`,
which offers the *type safe* DSL for Camel endpoints.
:::
::::
:::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::

:::::::::::::::::::: sect1
## Default route configurations {#_default_route_configurations}

::::::::::::::::::: sectionbody
::: paragraph
Route configurations are either given an explicit unique ID, or the
configuration is *nameless*. A *nameless* configuration is used as
default/fallback configuration, for routes which have **NOT** been
explicitly assigned route configurations.
:::

::: paragraph
Suppose you have one *nameless* configuration and another named
*`retryError`*:
:::

:::: listingblock
::: content
``` highlight
public class MyJavaErrorHandler extends RouteConfigurationBuilder {

    @Override
    public void configuration() throws Exception {
        routeConfiguration()
            .onException(Exception.class).handled(true)
            .log("WARN: ${exception.message}");

        routeConfiguration("retryError")
            .onException(Exception.class).maximumRedeliveries(5);
    }
}
```
:::
::::

::: paragraph
And the following two routes:
:::

:::: listingblock
::: content
``` highlight
   from("file:cheese").routeId("cheese")
        .to("kafka:cheese");

   from("file:beer").routeId("beer")
        .routeConfigurationId("retryError")
        .to("jms:beer");
```
:::
::::

::: paragraph
In the example above, the *`cheese`* route has no route configurations
assigned, so the route will use the default configuration, which in case
of an exception will log a warning.
:::

::: paragraph
The *`beer`* route on the other hand has the route configuration
*`retryError`* assigned, and this configuration will in case of an
exception retry up to five times and then if still an error then fail
and rollback.
:::

::: paragraph
If you add more routes, then those routes can also be assigned the
*`retryError`* configuration if they should also retry in case of error.
:::

:::::::: sect2
### Route Configuration with Error Handler {#_route_configuration_with_error_handler}

::: paragraph
Each route configuration can also have a specific error handler
configured, as shown below:
:::

:::: listingblock
::: content
``` highlight
public class MyJavaErrorHandler extends RouteConfigurationBuilder {

    @Override
    public void configuration() throws Exception {
        routeConfiguration()
            .errorHandler(deadLetterChannel("mock:dead"));

        routeConfiguration("retryError")
            .onException(Exception.class).maximumRedeliveries(5);
    }
}
```
:::
::::

::: paragraph
In the example above, the *`nameless`* configuration has an error
handler with a dead letter queue. And the route configuration with id
*`retryError`* does not, and instead it will attempt to retry the
failing message up till five times before giving up (exhausted). Because
this route configuration does not have any error handler assigned, then
Camel will use the default error handler.
:::

::: {.admonitionblock .important}
+-----------------------------------+-----------------------------------+
| ::: title                         | Routes that have a local error    |
| Important                         | handler defined, will always use  |
| :::                               | this error handler, instead of    |
|                                   | the error handler from route      |
|                                   | configurations. A route can only  |
|                                   | have one error handler.           |
+-----------------------------------+-----------------------------------+
:::
::::::::
:::::::::::::::::::
::::::::::::::::::::

::::::::::: sect1
## Route Configuration in XML {#_route_configuration_in_xml}

:::::::::: sectionbody
::: paragraph
When using XML DSL, then you can code your route configurations in XML
files as shown below:
:::

:::: listingblock
::: content
``` highlight
<routeConfiguration id="xmlError">
    <onException>
        <exception>java.lang.Exception</exception>
        <handled><constant>true</constant></handled>
        <log message="XML WARN: ${exception.message}"/>
    </onException>
</routeConfiguration>
```
:::
::::

::: paragraph
And in the XML routes you can assign which configurations to use:
:::

:::: listingblock
::: content
``` highlight
<route routeConfigurationId="xmlError">
    <from uri="timer:xml?period=5s"/>
    <log message="I am XML"/>
    <throwException exceptionType="java.lang.Exception" message="Some kind of XML error"/>
</route>
```
:::
::::

::: paragraph
In this example, the route is assigned the *`xmlError`* route
configuration by the exact ID.
:::
::::::::::
:::::::::::

::::::::::: sect1
## Route Configuration in YAML {#_route_configuration_in_yaml}

:::::::::: sectionbody
::: paragraph
When using YAML DSL, then you can code your route configurations in YAML
files as shown below:
:::

:::: listingblock
::: content
``` highlight
- route-configuration:
    id: "yamlError"
    on-exception:
    - on-exception:
        handled:
          constant: "true"
        exception:
          - "java.lang.Exception"
        steps:
          - log:
              message: "YAML WARN ${exception.message}"
```
:::
::::

::: paragraph
And in the YAML routes you can assign which configurations to use:
:::

:::: listingblock
::: content
``` highlight
- route:
    # refer to the route configuration by the id to use for this route
    route-configuration-id: "yamlError"
    from:
      uri: "timer:yaml?period=3s"
      steps:
        - set-body:
            simple: "Timer fired ${header.CamelTimerCounter} times"
        - to:
            uri: "log:yaml"
            parameters:
              show-body-type: false
              show-exchange-pattern: false
        - throw-exception:
            exception-type: "java.lang.IllegalArgumentException"
            message: "Error from yaml"
```
:::
::::

::: paragraph
In this example, the route is assigned the *`yamlError`* route
configuration by the exact ID.
:::
::::::::::
:::::::::::

::::: sect1
## Mixing DSLs {#_mixing_dsls}

:::: sectionbody
::: paragraph
Routes and route configuration are not required to use the same
language. For example, you can code route configurations in Java, and
then use XML DSL for the routes, and they would work together.
:::
::::
:::::

::::::::::: sect1
## Route Configuration in classic Spring XML {#_route_configuration_in_classic_spring_xml}

:::::::::: sectionbody
::: paragraph
When using XML DSL with `camel-spring-xml` then you can code your route
configurations in `<routeConfigurationContext>` snippets in separate XML
files as shown below:
:::

:::: listingblock
::: content
``` highlight
<routeConfigurationContext id="myConf" xmlns="http://camel.apache.org/schema/spring">
    <routeConfiguration id="xmlError">
        <onException>
            <exception>java.lang.Exception</exception>
            <handled><constant>true</constant></handled>
            <log message="XML WARN: ${exception.message}"/>
        </onException>
    </routeConfiguration>
</routeConfigurationContext>
```
:::
::::

::: paragraph
Then from `<camelContext>` you can refer to these XML snippets by their
ids:
:::

:::: listingblock
::: content
``` highlight
<camelContext id="myCamel" xmlns="http://camel.apache.org/schema/spring">

    <!-- refer to the ID on the context that has the route configurations (see above) -->
    <routeConfigurationContextRef ref="myConf"/>

    <!-- routes can then assign which configuration to use -->
    <route routeConfigurationId="xmlError">
        <from uri="timer:xml?period=5s"/>
        <log message="I am XML"/>
        <throwException exceptionType="java.lang.Exception" message="Some kind of XML error"/>
    </route>
</camelContext>
```
:::
::::

::: paragraph
In this example, the route is assigned the *`xmlError`* route
configuration by the exact ID.
:::
::::::::::
:::::::::::

:::::: sect1
## Packaging route configurations in reusable JARs {#_packaging_route_configurations_in_reusable_jars}

::::: sectionbody
::: paragraph
You can package common route configurations into JARs which you can then
use together with your Camel applications, by adding the JARs as
dependencies to the classpath (such as in Maven pom.xml file).
:::

::: paragraph
This allows, for example, to use a *common practice* among your Camel
applications.
:::
:::::
::::::

:::::::::::::: sect1
## Logging Summary {#_logging_summary}

::::::::::::: sectionbody
::: paragraph
If you set `startup-summary-level=verbose` then Camel will log for each
route which route configurations they have been assigned.
:::

::: paragraph
This option can be configured via Java API and also in
`application.properties` for Camel on Spring Boot, Quarkus, and Camel
standalone via `camel-main`
:::

:::: listingblock
::: content
``` highlight
camelContext.setStartupSummaryLevel(StartupSummaryLevel.Verbose);
```
:::
::::

::: paragraph
And with Spring Boot:
:::

:::: listingblock
::: content
``` highlight
camel.spring-boot.startup-summary-level = verbose
```
:::
::::

::: paragraph
And in Camel Main / Quarkus:
:::

:::: listingblock
::: content
``` highlight
camel.main.startup-summary-level = verbose
```
:::
::::
:::::::::::::
::::::::::::::

:::::::::::::: sect1
## Route Precondition {#_route_precondition}

::::::::::::: sectionbody
::: paragraph
The route configurations can be included or not according to the result
of a test expressed in simple language that is evaluated only once
during the initialization phase.
:::

::: paragraph
In the next example, the route configuration is only included if the
parameter `activate` has been set to `true`.
:::

:::: listingblock
::: content
``` highlight
routeConfiguration().precondition("{{activate}}")
    .onException(IllegalArgumentException.class)
    .handled(true)
    .log("WARN: ${exception.message}");
```
:::
::::

::: paragraph
And the same example using XML DSL:
:::

:::: listingblock
::: content
``` highlight
<routeConfiguration precondition="{{activate}}">
    <onException>
        <exception>java.lang.IllegalArgumentException</exception>
        <handled>
            <constant>true</constant>
        </handled>
        <log message="XML WARN: ${exception.message}"/>
    </onException>
</routeConfiguration>
```
:::
::::

::: paragraph
And in YAML DSL:
:::

:::: listingblock
::: content
``` highlight
- route-configuration:
    precondition: "{{activate}}"
    on-exception:
    - on-exception:
        exception:
          - "java.lang.IllegalArgumentException"
        handled:
          constant: "true"
        steps:
          - log:
              message: "YAML WARN ${exception.message}"
```
:::
::::
:::::::::::::
::::::::::::::

:::::: sect1
## More Information {#_more_information}

::::: sectionbody
::: paragraph
See these examples:
:::

::: ulist
- [camel-examples/examples/routes-configuration](https://github.com/apache/camel-examples/tree/main/routes-configuration)

- [camel-spring-boot-examples/routes-configuration/](https://github.com/apache/camel-spring-boot-examples/tree/main/routes-configuration)
:::
:::::
::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
