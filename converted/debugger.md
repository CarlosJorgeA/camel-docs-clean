::: {#header}
# Debugger
:::

:::::::::::::::::::::::::::::::::::::::: {#content}
:::::: {#preamble}
::::: sectionbody
::: paragraph
The Camel Debugger is intended for third party tooling to make it
possible to debug routes, trace messages and to use breakpoints with the
EIP patterns in the Camel routes.
:::

::: paragraph
The Debugger allows tooling or the likes to attach breakpoints which are
being invoked when [Exchanges](exchange.html) are routed.
:::
:::::
::::::

::::::::::::::::::::::::::::::::::: sect1
## Java Debugging Camel routes in unit tests {#_java_debugging_camel_routes_in_unit_tests}

:::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
If you are developing unit tests using the `camel-test-junit5`
component, then the Debugger is available if you turn it on via
overriding the `isUseDebugger()` method and return `true`.
:::

::: paragraph
In this unit test
:::

:::: listingblock
::: content
``` highlight
public class DebugTest extends CamelTestSupport
```
:::
::::

::: paragraph
We want to debug the following route
:::

:::: listingblock
::: content
``` highlight
@Override
protected RouteBuilder createRouteBuilder() throws Exception {
    return new RouteBuilder() {
        @Override
        public void configure() throws Exception {
            // this is the route we want to debug
            from("direct:start")
                .to("mock:a")
                .transform(body().prepend("Hello "))
                .to("mock:b");
        }
    };
}
```
:::
::::

::: paragraph
Which can easily done by overriding the `debugBefore` method as shown
:::

:::: listingblock
::: content
``` highlight
@Override
public boolean isUseDebugger() {
    // must enable debugger
    return true;
}

@Override
protected void debugBefore(Exchange exchange, Processor processor,
                           ProcessorDefinition<?> definition, String id, String shortName) {
    // this method is invoked before we are about to enter the given processor
    // from your Java editor you can just add a breakpoint in the code line below
    log.info("Before " + definition + " with body " + exchange.getIn().getBody());
}
```
:::
::::

::: paragraph
Then from your Java editor just add a breakpoint inside the
`debugBefore` method. Then fire up the unit test and wait for the Java
editor to hit the breakpoint. Then you can inspect the Exchange during
debugging while it advances during routing. The `ProcessorDefinition`
and the `id` and `shortName` parameters is all information which tells
you where in the route the breakpoint was hit.
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | There is also a `debugAfter`      |
| Tip                               | method which is invoked after the |
| :::                               | processor has been invoked. This  |
|                                   | allows you to *see* what happens  |
|                                   | to the Exchange right after it    |
|                                   | has invoked a processor in the    |
|                                   | route.                            |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
The screenshot below shows the Debugger in action. The IDE (IDEA) has
hit the breakpoint, and we can inspect the parameters. Notice how we can
see that the message is to be sent to the mock:a endpoint.
:::

:::: imageblock
::: content
![image](images/debug.png)
:::
::::

::::::: sect2
### Java debugging of Camel routes written with Java DSL {#_java_debugging_of_camel_routes_written_with_java_dsl}

::: paragraph
A trick to debug a Camel route written with Java DSL is to modify the
route to insert a `processor` and then to set the breakpoint in it.
:::

::: paragraph
For instance:
:::

:::: listingblock
::: content
``` highlight
public class MyRouteBuilder extends RouteBuilder {

    @Override
    public void configure() throws Exception {
        from("timer:demo").routeId("foo")
            .bean("myBean", "hello")
            .process(new Processor() {
                @Override
                public void process(Exchange exchange) throws Exception {
                    System.out.println("put a breakpoint here");
                }
            })
            .log("${body}")
            .bean("myBean", "bye")
            .log("${body}");
    }
}
```
:::
::::
:::::::

:::::: sect2
### Implementing a custom debugger {#_implementing_a_custom_debugger}

::: paragraph
The debugger API is defined in `org.apache.camel.spi.Debugger`. This API
has methods to attach and remove breakpoints.
:::

::: paragraph
And to suspend/resume all breakpoints etc. You can also attach a
condition to the breakpoint, so it only reacts if the condition matches.
:::

::: paragraph
Camel provides a base implementation
`org.apache.camel.impl.DefaultDebugger`, which can be used to extend for
custom implementations.
:::
::::::

::::::::: sect2
### Camel Route debugger through JMX {#_camel_route_debugger_through_jmx}

::: paragraph
There is also a [Backlog Debugger](backlog-debugger.html) that allows
debugging from JMX. It is automatically provided when `camel-debug` is
on the classpath (since 3.16) or when using the `camel:debug` Maven goal
(since 3.18).
:::

::: paragraph
To be able to have enough time to add your breakpoints, since 3.18, you
could need to suspend the message processing of Camel to make sure that
you won't miss any messages. For this kind of need, you have to set
either the environment variable `CAMEL_DEBUGGER_SUSPEND` or the system
property `org.apache.camel.debugger.suspend` to `true` within the
context of your application, then the `Backlog Debugger` suspends the
message processing until the JMX operation `attach` is called. Calling
the JMX operation `detach` suspends again the message processing.
:::

::: paragraph
In case the environment variable and the system property are both set,
the value of the environment variable is used.
:::

::: paragraph
Several third-party tools are using it:
:::

::: ulist
- [hawtio](https://hawt.io/) uses this for its web-based debugging
  functionality

- [VS Code Debug Adapter for
  Camel](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-debug-adapter-apache-camel)

- [Eclipse Desktop Debug Adapter for
  Camel](http://marketplace.eclipse.org/content/textual-debugging-apache-camel)

- [IntelliJ Camel
  plugin](https://plugins.jetbrains.com/plugin/9371-apache-camel)

- [Debug Adapter Server for
  Camel](https://github.com/camel-tooling/camel-debug-adapter)
:::

::: paragraph
Camel requires to have `camel-management` JAR on the classpath for
having JMX enabled.
:::
:::::::::
::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
