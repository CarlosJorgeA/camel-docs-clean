::: {#header}
# Components
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
::::: {#preamble}
:::: sectionbody
::: paragraph
Components are a fundamental building block of Apache Camel and are used
to connect routes to a wide variety of external systems and services.
Camel comes with a large number of built-in components that provide
connectivity to a wide range of technologies and protocols, such as
HTTP, JMS, file, and many others. You can also create a custom
components if the built-in components do not meet your needs.
:::
::::
:::::

:::::::::: sect1
## Components: the basics {#_components_the_basics}

::::::::: sectionbody
::: paragraph
A Component is essentially a factory of Endpoint instances. Typically,
applications shouldn't need to interact directly with a component.
However, there are some circumstances where manipulating the component
may be beneficial for the performance, operation, or scalability of the
application. In such cases, these applications may use the context to
get access to an instance of the endpoint they need to manipulate. The
applications can do so by using the method `CamelContext.getEndpoint()`.
This method returns an implementation of the `Component` interface that
is appropriate for the component requested. For instance, consider the
following code:
:::

:::: listingblock
::: content
``` highlight
myCamelContext.getEndpoint("pop3://john.smith@mailserv.example.com?password=myPassword");
```
:::
::::

::: paragraph
For the URI given in the above example, the `CamelContext` object would
map the `pop3` prefix to an instance of the `MailComponent` class.
:::

::: paragraph
The parameter to `getEndpoint()` is a URI. The URI *scheme* (that is,
the part before `:`) specifies the name of a component. Internally, the
`CamelContext` object maintains a mapping from the names of components
to `Component` objects.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | ::: paragraph                     |
| Note                              | Our documentation contains an     |
| :::                               | in-depth overview of the          |
|                                   | [C                                |
|                                   | omponent](manual::component.html) |
|                                   | if you want to learn more about   |
|                                   | it, including important details   |
|                                   | necessary to write your own.      |
|                                   | :::                               |
+-----------------------------------+-----------------------------------+
:::
:::::::::
::::::::::

::::::::: sect1
## Configuring Component Options {#_configuring_component_options}

:::::::: sectionbody
::: paragraph
Camel components are configured on two separate levels:
:::

::: ulist
- component level

- endpoint level
:::

::: paragraph
The component level is the highest level which holds general and common
configurations that are inherited by the endpoints. For example, a
component may have security settings, credentials for authentication,
urls for network connection and so forth.
:::

::: paragraph
Some components only have a few options, and others may have many.
Because components typically have pre-configured defaults that are
commonly used, then you may often only need to configure a few options
on a component; or none at all.
:::

::: paragraph
Configuring components can be done with the [Component
DSL](component-dsl.html), in a configuration file
(application.properties\|yaml), or directly with Java code.
:::
::::::::
:::::::::

:::::::::::::::::::::::::::::::::::: sect1
## Configuring Endpoint Options {#_configuring_endpoint_options}

::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
Typically, you want to configure endpoints, as endpoints often have many
options, which allows you to configure what you need the endpoint to do.
These endpoint options are also categorized according to the type of
endpoint:
:::

::: ulist
- consumer (*from*)

- producer (*to*)

- or both.
:::

::: paragraph
Configuring endpoints is most often done directly in the endpoint URI as
path and query parameters. You can also use the [Endpoint
DSL](Endpoint-dsl.html) as a *type safe* way of configuring endpoints.
:::

::: paragraph
A good practice when configuring options is to use [Property
Placeholders](using-propertyplaceholder.html), which allow us to avoid
using hardcoded urls, port numbers, sensitive information, and other
settings. In other words, with placeholders you can externalize the
configuration from your code, thus obtaining more flexibility and reuse.
:::

::::::: sect2
### How Camel maps names to components {#_how_camel_maps_names_to_components}

::: paragraph
In the getting started guide, we explained that Camel maintains a map of
names to components. This raises the question of how Camel populates
this map with named `Component` objects.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | ::: paragraph                     |
| Note                              | Normally, application developers  |
| :::                               | don't need to worry about this.   |
|                                   | However, this information is      |
|                                   | fundamental if you are writing a  |
|                                   | custom component.                 |
|                                   | :::                               |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
There are two ways of populating the map.
:::

::: ulist
- Programmatically, using the context

- Via lazy-initialization
:::
:::::::

::::::: sect2
### Programmatically {#_programmatically}

::: paragraph
The programmatic way is for application-level code to invoke
`CamelContext.addComponent(String componentName, Component component)`.
:::

::: paragraph
The example below shows a single `MailComponent` object being registered
in the map under 3 different names.
:::

:::: listingblock
::: content
``` highlight
Component mailComponent = new org.apache.camel.component.mail.MailComponent();
myCamelContext.addComponent("pop3", mailComponent);
myCamelContext.addComponent("imap", mailComponent);
myCamelContext.addComponent("smtp", mailComponent);
```
:::
::::
:::::::

:::::::::::::::::::: sect2
### Lazy-initialization {#_lazy_initialization}

::: paragraph
The second (and preferred) way to populate the map of named `Component`
objects in the `CamelContext` object is to let the `CamelContext` object
perform lazy initialization.
:::

::: paragraph
This approach relies on developers following a convention when they
write a class that implements the `Component` interface. For instance,
let's assume you write a class called
`com.example.myproject.FooComponent` and you want Camel to automatically
recognize this by the name `foo`. To do this, you write a properties
file called `META-INF/services/org/apache/camel/component/foo` (without
a `.properties` file extension) that has a single entry in it called
`class`, the value of which is the fully-scoped name of your class. For
instance:
:::

::::: listingblock
::: title
META-INF/services/org/apache/camel/component/foo
:::

::: content
``` highlight
class=com.example.myproject.FooComponent
```
:::
:::::

::: paragraph
If you want Camel to also recognize the class by the name `bar` then you
write another properties file in the same directory called `bar` that
has the same contents. Once you have written the properties file(s), you
create a JAR file that contains the `com.example.myproject.FooComponent`
class and the properties file(s), and you add this jar file to your
CLASSPATH. Then, when application-level code invokes
`createEndpoint("foo:…​")` on a `CamelContext` object, Camel will find
the \"foo\"\" properties file on the CLASSPATH, get the value of the
`class` property from that properties file, and use reflection APIs to
create an instance of the specified class.
:::

::: paragraph
Camel provides out-of-the-box support for various communication
technologies. This support consists of classes that implement the
`Component` interface plus properties files that enable a `CamelContext`
object to populate its map of named `Component` objects.
:::

::: paragraph
Earlier in this guide, we provided the following example of calling
`CamelContext.getEndpoint()`:
:::

:::: listingblock
::: content
``` highlight
myCamelContext.getEndpoint("pop3://john.smith@mailserv.example.com?password=myPassword");
```
:::
::::

::: paragraph
We originally referred to the parameter as a URI because the online
Camel documentation and the Camel source code both claim the parameter
is a URI. In reality, the parameter is restricted to being a URL. This
is because when Camel extracts the component name from the parameter, it
looks for the first \":\", which is a simplistic algorithm.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | ::: paragraph                     |
| Note                              | Remember that in the [Getting     |
| :::                               | Started With                      |
|                                   | Camel](camel                      |
|                                   | -core:getting-started:index.html) |
|                                   | we explained that a URI could be  |
|                                   | a URL or a URN.                   |
|                                   | :::                               |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
Now consider the following calls to `getEndpoint`:
:::

:::: listingblock
::: content
``` highlight
myCamelContext.getEndpoint("pop3:...");
myCamelContext.getEndpoint("jms:...");
myCamelContext.getEndpoint("urn:foo:...");
myCamelContext.getEndpoint("urn:bar:...");
```
:::
::::

::: paragraph
Camel identifies the components in the above example as `pop3`, `jms`,
`urn`, and `urn`. It would be more useful if the latter components were
identified as `urn:foo` and `urn:bar` or as `foo` and `bar` (that is, by
skipping over the `urn:` prefix). So, in practice, you must identify an
endpoint with a URL (a string of the form `<scheme>:…​`) rather than with
a URN (a string of the form `urn:<scheme>:…​`). This lack of proper
support for URNs means that you should consider the parameter to the
method `getEndpoint()` as being a URL rather than (as claimed) a URI.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | ::: paragraph                     |
| Note                              | Make sure to read [How do I       |
| :::                               | configure                         |
|                                   | endpoints?](faq:h                 |
|                                   | ow-do-i-configure-endpoints.html) |
|                                   | to learn more about configuring   |
|                                   | endpoints. For example, how to    |
|                                   | refer to beans in the             |
|                                   | [Registry](registry.html) or how  |
|                                   | to use raw values for password    |
|                                   | options, and using [property      |
|                                   | placeholders                      |
|                                   | ](using-propertyplaceholder.html) |
|                                   | etc.                              |
|                                   | :::                               |
+-----------------------------------+-----------------------------------+
:::
::::::::::::::::::::
:::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::

:::::: sect1
## Component DSL {#_component_dsl}

::::: sectionbody
::: paragraph
Component-DSL is a builder API that allows using type-safe construction
of Camel Components and injecting them directly to the [Camel
Context](camelcontext.html) instead of initializing through a
constructor.
:::

::: ulist
- [Component DSL](manual::component-dsl.html)
:::
:::::
::::::

::::: sect1
## Writing Components {#_writing_components}

:::: sectionbody
::: paragraph
This is an advanced topic and described in more detail in the [Writing
Components Guide](writing-components.html).
:::
::::
:::::

::::: sect1
## See Also {#_see_also}

:::: sectionbody
::: ulist
- List of all Camel [Components](components::index.html)
:::
::::
:::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
