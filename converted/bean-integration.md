::: {#header}
# Bean Integration
:::

::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
::::: {#preamble}
:::: sectionbody
::: paragraph
Camel supports the integration of beans and POJOs in a number of ways.
:::
::::
:::::

:::::::::: sect1
## Annotations {#_annotations}

::::::::: sectionbody
::: paragraph
If a bean is defined in Spring XML or scanned using the Spring component
scanning mechanism, and a **\<camelContext\>** is used or a
`CamelBeanPostProcessor` then we process a number of Camel annotations
to do various things such as injecting resources or producing, consuming
or routing messages.
:::

::: paragraph
The following annotations is supported and inject by Camel's
`CamelBeanPostProcessor`
:::

+------+---------------------------------------------------------------+
| An   | Description                                                   |
| nota |                                                               |
| tion |                                                               |
+======+===============================================================+
| `    | To inject an endpoint, see more details at [POJO              |
| @End | Producing](pojo-producing.html).                              |
| poin |                                                               |
| tInj |                                                               |
| ect` |                                                               |
+------+---------------------------------------------------------------+
| `    | To inject a bean obtained from the [Registry](registry.html). |
| @Bea | See [Bean Injection](bean-injection.html).                    |
| nInj |                                                               |
| ect` |                                                               |
+------+---------------------------------------------------------------+
| `@B  | To inject a configuration bean obtained from the              |
| eanC | [Registry](registry.html). The bean is a POJO that represents |
| onfi | a set of configuration options, which is automatically        |
| gInj | configured with values loaded via Camel [Property             |
| ect` | Placeholders](using-propertyplaceholder.html).                |
+------+---------------------------------------------------------------+
| `    | To inject a value using property placeholder.                 |
| @Pro |                                                               |
| pert |                                                               |
| yInj |                                                               |
| ect` |                                                               |
+------+---------------------------------------------------------------+
| `@   | To inject a producer to send a message to an endpoint. See    |
| Prod | [POJO Producing](pojo-producing.html).                        |
| uce` |                                                               |
+------+---------------------------------------------------------------+
| `@   | To inject a consumer on a method. See [POJO                   |
| Cons | Consuming](pojo-consuming.html).                              |
| ume` |                                                               |
+------+---------------------------------------------------------------+
| `    | Used for binding a bean to the registry. If no name is        |
| @Bin | specified, then the bean will have its name auto computed     |
| dToR | based on the class name, field name, or method name where the |
| egis | annotation is configured.                                     |
| try` |                                                               |
+------+---------------------------------------------------------------+
| `    | Used to indicate that if the target type is                   |
| @Def | `CamelContextAware` then the `CamelContext` is deferred and   |
| erre | injected later; after the bootstrap of Camel so the           |
| dCon | `CamelContext` is ready for use.                              |
| text |                                                               |
| Bind |                                                               |
| ing` |                                                               |
+------+---------------------------------------------------------------+

::: paragraph
See more details at:
:::

::: ulist
- [POJO Consuming](pojo-consuming.html) to consume and possibly route
  messages from Camel

- [POJO Producing](pojo-producing.html) to make it easy to produce camel
  messages from your POJOs

- `@DynamicRouter` Annotation for creating a [Dynamic
  Router](components:eips:dynamicRouter-eip.html) from a POJO method

- `@RecipientList` Annotation for creating a [Recipient
  List](components:eips:recipientList-eip.html) from a POJO method

- `@RoutingSlip` Annotation for creating a [Routing
  Slip](components:eips:routingSlip-eip.html) for a POJO method

- [Bean Injection](bean-injection.html) to inject Camel related
  resources into your POJOs

- [Using Exchange Pattern
  Annotations](using-exchange-pattern-annotations.html) describes how
  the pattern annotations can be used to change the behaviour of method
  invocations with Spring Remoting or POJO Producing
:::

::: paragraph
**Example**
:::

::: paragraph
See the [POJO Messaging
Example](https://github.com/apache/camel-examples/tree/main/pojo-messaging)
for how to use the annotations for routing and messaging.
:::
:::::::::
::::::::::

:::::::::::::::::::::::::::::::::: sect1
## Using \@PropertyInject {#_using_propertyinject}

::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
Camel allows injecting property placeholders in POJOs using the
`@PropertyInject` annotation which can be set on fields and setter
methods. For example, you can use that with `RouteBuilder` classes, such
as shown below:
:::

:::: listingblock
::: content
``` highlight
public class MyRouteBuilder extends RouteBuilder {

    @PropertyInject("hello")
    private String greeting;

    @Override
    public void configure() throws Exception {
        from("direct:start")
            .transform().constant(greeting)
            .to("{{result}}");
    }
}
```
:::
::::

::: paragraph
Notice we have annotated the greeting field with `@PropertyInject` and
define it to use the key `hello`. Camel will then lookup the property
with this key and inject its value, converted to a String type.
:::

::: paragraph
You can also use multiple placeholders and text in the key, for example
we can do:
:::

:::: listingblock
::: content
``` highlight
@PropertyInject("Hello {{name}} how are you?")
private String greeting;
```
:::
::::

::: paragraph
This will lookup the placeholder with they key `name`.
:::

::: paragraph
You can also add a default value if the key does not exist, such as:
:::

:::: listingblock
::: content
``` highlight
@PropertyInject(value = "myTimeout", defaultValue = "5000")
private int timeout;
```
:::
::::

::::::::::::::::::::: sect2
### Using \@PropertyInject with arrays, lists, sets or maps {#_using_propertyinject_with_arrays_lists_sets_or_maps}

::: paragraph
You can also use `@PropertyInject` to inject an array of values. For
example, you may configure multiple hostnames in the configuration file,
and need to inject this into an `String[]` or `List<String>` field. To
do this, you need to tell Camel that the property value should be split
using a separator, as follows:
:::

:::: listingblock
::: content
``` highlight
@PropertyInject(value = "myHostnames", separator = ",")
private String[] servers;
```
:::
::::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | You can also use list/set types,  |
| Tip                               | such as `List<String>` or         |
| :::                               | `Set<String>` instead of array.   |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
Then in the `application.properties` file you can define the servers:
:::

:::: listingblock
::: content
``` highlight
myHostnames = serverA, serverB, serverC
```
:::
::::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | This also works for fields that   |
| Tip                               | are not String based, such as     |
| :::                               | `int[]` for numeric values.       |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
For `Map` types then the values are expected to be in *`key=value`*
format, such as:
:::

:::: listingblock
::: content
``` highlight
myServers = serverA=http://coolstore:4444,serverB=http://megastore:5555
```
:::
::::

::: paragraph
You can then inject this into a `Map` as follows:
:::

:::: listingblock
::: content
``` highlight
@PropertyInject(value = "myServers", separator = ",")
private Map servers;
```
:::
::::

::: paragraph
You can use generic types in the Map such as the values should be
`Integer` values:
:::

:::: listingblock
::: content
``` highlight
@PropertyInject(value = "ports", separator = ",")
private Map<String, Integer> ports;
```
:::
::::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | The generic type can only be a    |
| Note                              | single class type, and cannot be  |
| :::                               | a nested complex type such as     |
|                                   | `Map<String,Map<Kind,Priority>>`. |
+-----------------------------------+-----------------------------------+
:::
:::::::::::::::::::::
:::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::

::::: sect1
## See Also {#_see_also}

:::: sectionbody
::: ulist
- [Bean Injection](bean-injection.html)

- [Bean Binding](bean-binding.html)
:::
::::
:::::
:::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
