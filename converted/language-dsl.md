::: {#header}
# Language DSL
:::

:::::::::::::::::::::: {#content}
::::::: {#preamble}
:::::: sectionbody
::: paragraph
The Language DSL is a builder API that allows using type safe
construction of Camel [Languages](languages.html).
:::

::: paragraph
The Language DSL is exclusively available as part of the Java DSL.
:::

::: paragraph
The DSL can be accessed directly from the `RouteBuilder` thanks to the
method `expression()`.
:::
::::::
:::::::

:::::::::::::::: sect1
## Using Language DSL {#_using_language_dsl}

::::::::::::::: sectionbody
::: paragraph
In the following example, a `TokenizerExpression` is created using the
legacy approach where the expression is instantiated explicitly and
configured using setters:
:::

:::: listingblock
::: content
``` highlight
public class MyRoutes extends RouteBuilder {
    @Override
    public void configure() {
        TokenizerExpression expression = new TokenizerExpression("(\\W+)\\s*"); // (1)
        expression.setRegex(true); // (2)
        from("file:data")
            .split(expression) // (3)
            .process("processEntry");
    }
}
```
:::
::::

::: {.colist .arabic}
1.  Instantiate the expected expression

2.  Configure the expression according to the needs

3.  Affect the expression with the expected configuration
:::

::: paragraph
The previous code could be simplified using the utility methods
available directly from the `ExpressionClause` corresponding to the type
returned by several existing methods such as `split()`, `setBody()`,
`setHeader(String)`, `aggregate()`, etc.:
:::

:::: listingblock
::: content
``` highlight
public class MyRoutes extends RouteBuilder {
    @Override
    public void configure() {
        from("file:data")
            .split()
            .tokenize("(\\W+)\\s*", true) // (1)
            .process("processEntry");
    }
}
```
:::
::::

::: {.colist .arabic}
1.  Select the `tokenize` language with a specific regular expression
:::

::: paragraph
This approach is suitable for very basic configuration, but as there are
only limited utility methods for each supported language, for more
complex configuration, we can quickly face situations where the utility
method for our expected configuration doesn't exist. In this situation,
you can either use the legacy approach or the language DSL like in the
next code snippet:
:::

:::: listingblock
::: content
``` highlight
public class MyRoutes extends RouteBuilder {
    @Override
    public void configure() {
        from("file:data")
            .split(
                expression() // (1)
                    .tokenize() // (2)
                        .token("(\\W+)\\s*") // (3)
                        .regex(true) // (3)
                    .end() // (4)
            )
            .process("processEntry");
    }
}
```
:::
::::

::: {.colist .arabic}
1.  Give access to all the supported languages

2.  Select the `tokenize` language

3.  Configure the expression according to the needs

4.  Build the expression with the expected configuration
:::
:::::::::::::::
::::::::::::::::
::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
