::: {#header}
# Expressions
:::

:::::::::::::: {#content}
:::::: {#preamble}
::::: sectionbody
::: paragraph
Expressions and [Predicates](predicate.html) can then be used to create
the various [Enterprise Integration
Patterns](components:eips:enterprise-integration-patterns.html) in the
[DSL](dsl.html) like with the [Recipient
List](components:eips:recipientList-eip.html) EIP.
:::

::: paragraph
To support dynamic rules Camel supports pluggable
[Expression](https://www.javadoc.io/doc/org.apache.camel/camel-api/current/org/apache/camel/Expression.html)
strategies using a variety of different
[Languages](components:languages:index.html).
:::
:::::
::::::

::::::::: sect1
## Expression API {#_expression_api}

:::::::: sectionbody
::: paragraph
If you are outside the DSL and want to create your own expressions you
can either implement the [Expression
interface](https://www.javadoc.io/doc/org.apache.camel/camel-api/current/org/apache/camel/Expression.html),
reuse one of the other builders or try the [ExpressionBuilder
class](https://www.javadoc.io/doc/org.apache.camel/camel-support/current/org/apache/camel/support/builder/ExpressionBuilder.html).
:::

:::::: sect2
### Expression {#_expression}

::: paragraph
The API for a Camel Expression is defined in the
`org.apache.camel.Expression` interface as shown:
:::

:::: listingblock
::: content
``` highlight
public interface Expression {

    /**
     * Returns the value of the expression on the given exchange
     *
     * @param exchange the message exchange on which to evaluate the expression
     * @param type the expected type of the evaluation result
     * @return the value of the expression
     */
    <T> T evaluate(Exchange exchange, Class<T> type);
}
```
:::
::::
::::::
::::::::
:::::::::
::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
