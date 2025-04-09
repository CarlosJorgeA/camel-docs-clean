::: {#header}
# Predicates
:::

:::::::::::::::::::::::::::::::::::::::::::::::: {#content}
:::::: {#preamble}
::::: sectionbody
::: paragraph
[Expressions](expression.html) and Predicates can then be used to create
the various [Enterprise Integration
Patterns](components:eips:enterprise-integration-patterns.html) in the
[DSL](dsl.html) like with the [Content Based
Router](components:eips:choice-eip.html) EIP.
:::

::: paragraph
To support dynamic rules Camel supports pluggable
[Predicate](https://www.javadoc.io/doc/org.apache.camel/camel-api/current/org/apache/camel/Predicate.html)
strategies using a variety of different
[Languages](components:languages:index.html).
:::
:::::
::::::

:::::::::::::::::: sect1
## Predicate API {#_predicate_api}

::::::::::::::::: sectionbody
::: paragraph
The API for a Camel Predicate is defined in the
`org.apache.camel.Predicate` interface as shown:
:::

:::: listingblock
::: content
``` highlight
public interface Predicate {

    /**
     * Evaluates the predicate on the message exchange and returns true if this
     * exchange matches the predicate
     *
     * @param exchange the message exchange
     * @return true if the predicate matches
     */
    boolean matches(Exchange exchange);

}
```
:::
::::

::: paragraph
A `Predicate` is being evaluated to a boolean value so the result is
either `true` or `false`. This makes predicates so powerful as it is
often used to control the routing of message in which path they should
be routed.
:::

::: paragraph
A simple example is to route an [Exchange](exchange.html) based on a
header value with the [Content Based
Router](components:eips:choice-eip.html) EIP:
:::

:::: listingblock
::: content
``` highlight
from("jms:queue:order")
   .choice()
      .when(header("type").isEqualTo("widget")).to("bean:widgetOrder")
      .when(header("type").isEqualTo("wombat")).to("bean:wombatOrder")
   .otherwise()
      .to("bean:miscOrder")
   .end();
```
:::
::::

::: paragraph
In the route above the [Predicate](#) is the
`header("type").isEqualTo("widget")` as it is constructed as an
[Expression](expression.html) that is evaluated as a [Predicate](#). To
do this the various *Builder classes* help us here to create a nice and
fluent syntax. `isEqualTo` is a builder method that returns a
[Predicate](#) based on the input.
:::

::: paragraph
Sometimes the fluent builders can get long, and a bit complex to read,
then you can just define your predicate outside the route and then just
refer to the predicate in the route:
:::

:::: listingblock
::: content
``` highlight
Predicate isWidget = header("type").isEqualTo("widget");
```
:::
::::

::: paragraph
And then you can refer to it in the route as:
:::

:::: listingblock
::: content
``` highlight
from("jms:queue:order")
   .choice()
      .when(isWidget).to("bean:widgetOrder")
      .when(isWombat).to("bean:wombatOrder")
   .otherwise()
      .to("bean:miscOrder")
   .end();
```
:::
::::
:::::::::::::::::
::::::::::::::::::

::::::::::: sect1
## Negating a Predicate {#_negating_a_predicate}

:::::::::: sectionbody
::: paragraph
You can use the **not** method on the `PredicateBuilder` to negate a
predicate.
:::

::: paragraph
First, we import the static method `not`, so it makes our route nice and
easy to read:
:::

:::: listingblock
::: content
``` highlight
import static org.apache.camel.builder.PredicateBuilder.not;
```
:::
::::

::: paragraph
And then we can use it to enclose an existing predicate and negate it as
the example shows:
:::

:::: listingblock
::: content
``` highlight
from("direct:start")
    .choice()
        .when(not(header("username").regex("goofy|pluto"))).to("mock:people")
        .otherwise().to("mock:animals")
    .end();
```
:::
::::
::::::::::
:::::::::::

:::::::::::::::::: sect1
## Compound Predicates {#_compound_predicates}

::::::::::::::::: sectionbody
::: paragraph
You can also create compound predicates using boolean operators such as
`and, or, not` and many others.
:::

::: paragraph
Currently, this feature is only available in the Java-based DSLs, and
not in other DSLs such as XML.
:::

::: paragraph
Using the
[`PredicateBuilder`](https://www.javadoc.io/doc/org.apache.camel/camel-support/current/org/apache/camel/support/builder/PredicateBuilder.html)
class, you can combine predicates **from different Expression
Languages** based on logical operators and comparison operators:
:::

::: ulist
- `not`, `and`, `or`

- `isNull`, `isNotNull`

- `isEqualTo`, `isGreaterThan`, `isLessThan`

- `startsWith`, `endsWith`

- `in` (\"any of X predicates stands true\")
:::

::: paragraph
Additionally, with `PredicateBuilder` you can create regular expressions
and use them as predicates, applying them to the result of an
expression, e.g.
:::

:::: listingblock
::: content
``` highlight
PredicateBuilder.regex(header("foo"), "\d\{4}");
```
:::
::::

::: paragraph
applies the regular expression to the foo header.
:::

::: paragraph
Combining different Expression Languages is also possible, e.g.:
:::

:::: listingblock
::: content
``` highlight
PredicateBuilder.and(XPathBuilder.xpath("/bookings/flights"), simple("${exchangeProperty.country = 'Spain'}"))
```
:::
::::

::: paragraph
The sample below demonstrates further use cases:
:::

:::: listingblock
::: content
``` highlight
// We define 3 predicates based on some user roles
// we have static imported and/or from org.apache.camel.builder.PredicateBuilder

// First we have a regular user that is just identified having a username header
Predicate user = header("username").isNotNull();

// The admin user must be a user AND have a admin header as true
Predicate admin = and(user, header("admin").isEqualTo("true"));

// And God must be an admin and (either have type god or a special message containing Camel Rider)
Predicate god = and(admin, or(body().contains("Camel Rider"), header("type").isEqualTo("god")));

// As you can see with the predicates above we can stack them to build compound predicates

// In our route below we can create a nice content based router based on the predicates we
// have defined. Then the route is easy to read and understand.
// We encourage you to define complex predicates outside the fluent router builder as
// it will just get a bit complex for humans to read
from("direct:start").choice()
    .when(god).to("mock:god")
    .when(admin).to("mock:admin")
    .when(user).to("mock:user")
    .otherwise().to("mock:guest")
.end();
```
:::
::::
:::::::::::::::::
::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
