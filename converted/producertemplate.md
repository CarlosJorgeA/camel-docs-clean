::: {#header}
# ProducerTemplate
:::

:::::::::::::::::::::::::::::::::::::: {#content}
:::::::::: {#preamble}
::::::::: sectionbody
::: paragraph
The `ProducerTemplate` interface allows you to send message exchanges to
endpoints in a variety of different ways to make it easy to work with
Camel [Endpoint](endpoint.html) instances from Java code.
:::

::: paragraph
It can be configured with a default endpoint if you just want to send
lots of messages to the same endpoint; or you can specify an
[Endpoint](endpoint.html) or uri as the first parameter.
:::

::: paragraph
The `sendBody()` method allows you to send any object to an endpoint
easily as shown:
:::

:::: listingblock
::: content
``` highlight
ProducerTemplate template = exchange.getContext().createProducerTemplate();

// send to default endpoint
template.sendBody("<hello>world!</hello>");

// send to a specific queue
template.sendBody("activemq:MyQueue", "<hello>world!</hello>");

// send with a body and header
template.sendBodyAndHeader("activemq:MyQueue",
   "<hello>world!</hello>",
   "CustomerRating", "Gold");
```
:::
::::

::: paragraph
You can also supply an `Exchange` or a `Processor` to customize the
exchange.
:::
:::::::::
::::::::::

:::::::::: sect1
## Send vs Request methods {#_send_vs_request_methods}

::::::::: sectionbody
::: paragraph
The `ProducerTemplate` supports [Message Exchange
Patterns](exchange-pattern.html) (MEP) that are used to control the
messaging style to use:
:::

::: ulist
- *send methods* - [Event Message](components:eips:event-message.html)
  (InOnly)

- *request methods* - [Request
  Reply](components:eips:requestReply-eip.html) (InOut)
:::

::: paragraph
In other words, all the methods on the `ProducerTemplate` that starts
with `sendXXX` are for InOnly messaging, and all the methods starting
with `requestXXX` are for InOut messaging.
:::

::: paragraph
Lets see an example where we invoke an endpoint to get the response
(InOut):
:::

:::: listingblock
::: content
``` highlight
Object response = template.requestBody("<hello/>");

// you can type convert the response to what you want such as String
String ret = template.requestBody("<hello/>", String.class);

// or specify the endpoint uri in the method
String ret = template.requestBody("cxf:bean:HelloWorldService", "<hello/>", String.class);
```
:::
::::
:::::::::
::::::::::

:::::::::::::::::: sect1
## Fluent interface {#_fluent_interface}

::::::::::::::::: sectionbody
::: paragraph
The `FluentProducerTemplate` provides a fluent syntax over the regular
`ProducerTemplate`.
:::

::: paragraph
Here are some examples:
:::

:::::: sect2
### Set headers and body {#_set_headers_and_body}

::: paragraph
This is the most common style with fluent builders to set headers, and
message body as show:
:::

:::: listingblock
::: content
``` highlight
Integer result = FluentProducerTemplate.on(context)
    .withHeader("key-1", "value-1")
    .withHeader("key-2", "value-2")
    .withBody("Hello")
    .to("direct:inout")
    .request(Integer.class);
```
:::
::::
::::::

:::::: sect2
### Using a processor {#_using_a_processor}

::: paragraph
Here we use [Processor](processor.html) to prepare the message to be
sent.
:::

:::: listingblock
::: content
``` highlight
Integer result = FluentProducerTemplate.on(context)
    .withProcessor(exchange -> exchange.getIn().setBody("Hello World"))
    .to("direct:exception")
    .request(Integer.class);
```
:::
::::
::::::

:::::: sect2
### Advanced with a template customizer {#_advanced_with_a_template_customizer}

::: paragraph
This is rarely in use, but a `TemplateCustomizer` can be used for
advanced use-cases to control various aspects of the
`FluentProducerTemplate` such as configuring to use a custom thread
pool:
:::

:::: listingblock
::: content
``` highlight
Object result = FluentProducerTemplate.on(context)
    .withTemplateCustomizer(
        template -> {
            template.setExecutorService(myExecutor);
            template.setMaximumCacheSize(10);
        }
    )
    .withBody("the body")
    .to("direct:start")
    .request();
```
:::
::::
::::::
:::::::::::::::::
::::::::::::::::::

::::: sect1
## See Also {#_see_also}

:::: sectionbody
::: paragraph
See [ConsumerTemplate](consumertemplate.html)
:::
::::
:::::
::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
