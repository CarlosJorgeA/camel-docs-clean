::: {#header}
# Message Exchange
:::

:::::::::::::: {#content}
:::::: {#preamble}
::::: sectionbody
::: paragraph
A request message and its corresponding reply or exception message is
represented in Camel using the `Exchange` interface. This interface
provides an abstraction for this pattern of communication between
systems. The presence of a reply message is optional and depends on the
[exchange pattern](exchange-pattern.html) used in the integration.
Thanks to this, Apache Camel can support different integration patterns
such as:
:::

::: ulist
- [Event Messages](components:eips:event-message.html): messages that
  have only an inbound message

- [Request and Reply](components:eips:requestReply-eip.html): messages
  that have an inbound and an outbound message.
:::
:::::
::::::

::::: sect1
## Learn More About Exchanges {#_learn_more_about_exchanges}

:::: sectionbody
::: ulist
- [Exchange Pooling](manual::exchange-pooling.html)

- [Using Exchange Pattern
  Annotations](manual::using-exchange-pattern-annotations.html)
:::
::::
:::::

:::::: sect1
## Implementation Details {#_implementation_details}

::::: sectionbody
::: paragraph
There are concrete classes that implement the `Exchange` interface for
each Camel-supported communications technology. For example, the
`JmsExchange` class provides a JMS-specific implementation of the
`Exchange` interface. The public API of the `Exchange` interface is
limited intentionally: we expect that each class that implements this
interface will provide its own technology-specific operations.
:::

::: paragraph
Application-level programmers rarely access the `Exchange` interface (or
classes that implement it) directly. However, many classes in Camel are
generic types that are instantiated on (a class that implements)
`Exchange`. Because of this, the `Exchange` interface appears a lot in
the generic signatures of classes and methods.
:::
:::::
::::::
::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
