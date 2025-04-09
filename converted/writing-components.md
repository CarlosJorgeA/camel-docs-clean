::: {#header}
# Writing Components
:::

:::::::::::::::::::::::::::::: {#content}
::::::::: {#preamble}
:::::::: sectionbody
::: paragraph
Apache Camel is designed to make it very easy to drop in new components
whether they be routing components, transformers, transports etc. The
idea of a component is to be a factory and manager of
[Endpoints](endpoint.html).
:::

::: paragraph
Here are the main steps to add a new component:
:::

::: ulist
- Write a POJO which implements the `Component` interface. The simplest
  approach is just to derive from `DefaultComponent`.

- To support auto-discovery of your component, add a file of
  `META-INF/services/org/apache/camel/component/FOO` where FOO is the
  URI scheme for your component and any related endpoints created on the
  fly. This file should contain the information of the component class
  full name. For example if your component is implemented by the
  `com.example.CustomComponent` class, the file should contain the
  following line --- `class=com.example.CustomComponent`.
:::

::: paragraph
Users can then either explicitly create your component, configure it and
register it with a `CamelContext` or they can use a URI which
auto-creates your component.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | ::: paragraph                     |
| Note                              | It is recommended to bootstrap    |
| :::                               | your initial component using      |
|                                   | [Camel Maven                      |
|                                   | Archetyp                          |
|                                   | es](camel-maven-archetypes.html), |
|                                   | as it will give you all the       |
|                                   | necessary bits to start           |
|                                   | developing your component with    |
|                                   | ease. You will need as well to    |
|                                   | make sure to have [Camel          |
|                                   | Component Maven                   |
|                                   | Plugin](c                         |
|                                   | amel-component-maven-plugin.html) |
|                                   | included in your component's      |
|                                   | `pom.xml` file, order to generate |
|                                   | all the necessary metadata and    |
|                                   | Java files for your component.    |
|                                   | :::                               |
+-----------------------------------+-----------------------------------+
:::
::::::::
:::::::::

::::::: sect1
## Writing Endpoints {#_writing_endpoints}

:::::: sectionbody
::: paragraph
When implementing an [Endpoint](endpoint.html) you typically may
implement one or more of the following methods:
:::

::: ulist
- `createProducer` will create a producer for sending message exchanges
  to the endpoint

- `createConsumer` implements the [Event Driven
  Consumer](components:eips:eventDrivenConsumer-eip.html) pattern for
  consuming message exchanges from the endpoint.
:::

::: paragraph
Typically, you just derive from `DefaultEndpoint`
:::
::::::
:::::::

::::::: sect1
## Annotating your Endpoint {#_annotating_your_endpoint}

:::::: sectionbody
::: paragraph
If you want to benefit from the automatic generation of HTML
documentation for all the parameters on your endpoint as part of the
maven site reports, you need to [annotate your Endpoint's
parameters](endpoint-annotations.html).
:::

::: paragraph
So this means you add a `@UriEndpoint` annotation to your Endpoint class
and then annotate each parameter you wish to be configured via the URI
configuration mechanism with `@UriParam` (or `@UriParams` for nested
configuration objects).
:::

::: paragraph
Refer to the [Endpoint Annotations guide for
details](endpoint-annotations.html).
:::
::::::
:::::::

:::::::::::: sect1
## Options {#_options}

::::::::::: sectionbody
::: paragraph
If your component has options you can let it have public getters/setters
and Camel will automatically set the properties when the endpoint is
created.
:::

::: paragraph
If you however want to take the matter in your own hands, then you must
remove the option from the given parameter list as Camel will validate
that all options are used. If not Camel will throw a
`ResolveEndpointFailedException` stating which options are unknown.
:::

::: paragraph
The parameters are provided by Camel in the `createEndpoint` method from
`DefaultComponent` class:
:::

:::: listingblock
::: content
``` highlight
protected abstract Endpoint<E> createEndpoint(String uri, String remaining, Map parameters)
```
:::
::::

::: paragraph
The code is an example from the [SEDA](components::seda-component.html)
component that removes the size parameter:
:::

:::: listingblock
::: content
``` highlight
    public BlockingQueue<Exchange> createQueue(String uri, Map parameters) {
        int size = 1000;
        Object value = parameters.remove("size");
        if (value != null) {
            Integer i = convertTo(Integer.class, value);
            if (i != null) {
                size = i;
            }
        }
        return new LinkedBlockingQueue<Exchange>(size);
    }
```
:::
::::
:::::::::::
::::::::::::
::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
