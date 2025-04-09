::: {#header}
# CamelContext
:::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
:::::::::: {#preamble}
::::::::: sectionbody
::: paragraph
The
[`CamelContext`](https://www.javadoc.io/doc/org.apache.camel/camel-api/latest/org/apache/camel/CamelContext.html)
is the runtime system, which holds together all the fundamental concepts
of Apache Camel (routes, endpoints, components, etc).
:::

::: paragraph
This context object represents the Camel runtime system. Typically, you
have one `CamelContext` instance in an application.
:::

:::: imageblock
::: content
![image](images/camel-context.png)
:::
::::

::: paragraph
The `CamelContext` provides access to many features and services, the
most notable being components, type converters, a registry, endpoints,
routes, data formats, and languages.
:::

::: paragraph
The following table lists the most common services provided by the
`CamelContext`:
:::

+-----------------------------------+-----------------------------------+
| Service                           | Description                       |
+===================================+===================================+
| [Components](component.html)      | Contains the components used.     |
+-----------------------------------+-----------------------------------+
| [Endpoints](endpoint.html)        | Contains the endpoints that have  |
|                                   | been used.                        |
+-----------------------------------+-----------------------------------+
| [Routes](routes.html)             | The routes in use.                |
+-----------------------------------+-----------------------------------+
| [Data formats](data-format.html)  | Contains the loaded data formats. |
+-----------------------------------+-----------------------------------+
| [Languages](languages.html)       | Contains the loaded languages.    |
+-----------------------------------+-----------------------------------+
| [Type                             | Contains the loaded type          |
| converters](type-converter.html)  | converters. Camel has a mechanism |
|                                   | that allows you to manually or    |
|                                   | automatically convert from one    |
|                                   | type to another.                  |
+-----------------------------------+-----------------------------------+
| [Registry](registry.html)         | Contains a registry that allows   |
|                                   | you to look up beans.             |
+-----------------------------------+-----------------------------------+
:::::::::
::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## CamelContext 101 {#_camelcontext_101}

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
To give you a better understanding of Apache Camel, we'll discuss what
is inside the `CamelContext`.
:::

:::: sect2
### Routing Engine {#_routing_engine}

::: paragraph
Camel's routing engine moves messages under the hood and does all the
heavy lifting to ensure that messages are routed properly. It is not
exposed to the developer, but you should be aware that it's there.
:::
::::

::::::: sect2
### Routes {#_routes}

::: paragraph
[Routes](routes.html) are a core abstraction for Camel. The simplest way
to define a route is as a chain of [Processors](processor.html). There
are many reasons for using routers in messaging applications. By
decoupling clients from servers, and producers from consumers, routes
can, for example, do the following:
:::

::: ulist
- Decide dynamically what server a client will invoke

- Provide a flexible way to add extra processing

- Allow independent development for clients and servers

- Foster better design practices by connecting disparate systems that do
  one thing well

- Allow for clients of servers to be stubbed out (using
  [mocks](components::mock-component.html)) for testing purposes
:::

::: paragraph
Each route in Camel has a unique identifier. You can use the identifier
to log, debug, monitor, and start and stop routes.
:::

::: paragraph
Routes have one and only one input source for messages. They are
effectively tied to an input endpoint.
:::
:::::::

:::::::::::::::: sect2
### Domain Specific Language (DSL) {#_domain_specific_language_dsl}

::: paragraph
To wire processors and endpoints together to form routes, Camel defines
a [DSL](dsl.html).
:::

::: paragraph
In Camel with Java, DSL means a fluent Java API that contains methods
named for EIP terms.
:::

::: paragraph
Consider this example:
:::

:::: listingblock
::: content
``` highlight
from("file:data/inbox")
    .filter().xpath("/order[not(@test)]")
        .to("jms:queue:order");
```
:::
::::

::: paragraph
Here, in a single Java statement, you define a route that consumes files
from a [file](components::file-component.html) endpoint. Camel uses the
[Filter EIP](components:eips:filter-eip.html) to route the messages
using an XPath predicate to test whether the message is not a test
order. If a message passes the test, Camel forwards it to the
[JMS](components::jms-component.html) endpoint. Messages failing the
filter test are skipped.
:::

::: paragraph
Camel provides multiple DSL languages. You could define the same route
using the XML DSL:
:::

:::: listingblock
::: content
``` highlight
<route>
    <from uri="file:data/inbox"/>
    <filter>
        <xpath>/order[not(@test)]</xpath>
        <to uri="jms:queue:order"/>
    </filter>
</route>
```
:::
::::

::: paragraph
And in YAML:
:::

:::: listingblock
::: content
``` highlight
- from:
    uri: "file:data/inbox"
    steps:
      - filter:
          xpath: "/order[not(@test)]"
          steps:
            - to: "jms:queue:order"
```
:::
::::

::: paragraph
The DSLs provide a nice abstraction for Camel users to build
applications. Under the hood, though, a route is composed of a graph of
processors.
:::
::::::::::::::::

:::::::: sect2
### Processors {#_processors}

::: paragraph
The [processor](processor.html) is a core Camel concept that represents
a node capable of using, creating, or modifying an incoming
[exchange](exchange.html).
:::

::: paragraph
During routing, exchanges flow from one processor to another; as such,
you can think of a route as a graph having specialized processors as the
nodes, and lines that connect the output of one processor to the input
of another. Processors could be implementations of EIPs, producers for
specific components, or your own custom code. The figure below shows the
flow between processors.
:::

:::: imageblock
::: content
![image](images/message_flow_in_route.png)
:::
::::

::: paragraph
A route starts with a consumer (i.e., `from` in the DSL) that populates
the initial [exchange](exchange.html). At each processor step, the
output (out) message from the previous step is the input (in) message of
the next. In many cases, processors don't set an out message, so in this
case the in message is reused. The [exchange
pattern](exchange-pattern.html) of the exchange determines, at the end
of a route, whether a reply needs to be sent back to the caller of the
route. If the exchange pattern (MEP) is `InOnly`, no reply will be sent
back. If it's `InOut`, Camel will take the out message from the last
step and return it.
:::
::::::::

:::::: sect2
### Component {#_component}

::: paragraph
[Components](components::index.html) are the main extension point in
Camel.
:::

::: paragraph
From a programming point of view, components are fairly simple: they're
associated with a name that's used in a [URI](uris.html), and they act
as a factory of [endpoints](endpoint.html).
:::

::: paragraph
For example, `FileComponent` is referred to by file in a URI, and it
creates `FileEndpoint`. The endpoint is perhaps an even more fundamental
concept in Camel.
:::
::::::

:::::::::::::: sect2
### Endpoint {#_endpoint}

::: paragraph
An [endpoint](endpoint.html) is the Camel abstraction that models the
end of a channel through which a system can send or receive messages.
:::

:::: imageblock
::: content
![image](images/MessageEndpointSolution.gif)
:::
::::

::: paragraph
In Camel, you configure endpoints by using URIs, such as
`file:data/inbox?delay=5000`, and you also refer to endpoints this way.
At runtime, Camel looks up an endpoint based on the URI notation. The
figure below shows how this works.
:::

:::: imageblock
::: content
![image](images/endpoint-uri-syntax.png)
:::
::::

::: paragraph
The scheme (1) denotes which Camel component handles that type of
endpoint. In this case, the scheme of `file` selects `FileComponent`.
`FileComponent` then works as a factory, creating `FileEndpoint` based
on the remaining parts of the URI. The context path `data/inbox` (2)
tells `FileComponent` that the starting folder is `data/inbox`. The
option, `delay=5000` (3) indicates that files should be polled at a
5-second interval.
:::

::: paragraph
The next figure shows how an endpoint works together with an exchange,
producers, and consumers.
:::

:::: imageblock
::: content
![image](images/endpoint-factory.png)
:::
::::

::: paragraph
An endpoint acts as a factory for creating consumers and producers that
are capable of receiving and sending messages to a particular endpoint.
:::
::::::::::::::

:::: sect2
### Producer {#_producer}

::: paragraph
A producer is the Camel abstraction that refers to an entity capable of
sending a message to an endpoint. When a message is sent to an endpoint,
the producer handles the details of getting the message data compatible
with that particular endpoint. For example, `FileProducer` will write
the message body to a `java.io.File`. `JmsProducer`, on the other hand,
will map the Camel message to `javax.jms.Message` before sending it to a
JMS destination. This is an important feature in Camel, because it hides
the complexity of interacting with particular transports. All you need
to do is route a message to an endpoint, and the producer does the heavy
lifting.
:::
::::

::::::::::::: sect2
### Consumer {#_consumer}

::: paragraph
A consumer is the service that receives messages produced by some
external system, wraps them in an [exchange](exchange.html), and sends
them to be processed. Consumers are the source of the exchanges being
routed in Apache Camel. To create a new exchange, a consumer will use
the endpoint that wraps the payload being consumed. A
[processor](processor.html) is then used to initiate the routing of the
exchange in Camel via the routing engine.
:::

::: paragraph
Camel has two kinds of consumers: event-driven consumers, and polling
consumers (or scheduled polling consumers). The differences between
these consumers are important, because they help solve different
problems.
:::

::::::: sect3
#### Event Driven Consumer {#_event_driven_consumer}

::: paragraph
The most familiar consumer is the event-driven consumer, as illustrated:
:::

:::: imageblock
::: content
![image](images/EventDrivenConsumerSolution.gif)
:::
::::

::: paragraph
This kind of consumer is mostly associated with client-server
architectures and web services. It's also referred to as an asynchronous
receiver in the EIP world. An event-driven consumer listens on a
particular messaging channel, such as a TCP/IP port, JMS queue, Twitter
handle, Amazon SQS queue, WebSocket, and so on. It then waits for a
client to send messages to it. When a message arrives, the consumer
wakes up and takes the message for processing.
:::
:::::::

::::: sect3
#### Polling Consumer / Scheduled Polling Consumer {#_polling_consumer_scheduled_polling_consumer}

::: paragraph
In contrast to the event-driven consumer, the polling consumer actively
goes and fetches messages from a particular source, such as an FTP
server. The polling consumer is also known as a synchronous receiver in
EIP lingo, because it won't poll for more messages until it's finished
processing the current message. A common flavor of the polling consumer
is the scheduled polling consumer, which polls at scheduled intervals.
File, FTP, and email components all use scheduled polling consumers.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | In the Camel components, its only |
| Note                              | either the event driven or        |
| :::                               | scheduled polling consumers that  |
|                                   | are in use. The polling consumer  |
|                                   | (non-scheduled) is only used to   |
|                                   | poll on-demand, such as when      |
|                                   | using the [Poll                   |
|                                   | Enrich](com                       |
|                                   | ponents:eips:pollEnrich-eip.html) |
|                                   | EIP, or from Java by creating a   |
|                                   | `PollingConsumer` instance via    |
|                                   | the `createPollingConsumer()`     |
|                                   | method from `Endpoint`.           |
+-----------------------------------+-----------------------------------+
:::
:::::
:::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::::: sect1
## See Also {#_see_also}

::::: sectionbody
::: paragraph
See the following for high-level [architecture](architecture.html) of
Apache Camel.
:::

::: paragraph
See [Lifecycle](lifecycle.html) to understand the overall lifecycle of
the `CamelContext`.
:::
:::::
::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
