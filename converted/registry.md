::: {#header}
# Registry
:::

:::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
:::::: {#preamble}
::::: sectionbody
::: paragraph
The `org.apache.camel.spi.Registry` API is a common API to lookup beans
in any kind of runtime platform, whether you run Camel on Spring Boot,
Quarkus, Standalone, Kafka or something else.
:::

::: paragraph
Camel uses the `DefaultRegistry` that based on which runtime used
(Spring Boot, Quarkus, etc.) will first look up beans from the runtime
platform, and fallback to Camel's own `SimpleRegistry`.
:::
:::::
::::::

:::::::::::::::::::::::::::::: sect1
## Registry API {#_registry_api}

::::::::::::::::::::::::::::: sectionbody
::: paragraph
The registry has two sets of APIs:
:::

::: ulist
- binding

- lookup
:::

::: paragraph
The binding API is used to add new beans into the registry. The lookup
is used for looking up existing beans from the registry.
:::

::::::::::::::::::::::::: sect2
### Binding API {#_binding_api}

::: paragraph
The binding API is as follows:
:::

:::: listingblock
::: content
``` highlight
public interface Registry extends BeanRepository {

    /**
     * Binds the bean to the repository (if possible).
     * If the bean is CamelContextAware then the registry will automatic inject the context if possible.
     *
     * @param  id    the id of the bean
     * @param  bean  the bean
     */
    void bind(String id, Object bean);

    /**
     * Binds the bean to the repository (if possible).
     * Binding by id and type allows to bind multiple entries with the same id but with different type.
     * If the bean is CamelContextAware then the registry will automatic inject the context if possible.
     *
     * @param  id    the id of the bean
     * @param  type  the type of the bean to associate the binding
     * @param  bean  the bean
     */
    void bind(String id, Class<?> type, Object bean);

}
```
:::
::::

::: paragraph
If you, for example, need to add a bean to the `Registry` then you can
easily do this from Java as follows:
:::

:::: listingblock
::: content
``` highlight
Object myFoo = ...
camelContext.getRegistry().bind("foo", myFoo);
```
:::
::::

::: paragraph
Then you can access the bean by the id, such as from a Camel route:
:::

:::: listingblock
::: content
``` highlight
from("jms:cheese").bean("foo");
```
:::
::::

:::::: sect3
#### Binding in Spring XML {#_binding_in_spring_xml}

::: paragraph
If you use the Spring XML file, then any `<bean>` is automatic handled
by Spring itself, and registered into Spring bean container; which means
there is no need to bind the bean from Camel also.
:::

:::: listingblock
::: content
``` highlight
<bean id="foo" class="com.foo.MyFoo"/>
```
:::
::::
::::::

::::::: sect3
#### Binding in Spring Boot {#_binding_in_spring_boot}

::: paragraph
When using Spring Boot, then you can also use annotations to declare
beans such as with the `@Bean` annotation on the method that creates the
bean:
:::

:::: listingblock
::: content
``` highlight
@Bean
public MyFoo foo() {
    return new MyFoo();
}
```
:::
::::

::: paragraph
This is a common functionality of Spring Boot, and you can find
information about this in the Spring Boot project documentation.
:::
:::::::

:::::: sect3
#### Binding in Quarkus {#_binding_in_quarkus}

::: paragraph
Quarkus has similar functionality like Spring Boot to declare beans,
which can be done with the `javax.inject.enterprise.Produces` and
`javax.inject.Named` annotations:
:::

:::: listingblock
::: content
``` highlight
@Produces @Named("foo")
public MyFoo foo() {
    return new MyFoo();
}
```
:::
::::
::::::
:::::::::::::::::::::::::
:::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::

:::::::::::::::::: sect1
## Lookup API {#_lookup_api}

::::::::::::::::: sectionbody
::: paragraph
Registry is mostly used for looking up beans by their IDs, or by type.
This is heavily used during startup of Camel where Camel is wiring up
all components, endpoints, routes, processors, beans and so forth.
:::

::: paragraph
The lookup API is the following methods:
:::

:::: listingblock
::: content
``` highlight
public interface BeanRepository {

    /**
     * Looks up a bean in the registry based purely on name, returning the bean or null if it could not be found.
     *
     * Important: Multiple beans of different types may be bound with the same name, and its encouraged to use the
     * lookupByNameAndType(String, Class) to lookup the bean with a specific type, or to use any of the
     * find methods.
     *
     * @param  name the name of the bean
     * @return      the bean from the registry or null if it could not be found
     */
    Object lookupByName(String name);

    /**
     * Looks up a bean in the registry, returning the bean or null if it could not be found.
     *
     * @param  name the name of the bean
     * @param  type the type of the required bean
     * @return      the bean from the registry or null if it could not be found
     */
    <T> T lookupByNameAndType(String name, Class<T> type);

    /**
     * Finds beans in the registry by their type.
     *
     * @param  type the type of the beans
     * @return      the types found, with their bean ids as the key. Returns an empty Map if none found.
     */
    <T> Map<String, T> findByTypeWithName(Class<T> type);

    /**
     * Finds beans in the registry by their type.
     *
     * @param  type the type of the beans
     * @return      the types found. Returns an empty Set if none found.
     */
    <T> Set<T> findByType(Class<T> type);

}
```
:::
::::

::: paragraph
You can lookup beans from Java code as shown:
:::

:::: listingblock
::: content
``` highlight
// lookup by id only
Object foo = camelContext.getRegistry().lookupByName("foo");

// lookup by type so there is no need for type casting
MyFoo foo2 = camelContext.getRegistry().lookupByNameAndType("foo", MyFoo.class);
```
:::
::::

::::::::: sect2
### Looking up beans {#_looking_up_beans}

::: paragraph
You can also use dependency injection that will look up the bean via the
Camel registry. If you use a runtime platform such as Spring Boot or
Quarkus, then they come with their own functionality for this. Camel
also has its own bean injection annotation `@BeanInject` which can be
used when running Camel standalone.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | You can also use `@BeanInject`    |
| Note                              | from Camel in Spring Boot or      |
| :::                               | Quarkus; but this requires the    |
|                                   | class with the bean injection is  |
|                                   | *managed* by Camel (such as a     |
|                                   | `RouteBuilder` class); which may  |
|                                   | not always be the case.           |
|                                   | Therefore, it is best to only use |
|                                   | the Spring Boot or Quarkus        |
|                                   | annotations.                      |
+-----------------------------------+-----------------------------------+
:::

:::: sect3
#### Lookup in Spring Boot {#_lookup_in_spring_boot}

::: paragraph
When using Spring Boot, you can use Spring annotations such as
`@Autowired` or `@Inject` for dependency injection.
:::
::::

:::: sect3
#### Lookup in Quarkus {#_lookup_in_quarkus}

::: paragraph
When using Quarkus, you can use CDI annotations such as `@Inject` and
`@Named` for dependency injection.
:::
::::
:::::::::
:::::::::::::::::
::::::::::::::::::

::::: sect1
## More Information {#_more_information}

:::: sectionbody
::: paragraph
See [Bean Injection](bean-injection.html) and [Bean
Integration](bean-integration.html) for more details on working with
Beans in Camel.
:::
::::
:::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
