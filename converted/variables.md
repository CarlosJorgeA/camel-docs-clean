::: {#header}
# Variables
:::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
:::::::: {#preamble}
::::::: sectionbody
::: paragraph
**Available from Camel 4.4**
:::

::: paragraph
In Camel 4.4, we have introduced the concept of *variables*.
:::

::: paragraph
A variable is a key/value that can hold a value that can either be
private per `Exchange`, or shared per route, or per `CamelContext`.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | You can also use *exchange        |
| Note                              | properties* as variables, but the |
| :::                               | exchange properties are also used |
|                                   | internally by Camel, and some     |
|                                   | EIPs and components. With the     |
|                                   | newly introduced *variables* then |
|                                   | these are exclusively for end     |
|                                   | users.                            |
+-----------------------------------+-----------------------------------+
:::
:::::::
::::::::

:::::::::::: sect1
## Variable Repository {#_variable_repository}

::::::::::: sectionbody
::: paragraph
The variables are stored in one or more
`org.apache.camel.spi.VariableRepository`. By default, there are the
following repositories
:::

::: ulist
- `ExchangeVariableRepository` - A private repository per `Exchange`
  that holds variables that are private for the lifecycle of each
  `Exchange`.

- `RouteVariableRepository` - Uses `route` as id. A single repository,
  that holds variables per `Route`.

- `GlobalVariableRepository` - Uses `global` as id. A single global
  repository for the entire `CamelContext`.
:::

::: paragraph
The `ExchangeVariableRepository` is special as its private per exchange
and is the default repository when used during routing. The
`RouteVariableRepository` is a single repository that holds variables
that are route scoped.
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | There is also                     |
| Tip                               | `org.apache.camel                 |
| :::                               | .spi.BrowsableVariableRepository` |
|                                   | which is an extension to          |
|                                   | `VariableRepository` that has     |
|                                   | APIs to browse the current        |
|                                   | variables. Camel uses this with   |
|                                   | Camel JBang, and JMX to be able   |
|                                   | to see the current variables from |
|                                   | management tooling, CLI, and the  |
|                                   | developer console.                |
+-----------------------------------+-----------------------------------+
:::

:::::: sect2
### Custom variable repositories {#_custom_variable_repositories}

::: paragraph
You can implement custom `org.apache.camel.spi.VariableRepository` and
plugin to be used out of the box with Apache Camel. For example, you can
build a custom repository that stores the variables in a database, so
they are persisted.
:::

::: paragraph
Each repository must have its own unique id. However, it's also possible
to replace the default `global`, or `route` repositories with another.
:::

::: {.admonitionblock .important}
+-----------------------------------+-----------------------------------+
| ::: title                         | The id `exchange` and `header` is |
| Important                         | reserved by Camel internally and  |
| :::                               | should not be used as id for      |
|                                   | custom repositories.              |
+-----------------------------------+-----------------------------------+
:::
::::::
:::::::::::
::::::::::::

:::::::::::::::::::::: sect1
## Getting and setting variables from Java API {#_getting_and_setting_variables_from_java_api}

::::::::::::::::::::: sectionbody
::: paragraph
To get a local variable from the current exchange, you can do this via
Java API:
:::

:::: listingblock
::: content
``` highlight
String myValue = "...";
exchange.setVariable("myKey", myValue);

// and later to get the variable
Object val = exchange.getVariable("myKey");

// you can get the value as a specific type
String str = exchange.getVariable("myKey", String.class);
```
:::
::::

::: paragraph
The API on `Exchange` will by default get the variables from its local
private repository. However, you can also get variables from other
repositories, such as the `global` as show:
:::

:::: listingblock
::: content
``` highlight
Object val = exchange.getVariable("global:myGlobalKey");
```
:::
::::

::: paragraph
And you can also assign a global variable by prefixing with `global:` as
follows:
:::

:::: listingblock
::: content
``` highlight
exchange.setVariable("global:myGlobalKey", someObjectHere);
```
:::
::::

::: paragraph
There is also API on `CamelContext` to get variables. However, this API
will by default get from the `global` repository, as it's not possible
to get variables from any inflight `Exchange` currently being routed.
:::

:::: listingblock
::: content
``` highlight
Object val = context.getVariable("myGlobalKey");

// you can get the value as a specific type
String str = context.getVariable("myGlobalKey", String.class);
```
:::
::::

::: paragraph
You can also assign a variable to a specific route with `route:` as
follows:
:::

:::: listingblock
::: content
``` highlight
exchange.setVariable("route:myRouteId:myRouteKey", someObjectHere);
```
:::
::::

::: paragraph
And you can get route variables as well:
:::

:::: listingblock
::: content
``` highlight
Object val = context.getVariable("route:myRouteId:myRouteKey");

// you can get the value as a specific type
String str = context.getVariable("route:myRouteId:myRouteKey", String.class);
```
:::
::::
:::::::::::::::::::::
::::::::::::::::::::::

:::::::::::::: sect1
## Setting and getting variables from DSL {#_setting_and_getting_variables_from_dsl}

::::::::::::: sectionbody
::: paragraph
It is also possible to use variables in Camel [routes](routes.html)
using the setVariable, removeVariable, and convertVariableTo EIPs.
:::

::: paragraph
These EIPs make it possible to set and remove variables from routes. And
you can also access variables from the
[Simple](components:languages:simple-language.html) language.
:::

::: paragraph
In the following route, we set a variable on the exchange which we use
later to build a human-readable event message:
:::

::::: exampleblock
:::: content
::: dlist

Java

:   :::: listingblock
    ::: content
    ``` highlight
    from("kafka:order.in")
      .setVariable("customerId", jq(".customer.custId"))
      .setVariable("country", jq(".customer.address.co"))
      .transform().simple("Order received from customer ${variable.customerId} in ${variable.country}")
      .to("kafka:order.event");
    ```
    :::
    ::::

XML

:   :::: listingblock
    ::: content
    ``` highlight
    <route>
        <from uri="kafka:order.in"/>
        <setVariable name="customerId">
            <jq>.customer.custId</jq>
        </setVariable>
        <setVariable name="country">
            <jq>.customer.address.co</jq>
        </setVariable>
        <transform>
            <simple>Order received from customer ${variable.customerId} in ${variable.country}</simple>
        </transform>
        <to uri="kafka:order.event"/>
    </route>
    ```
    :::
    ::::

YAML

:   :::: listingblock
    ::: content
    ``` highlight
    - route:
        from:
          uri: kafka:order.in
          steps:
            - setVariable:
                name: customerId
                jq:
                  expression: .customer.custId
            - setVariable:
                name: country
                jq:
                  expression: .customer.address.co
            - transform:
                simple:
                  expression: "Order received from customer ${variable.customerId} in ${variable.country}"
            - to:
                uri: kafka:order.event
    ```
    :::
    ::::
:::
::::
:::::

::: paragraph
When `route` variables in Camel routes, then the `routeId` is implied as
the current route, if not explicit declared. For example, the following
example the first route will set a variable (`route:second:foo`) in the
second route. Then the second route can get hold of the variable without
having to specify its route id `route:foo`:
:::

::::: exampleblock
:::: content
::: dlist

Java

:   :::: listingblock
    ::: content
    ``` highlight
    from("direct:start").routeId("first")
        // sets variable in another route
        .setVariable("route:second:foo").constant("Hello World")
        .to("mock:end");

    from("direct:second").routeId("second")
        // use variable from this route
        .setBody().variable("route:foo");
    ```
    :::
    ::::
:::
::::
:::::
:::::::::::::
::::::::::::::

:::::::::::::::::::::::::: sect1
## Configuring initial variables on startup {#_configuring_initial_variables_on_startup}

::::::::::::::::::::::::: sectionbody
::: paragraph
When Camel is starting then it's possible to configure initial variables
for `global` and `route` repositories only.
:::

::: paragraph
This can be done in `application.properties` as shown below:
:::

:::: listingblock
::: content
``` highlight
camel.variable.greeting = Random number
camel.variable.random = 999
```
:::
::::

::: paragraph
The variables are default set on the `global` repository, but you can
set route scoped variables, using `route.` as prefix. As we cannot use
colon (`:`) in property keys, then dot is used to separate the route id
from the variable name, eg `myRoute.gold`.
:::

:::: listingblock
::: content
``` highlight
camel.variable.route.myRoute.gold = true
camel.variable.greeting = Random number
camel.variable.random = 999
```
:::
::::

::: paragraph
Here the gold variable is set on the `route` repository, and the other
variables are set on the `global` repository.
:::

::: paragraph
The value of a variable can also be loaded from the file system, such as
a JSon file. To do this, you should prefix the value with
`resource:file:` or `resource:classpath:` to load from the file system
or classpath, as shown below:
:::

:::: listingblock
::: content
``` highlight
camel.variable.user-template = resource:file:/etc/user.json
```
:::
::::

::: paragraph
Camel (**Camel 4.8**) will automatically convert the value to
appropriate type:
:::

::: ulist
- all digits are converted to an int or long

- true/false are converted to a boolean

- otherwise string value
:::

::: paragraph
There is also support for referring to other existing beans, using the
`#bean:` syntax:
:::

:::: listingblock
::: content
``` highlight
camel.variable.cheese = #bean:myCheeseBean
```
:::
::::

::: paragraph
Or create a new bean via the `#class:` or `#type:` syntax:
:::

:::: listingblock
::: content
``` highlight
camel.variable.cheese = #class:com.foo.MyClassName
```
:::
::::

::: paragraph
Or if the value must be of a special type, you can specify this via
`#valueAs` as follows:
:::

:::: listingblock
::: content
``` highlight
camel.variable.amount = #valueAs(float):1.23
```
:::
::::
:::::::::::::::::::::::::
::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::: sect1
## Using Variables with EIPs {#_using_variables_with_eips}

:::::::::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
The following commonly used EIPs for sending and receiving, and
transforming messages, have special support for choosing to use
variables over the current `Exchange`:
:::

::: ulist
- from

- to

- toD

- enrich

- poll

- pollEnrich

- wireTap

- unmarshal

- marshal
:::

::: paragraph
The intention is to make it more convenient and easy to *gather data*
from other systems without any ceremony to keep existing data by using
techniques such as storing the data temporary using headers, exchange
properties, or with the [Claim
Check](components:eips:claimCheck-eip.html) EIP.
:::

::::::::: sect2
### Important concept when using variables and EIPs {#_important_concept_when_using_variables_and_eips}

::: paragraph
It is **important** to understand that the variables focus the use of
the message **body** only. This is on purpose to keep it simpler and
primary work with the message body as the user data.
:::

::: paragraph
The following table summarizes what the EIP supports with variables:
:::

+----------------------+----------------------+-----------------------+
| **EIP**              | **VariableSend**     | **VariableReceive**   |
+----------------------+----------------------+-----------------------+
| From                 |                      | yes                   |
+----------------------+----------------------+-----------------------+
| To                   | yes                  | yes                   |
+----------------------+----------------------+-----------------------+
| ToD                  | yes                  | yes                   |
+----------------------+----------------------+-----------------------+
| Enrich               | yes                  | yes                   |
+----------------------+----------------------+-----------------------+
| Poll                 |                      | yes                   |
+----------------------+----------------------+-----------------------+
| PollEnrich           |                      | yes                   |
+----------------------+----------------------+-----------------------+
| WireTap              | yes                  |                       |
+----------------------+----------------------+-----------------------+
| Unmarshal            | yes                  | yes                   |
+----------------------+----------------------+-----------------------+
| Marshal              | yes                  | yes                   |
+----------------------+----------------------+-----------------------+

::: paragraph
The EIPs listed above have support for using variables when sending and
receiving data. This is done by using the `variableSend` and
`variableReceive` options to specify the name of the variable.
:::

::: paragraph
The EIPs works in two modes where **variableSend** and
**variableReceive** are a little bit different, so pay attention to the
following table:
:::

+-----------------------------------+-----------------------------------+
| **VariableSend**                  | **VariableReceive**               |
+-----------------------------------+-----------------------------------+
| **Sending Headers:** Message      | **Received Headers:** Variable    |
+-----------------------------------+-----------------------------------+
| **Sending Body:** Variable        | **Received Body:** Variable       |
+-----------------------------------+-----------------------------------+

::: paragraph
The **VariableSend** is intended for sending as regular Camel where the
sending headers are from the current `Message` and the body is from the
variable. In other words it's only the message body taken from the
variable instead of the current `Message` body.
:::

::: paragraph
The **VariableReceive** works in a different mode. The idea is that all
the received data is stored as variables. This means the current
`Message` is not changed at all. The received body is stored in the
variable, and the received headers (transport headers etc.) are stored
as read-only headers as variables as well. The names of the variable is
`header:variableName.headerName`. For example, if the variable is
`myVar` and the header is `Content-Type` then the header is stored as a
variable with the full name `header:myVar.Content-Type`.
:::
:::::::::

:::::::::::::::::::::::: sect2
### Example using Variable Receive {#_example_using_variable_receive}

::: paragraph
When the EIP is using **VariableReceive**, then the `Message` on the
`Exchange` is not in use, but the body and headers will be from the
variable. For example, given the following `Message` containing:
:::

:::: listingblock
::: content
``` highlight
header.foo=123
header.bar=456
body=Hello World
```
:::
::::

::: paragraph
And a remote service is called via the route below, and this service
returns a new header (`level`) and body: \'Bye World\'
:::

::::: exampleblock
:::: content
::: dlist

Java

:   :::: listingblock
    ::: content
    ``` highlight
    from("direct:service")
      .to("http:myservice")
      .to("log:after");
    ```
    :::
    ::::

XML

:   :::: listingblock
    ::: content
    ``` highlight
    <route>
      <from uri="direct:service"/>
      <to uri="http:myservice"/>
      <to uri="log:after"/>
    </route>
    ```
    :::
    ::::

YAML

:   :::: listingblock
    ::: content
    ``` highlight
    from:
      uri: "direct:service"
      steps:
        - to: "http:myservice"
        - to: "log:after"
    ```
    :::
    ::::
:::
::::
:::::

::: paragraph
Calling this route, the `Message` is updated to following:
:::

:::: listingblock
::: content
``` highlight
header.foo=123
header.bar=456
header.level=gold
body=Bye World
```
:::
::::

::: paragraph
However, if you use **VariableReceive=myVar** to store the returned data
from calling the remote service into a variable, then the result changes
as follows:
:::

::::: exampleblock
:::: content
::: dlist

Java

:   :::: listingblock
    ::: content
    ``` highlight
    from("direct:service")
      .toV("http:myservice", null, "myVar")
      .to("log:after");
    ```
    :::
    ::::

XML

:   :::: listingblock
    ::: content
    ``` highlight
    <route>
      <from uri="direct:service"/>
      <to uri="http:myservice" variableReceive="myVar"/>
      <to uri="log:after"/>
    </route>
    ```
    :::
    ::::

YAML

:   :::: listingblock
    ::: content
    ``` highlight
    from:
      uri: "direct:service"
      steps:
        - to:
            uri: http:myservice
            variableReceive: myVar
        - to: "log:after"
    ```
    :::
    ::::
:::
::::
:::::

::: paragraph
The `Message` on the current `Exchange` is not changed:
:::

:::: listingblock
::: content
``` highlight
header.foo=123
header.bar=456
body=Hello World
```
:::
::::

::: paragraph
And the variable contains all the data received from the remote HTTP
service separated into two variables:
:::

:::: listingblock
::: content
``` highlight
myVar=Bye World
header:myVar.level=gold
```
:::
::::

::: {.admonitionblock .important}
+-----------------------------------+-----------------------------------+
| ::: title                         | Notice the headers are stored     |
| Important                         | with the syntax                   |
| :::                               | `header:variable.key`. In the     |
|                                   | example above the variable name   |
|                                   | is `myVar`, and the header key is |
|                                   | `level`, which gives:             |
|                                   | `header:myVar.level`.             |
+-----------------------------------+-----------------------------------+
:::
::::::::::::::::::::::::

::::::::: sect2
### Using variable to store incoming message body {#_using_variable_to_store_incoming_message_body}

::: paragraph
You can configure the `from` to store the message body into a variable,
instead of the `Message`. This makes it easy to have quick access to the
original incoming message body via the variable. Notice that the body on
the `Message` will be `null`.
:::

::: paragraph
The following example from a unit test shows how to do this. Notice how
Java DSL uses `fromV` to make it possible to specify the name of the
variable. In XML and YAML DSL you specify this using the
`variableReceive` parameter.
:::

::::: exampleblock
:::: content
::: dlist

Java

:   :::: listingblock
    ::: content
    ``` highlight
    fromV("direct:start", "myKey")
        .transform().simple("Bye ${body}")
        .to("mock:foo")
        .setBody(variable("myKey"))
        .to("mock:result");
    ```
    :::
    ::::

XML

:   :::: listingblock
    ::: content
    ``` highlight
    <route>
      <from uri="direct:start" variableReceive="myKey"/>
      <transform>
        <simple>Bye ${body}</simple>
      </transform>
      <to uri="mock:foo"/>
      <setBody>
        <variable>myKey</variable>
      </setBody>
      <to uri="mock:result"/>
    </route>
    ```
    :::
    ::::

YAML

:   :::: listingblock
    ::: content
    ``` highlight
    from:
      uri: "direct:start"
      variableReceive: "myKey"
      steps:
        - transform:
            simple: "Bye ${body}"
        - to: "mock:foo"
        - setBody:
            variable: "myKey"
        - to: "mock:result"
    ```
    :::
    ::::
:::
::::
:::::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | In the examples above the         |
| Note                              | transform `Bye ${body}` will      |
| :::                               | result as `Bye ` because the      |
|                                   | `Message` has no message body, as |
|                                   | the incoming message body is      |
|                                   | stored in the variable `myKey`    |
|                                   | instead.                          |
+-----------------------------------+-----------------------------------+
:::
:::::::::
::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
