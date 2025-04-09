::: {#header}
# AdviceWith
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
:::::::: {#preamble}
::::::: sectionbody
::: paragraph
AdviceWith is used for testing Camel routes where you can *advice* an
existing route before its being tested.
:::

::: paragraph
What `adviceWith` allows is to change some factors on the route before
the test is being run, such as:
:::

::: ulist
- Intercept sending to endpoints

- Replace incoming endpoint with another

- Take out or replace node(s) of the route

- Insert new node(s) into the route

- Mock endpoints
:::

::: paragraph
All these features are available from `AdviceWithRouteBuilder`, which is
a specialized `RouteBuilder`.
:::
:::::::
::::::::

::::::::::: sect1
## AdviceWithRouteBuilder API {#_advicewithroutebuilder_api}

:::::::::: sectionbody
::: paragraph
The `AdviceWithRouteBuilder` extends the regular `RouteBuilder` adding
specialized methods for *advicing* routes. The table below lists the
most commonly used methods:
:::

+-------------+--------------------------------------------------------+
| Method      | Description                                            |
+=============+========================================================+
| `moc        | Mocks all endpoints in the route.                      |
| kEndpoints` |                                                        |
+-------------+--------------------------------------------------------+
| `mo         | Mocks all endpoints in the route that matches the      |
| ckEndpoints | patterns. You can use wildcards and regular            |
| (patterns)` | expressions in the given pattern to match multiple     |
|             | endpoints.                                             |
+-------------+--------------------------------------------------------+
| `mockEndpo  | Mocks all endpoints and skip sending to the endpoint   |
| intsAndSkip | in the route that matches the patterns. You can use    |
| (patterns)` | wildcards and regular expressions in the given pattern |
|             | to match multiple endpoints.                           |
+-------------+--------------------------------------------------------+
| `replaceFro | To replace the route input with a new endpoint.        |
| mWith(uri)` |                                                        |
+-------------+--------------------------------------------------------+
| `weaveByUr  | Manipulates the route at the nodes sending to          |
| i(pattern)` | endpoints matching the pattern.                        |
+-------------+--------------------------------------------------------+
| `weaveByI   | Manipulates the route at the node IDs that matches the |
| d(pattern)` | pattern.                                               |
+-------------+--------------------------------------------------------+
| `wea        | Manipulates the route at the node string               |
| veByToStrin | representation(output from toString method) that       |
| g(pattern)` | matches the pattern.                                   |
+-------------+--------------------------------------------------------+
| `weaveByT   | Manipulates the route at the node type (class name)    |
| ype(Class)` | that matches the pattern.                              |
+-------------+--------------------------------------------------------+
| `wea        | Easily weaves in new nodes at the beginning of the     |
| veAddFirst` | route.                                                 |
+-------------+--------------------------------------------------------+
| `we         | Easily weaves in new nodes at the end of the route.    |
| aveAddLast` |                                                        |
+-------------+--------------------------------------------------------+

:::::::: sect2
### Pattern matching {#_pattern_matching}

::: paragraph
The pattern option is used for matching. It uses the same rules as
[Intercept](components:eips:intercept.html), which is applied in the
following order:
:::

::: ulist
- match exact

- match by wildcard (`*`)

- match by regular expression
:::

::: paragraph
For example, to match exact, you can use `weaveById("foo")` which will
match only the id in the route which has the value foo. The wildcard is
when the pattern ends with a `*` character, such as: `weaveById("foo*")`
which will match any id's starting with foo, such as: foo, foobar,
foobie and so forth. The regular expression is more advanced and allows
you to match multiple ids, such as `weaveById("(foo|bar)")` which will
match both foo and bar.
:::

::: paragraph
If you try to match a pattern on an exact endpoint URI, then mind that
URI options ordering may influence, and hence it's best to match by
wildcard.
:::

::: paragraph
For example, using `mockEndpointsAndSkip("activemq:queue:foo?*")` To
match the foo queue and disregard any options.
:::
::::::::
::::::::::
:::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Using AdviceWith {#_using_advicewith}

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
To *advice* you need to use the `AdviceWithRouteBuilder` for
manipulating the route. But first, you need to select which route to
manipulate which you can do by the route ID or the route index.
:::

:::: listingblock
::: content
``` highlight
AdviceWith.adviceWith(context, "myRoute", new AdviceWithRouteBuilder() {
        @Override
        public void configure() throws Exception {
            weaveAddLast().to("mock:result");
        }
});
```
:::
::::

::: paragraph
We introduce a more modern API for *advicing* routes using Java lambda
style.
:::

::: paragraph
Below, we are *advicing* the route with ID myRoute:
:::

:::: listingblock
::: content
``` highlight
AdviceWith.adviceWith(context, "myRoute", a ->
     a.weaveAddLast().to("mock:result")
);
```
:::
::::

::: paragraph
The variable `a` is a lambda style of the `AdviceWithRouteBuilder` which
can be used as shorthand for inlining the route manipulation.
:::

::: {.admonitionblock .important}
+-----------------------------------+-----------------------------------+
| ::: title                         | Before using `adviceWith` then it |
| Important                         | is best to tell Camel that advice |
| :::                               | is in use, which is covered in    |
|                                   | the following section.            |
+-----------------------------------+-----------------------------------+
:::

::::::::::::::::: sect2
### Enabling advice during testing {#_enabling_advice_during_testing}

::: paragraph
When `adviceWith` is being used, then Camel will restart the adviced
routes. This happens because the route is manipulated, and Camel needs
to:
:::

::: {.olist .arabic}
1.  Stop the existing route

2.  Remove the existing route

3.  Add the new advised route

4.  Start the new route
:::

::: paragraph
This happens for every *adviced* route during startup of your unit
tests. It happens quickly: a route is started and then is immediately
stopped and removed. This is an undesired behavior; you want to avoid
restarts of the *adviced* routes. This is solved by following the
following steps:
:::

::: {.olist .arabic}
1.  Tell Camel that routes are being *adviced*, which will prevent
    Apache Camel from automatic starting routes.

2.  Advice the routes in your unit test methods

3.  Start Camel after you have *adviced* the routes
:::

::: paragraph
When using [camel-test-junit5](components:others:test-junit5.html) for
unit testing, then you can tell Camel that advice is in use by either
overriding the `isUsedAdviceWith` method from `CamelTestSupport` as
shown:
:::

:::: listingblock
::: content
``` highlight
public class MyAdviceWithTest extends CamelTestSupport {
    @Override
    public boolean isUseAdviceWith() {
        return true; // turn on advice with
    }
}
```
:::
::::

::: paragraph
Or when using
[camel-test-spring-junit5](components:others:test-spring-junit5.html)
for unit testing you can use the `@UseAdviceWith` annotation as shown:
:::

:::: listingblock
::: content
``` highlight
@UseAdviceWith
public class MyAdviceWithTest extends CamelSpringTestSupport {
}
```
:::
::::

::: paragraph
Then you advise the routes followed by starting Camel:
:::

:::: listingblock
::: content
``` highlight
@Test
public void testMockEndpoints() throws Exception {
    AdviceWith.adviceWith(context, "myRoute", a ->
         a.mockEndpoints();
    );

    context.start();
```
:::
::::

::: paragraph
In the unit test method above, we first advise the route by ID, where we
auto mock all the endpoints. After that we start Camel.
:::
:::::::::::::::::

:::::::::: sect2
### Logging before and after advicing routes {#_logging_before_and_after_advicing_routes}

::: paragraph
When using `adviceWith` then Camel will automatically log, the before
and after, of each adviced route, in XML format.
:::

::: paragraph
However, this requires to have `camel-xml-jaxb` as dependency, which you
can add as test scoped if using Maven:
:::

:::: listingblock
::: content
``` highlight
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-xml-jaxb</artifactId>
    <version>x.x.x</version>
    <!-- use the same version as your Camel core version -->
    <scope>test</scope>
</dependency>
```
:::
::::

::: paragraph
It is possible to turn of logging as XML, by setting the logging to
`false` as shown:
:::

:::: listingblock
::: content
``` highlight
AdviceWith.adviceWith(context, "myRoute", false, a ->
     a.mockEndpoints();
);
```
:::
::::
::::::::::

::::::::: sect2
### Replacing route endpoints {#_replacing_route_endpoints}

::: paragraph
You may have built Camel routes that start from endpoints that consume
from databases, message brokers, cloud systems, or other external
systems.
:::

::: paragraph
To make unit testing these kinds of routes easier, you can replace the
route input endpoints with internal endpoints such as
[direct](components::direct-component.html),
[seda](components::seda-component.html),
[stub](components::stub-component.html).
:::

::: paragraph
The following illustrates how to do this:
:::

:::: listingblock
::: content
``` highlight
@Test
public void testReplaceFrom() throws Exception {
    AdviceWith.adviceWith(context, "myRoute", a ->
        a.replaceFromWith("direct:start");
    );

    context.start();
```
:::
::::

::: paragraph
This replaces the input endpoint (from) in the route with ID myRoute,
with a direct endpoint, which makes it easy to send a message to the
route when unit testing.
:::
:::::::::

:::::::::::: sect2
### Mocking endpoints {#_mocking_endpoints}

::: paragraph
When using the `mockEndpoints` methods when *advicing* routes, then
Camel will log during startup which endpoints have been *adviced* and
their corresponding mock uri, such as:
:::

:::: listingblock
::: content
``` highlight
INFO  ceptSendToMockEndpointStrategy - Adviced endpoint [seda://camel] with mock endpoint [mock:seda:camel]
INFO  ceptSendToMockEndpointStrategy - Adviced endpoint [seda://other] with mock endpoint [mock:seda:other]
```
:::
::::

::: paragraph
Here Camel have *adviced* two endpoints:
:::

::: ulist
- seda:camel -→ mock:seda:camel

- seda:other -→ mock:seda:other
:::

::: paragraph
This allows using the mock endpoints in your unit tests for testing,
such as:
:::

:::: listingblock
::: content
``` highlight
public void testMockEndpoints() throws Exception {
    // advice the route goes here
    // start camel after advice

    // use the auto mocked uris during testing
    getMockEndpoint("mock:seda:camel").expectedMessageCount(3);
    getMockEndpoint("mock:seda:other").expectedMessageCount(1);

    // send messages

    MockEndpoint.assertIsSatisfied(context);
}
```
:::
::::

::: paragraph
Replacing the input endpoints, or mocking endpoints in Camel routes by
using `adviceWith` is just the beginning of the route manipulation
capabilities available. The following section covers how to go even
deeper.
:::
::::::::::::

::::::::::::::::::::::::::: sect2
### Using weave to amend routes {#_using_weave_to_amend_routes}

::: paragraph
When testing your Camel routes, you can use `adviceWith` to *weave* the
routes before testing.
:::

::: paragraph
The following methods are available for the `weave` methods:
:::

+-------------+--------------------------------------------------------+
| Method      | Description                                            |
+=============+========================================================+
| `remove`    | Removes the selected node(s).                          |
+-------------+--------------------------------------------------------+
| `replace`   | Replaces the selected node(s) with the following       |
|             | nodes.                                                 |
+-------------+--------------------------------------------------------+
| `before`    | Before the selected node(s), the following nodes is    |
|             | added.                                                 |
+-------------+--------------------------------------------------------+
| `after`     | After the selected node(s), the following nodes is     |
|             | added.                                                 |
+-------------+--------------------------------------------------------+

::: paragraph
For example, given the following route:
:::

:::: listingblock
::: content
``` highlight
from("direct:start")
  .to("mock:foo")
  .to("mock:bar").id("bar")
  .to("mock:result");
```
:::
::::

::: paragraph
Then let's go over the four methods to see how you can use them in unit
tests
:::

:::::: sect3
#### Replace {#_replace}

:::: listingblock
::: content
``` highlight
AdviceWith.adviceWith(context.getRouteDefinitions().get(0), context, new AdviceWithRouteBuilder() {
    @Override
    public void configure() throws Exception {
        // weave the node in the route which has id = bar
        // and replace it with the following route path
        weaveById("bar").replace().multicast().to("mock:a").to("mock:b");
    }
});
```
:::
::::

::: paragraph
In this example we replace the `.to("mock:bar").id("bar")` with the
`.multicast().to("mock:a").to("mock:b")`. That means instead of sending
the message to a mock:bar endpoint, we do a Multicast to mock:a and
mock:b endpoints instead.
:::
::::::

:::::: sect3
#### Remove {#_remove}

::: paragraph
In the example below, we simply just remove the
`.to("mock:bar").id("bar")` from the route:
:::

:::: listingblock
::: content
``` highlight
AdviceWith.adviceWith(context.getRouteDefinitions().get(0), context, new AdviceWithRouteBuilder() {
    @Override
    public void configure() throws Exception {
        // weave the node in the route which has id = bar and remove it
        weaveById("bar").remove();
    }
});
```
:::
::::
::::::

::::::: sect3
#### Before {#_before}

::: paragraph
In the example below, we add the following nodes
`to("mock:a").transform(constant("Bye World"))` before the node with the
id bar.
:::

:::: listingblock
::: content
``` highlight
AdviceWith.adviceWith(context.getRouteDefinitions().get(0), context, new AdviceWithRouteBuilder() {
    @Override
    public void configure() throws Exception {
        // weave the node in the route which has id = bar
        // and insert the following route path before the adviced node
        weaveById("bar").before().to("mock:a").transform(constant("Bye World"));
    }
});
```
:::
::::

::: paragraph
That means the message being sent before to mock:bar would have been
transformed to a constant message Bye World
:::
:::::::

::::::: sect3
#### After {#_after}

::: paragraph
In the example below, we add the following nodes
`to("mock:a").transform(constant("Bye World"))` after the node with the
id bar.
:::

:::: listingblock
::: content
``` highlight
AdviceWith.adviceWith(context.getRouteDefinitions().get(0), context, new AdviceWithRouteBuilder() {
    @Override
    public void configure() throws Exception {
        // weave the node in the route which has id = bar
        // and insert the following route path after the advice node
        weaveById("bar").after().to("mock:a").transform(constant("Bye World"));
    }
});
```
:::
::::

::: paragraph
That means the message being sent after mock:bar would have been
transformed to a constant message Bye World
:::
:::::::
:::::::::::::::::::::::::::

::::::::::: sect2
### weave without using IDs {#_weave_without_using_ids}

::: paragraph
When weaving a route, you need to use one of the `weaveBy` methods as
criteria to select one or more nodes in the route graph.
:::

::: paragraph
Suppose you use the [Split](components:eips:split-eip.html) EIP in a
route; then you can use `weaveByType` to select this EIP. Given the
following route:
:::

:::: listingblock
::: content
``` highlight
from("file:inbox").routeId("inbox")
    .split(body())
    .transform(simple("${body.toLowerCase()}"))
        .to("mock:line")
    .end()
    .to("mock:combined");
```
:::
::::

::: paragraph
Due to that route has only one [Split](components:eips:split-eip.html)
EIP, you can use `weaveByType` to find this single splitter in the
route. Using `weaveByType` requires you to pass in the model type of the
EIP. The name of the model type is using the pattern \_name_Definition.
:::

:::: listingblock
::: content
``` highlight
weaveByType(SplitDefinition.class)
    .before()
        .transform(simple("${body},Camel is awesome"));
```
:::
::::

::: paragraph
Here we weave and select the [Split](components:eips:split-eip.html) EIP
and weave in a message transformation, that is processed before calling
the splitter. This means the message body is appended with *Camel is
awesome*.
:::
:::::::::::

:::::::::::: sect2
### weaveByToUri {#_weavebytouri}

::: paragraph
The `weaveByToUri` is a handy method that makes it easy to *weave* a
Camel route that send messages to a given endpoint URI or pattern.
:::

::: paragraph
Given the following route having two branches in the [Content Based
Router](components:eips:choice-eip.html) EIP:
:::

:::: listingblock
::: content
``` highlight
from("direct:start")
    .choice()
        .when(header("foo")).to("direct:branch-1")
    .otherwise()
        .to("direct:branch-2");
```
:::
::::

::: paragraph
Then we want to easily unit test this route, that messages are sent
branch-1 or branch-2. This can be done with the `weaveByToUri` as shown:
:::

:::: listingblock
::: content
``` highlight
weaveByToUri("direct:branch*").replace().to("mock:cheese");
```
:::
::::

::: paragraph
Notice the `weaveByToUri` method is using a wildcard (`*`) to match the
two branches.
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | You can also use `mockEndpoints`  |
| Tip                               | to auto mock instead of           |
| :::                               | `weaveByToUri` in the example     |
|                                   | above. The `weave` methods have a |
|                                   | lot more power to manipulate the  |
|                                   | route, such as message            |
|                                   | transformation, routing the       |
|                                   | message or much more.             |
+-----------------------------------+-----------------------------------+
:::
::::::::::::

::::: sect2
### weaveAddFirst and weaveAddLast {#_weaveaddfirst_and_weaveaddlast}

::: paragraph
The `weaveAddFirst` and `weaveAddLast` is a shorthand to easily add
nodes to the route. These methods can only add to an existing route. If
you want to manipulate the route, then use the other `weave` methods as
already covered.
:::

::: paragraph
The `weaveAddFirst` method adds in the beginning of the route, and
`weaveAddLast` at the end. Using them works the same as the other
`weaveBy` methods, so see above for examples.
:::
:::::

::::::::::: sect2
### weave using node selection {#_weave_using_node_selection}

::: paragraph
The `weaveBy` methods, select all matching nodes, which can be anything
from none, one, two, or more nodes. In those situations, you may want to
narrow the selection to a specific node. This can be done by using the
select methods:
:::

::: ulist
- `selectFirst` Selects only the first node.

- `selectLast` Selects only the last node.

- `selectIndex(index)` Selects only the nth node. The index is
  zero-based.

- `selectRange(from, to)` Selects the nodes within the given range. The
  index is zero-based.

- `maxDeep(level)` Limits the selection to at most N levels deep in the
  Camel route tree. The first level is number 1. So number 2 is the
  children of the first-level nodes.
:::

::: paragraph
Given the following route which has multiple
[Filter](components:eips:filter-eip.html) EIP, then we want to only
advise the second filter.
:::

:::: listingblock
::: content
``` highlight
from("file:inbox").routeId("inbox")
    .filter(header("foo"))
        .to("mock:foo")
    .end()
    .to("mock:a")
    .filter(header("bar"))
        .to("mock:bar")
    .end()
    .to("mock:b")
    .filter(header("cheese"))
        .to("mock:cheese")
    .end()
    .to("mock:c")
```
:::
::::

::: paragraph
You can then use `weaveByType` to match the Filter EIPs and selectIndex
to match the second found:
:::

:::: listingblock
::: content
``` highlight
weaveByType(FilterDefinition.class).selectIndex(1).replace().to("mock:changed");
```
:::
::::
:::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
