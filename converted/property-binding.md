::: {#header}
# Property binding in Camel
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
:::::: {#preamble}
::::: sectionbody
::: paragraph
Camel supports binding property values (key=value) in many places such
as configuration of Camel components, endpoints, EIPs, routes, and Camel
bootstrap configuration.
:::

::: paragraph
Together with property placeholders, property placeholder functions,
then there is plenty of power, but also something that takes a little
learning to master.
:::
:::::
::::::

::::::: sect1
## Property binding features {#_property_binding_features}

:::::: sectionbody
::: paragraph
The core of Camels property binding is implemented in
`PropertyBindingSupport.java` which is used internally in Camel, and as
well can be used by Camel component developers.
:::

::: paragraph
The `PropertyBindingSupport` class supports binding String valued
properties to an instance which uses a set of conventions:
:::

::: ulist
- *property placeholders* - Keys and values using Camels property
  placeholder will be resolved.

- *nested* - Properties can be nested using the dot syntax (OGNL and
  builder pattern using with as prefix), eg `foo.bar=123`.

- *map* - Properties can lookup in Map's using map syntax, eg `foo[bar]`
  where foo is the name of the property that is a Map instance, and bar
  is the name of the key.

- *list* - Properties can refer or add to in List's using list syntax,
  eg `foo[0]` where foo is the name of the property that is a List
  instance, and 0 is the index. To refer to the last element, then use
  `last` as key.

- *reference by property placeholder id* - Values can refer to a
  property placeholder key with `#property:myKey`

- *reference by bean id* - Values can refer to other beans in the
  registry by prefixing with `#` or `#bean:` eg `#myBean` or
  `#bean:myBean`. It is recommended to favour using `#bean:` syntax to
  make it obvious it's a bean reference.

- *reference by type* - Values can refer to singleton beans by their
  type in the registry by prefixing with `#type:` syntax, eg
  `#type:com.foo.MyClassType`.

- *autowire by type* - Values can refer to singleton beans by auto
  wiring by setting the value to `#autowired`.

- *reference new class* - Values can refer to creating new beans by
  their class name by prefixing with `#class`, eg
  `#class:com.foo.MyClassType`. The class is created using a default
  no-arg constructor, however if you need to create the instance via a
  factory method then you specify the method as shown:
  `#class:com.foo.MyClassType#myFactoryMethod`. And if the factory
  method requires parameters they can be specified as follows:
  `#class:com.foo.MyClassType#myFactoryMethod('Hello World', 5, true)`.
  Or if you need to create the instance via constructor parameters then
  you can specify the parameters as shown:
  `#class:com.foo.MyClass('Hello World', 5, true)`. If the factory
  method is on another bean or class, then you must specify this as
  shown: `#class:com.foo.MyClassType#com.foo.MyFactory:myFactoryMethod`.
  Where `com.foo.MyFactory` either refers to a FQN classname, or can
  refer to an existing bean by id, such as:
  `#class:com.foo.MyClassType#myFactoryBean:myFactoryMethod`.

- *valueAs(type):value* - To declare that the value should be converted
  to the given type, such as `#valueAs(int):123` which indicates that
  the value 123 should be converted to an integer.

- *ignore case* - Whether to ignore case for property keys (will ignore
  by default)
:::
::::::
:::::::

:::::::::::::: sect1
## Property binding basics {#_property_binding_basics}

::::::::::::: sectionbody
::: paragraph
Do not get overwhelmed by the set of features and what they really do.
:::

::: paragraph
At the basics the property binding are used for setting values on Java
objects from string values (key=value).
:::

::: paragraph
For example to set brokers on the Kafka component you can do:
:::

:::: listingblock
::: content
``` highlight
camel.component.kafka.brokers = mykafka1,mykafka2
```
:::
::::

::: paragraph
This will essentially be equivalent to configuring Kafka component in
regular Java code via setters:
:::

:::: listingblock
::: content
``` highlight
KafkaComponent kafka = ...
kafka.setBrokers("mykafka1,mykafka2");
```
:::
::::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | For configuring Camel components  |
| Note                              | in Java code, there is also       |
| :::                               | [Component                        |
|                                   | DSL](component-dsl.html).         |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
The configuration of Camel components, endpoints, routes etc can often
require more flexibility and therefore the property binding has many
features to bind by looking up existing objects by id, or anonymously by
their type, and as well to walk down an object graph to bind nested
parameters.
:::
:::::::::::::
::::::::::::::

::::::::::::::::::::::::::::::::::: sect1
## Using PropertyBindingSupport in Java {#_using_propertybindingsupport_in_java}

:::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
Although `PropertyBindingSupport` is not primary intended for end users
to use, but nevertheless its possible to use, and also you may get a
better understanding of this feature by seeing how this class is used
with pure Java.
:::

::: paragraph
Suppose we have the following two POJOs `Foo.java` and `Bar.java`:
:::

:::: listingblock
::: content
``` highlight
public class Foo {
    private String name;
    private Bar bar = new Bar();

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Bar getBar() {
        return bar;
    }

    public void setBar(Bar bar) {
        this.bar = bar;
    }
}

public class Bar {
    private int age;
    private boolean rider;

    public int getAge() {
        return age;
    }

    public boolean isRider() {
        return rider;
    }

    // this has no setter but only builders and mix the builders with both styles

    public Bar withAge(int age) {
        this.age = age;
        return this;
    }

    public Bar withRider(boolean rider) {
        this.rider = rider;
        return this;
    }
}
```
:::
::::

::: paragraph
Then we can use `PropertyBindingSupport` to bind properties to these
POJOs:
:::

:::: listingblock
::: content
``` highlight
Foo foo = new Foo();

Map<String, Object> prop = new HashMap<>();
prop.put("name", "James");
prop.put("bar.age", "33");
prop.put("bar.rider", "true");

PropertyBindingSupport.bindProperties(context, foo, prop);
```
:::
::::

::: paragraph
This will then set the POJOs to have the following values:
:::

:::: listingblock
::: content
    Foo.name = James
    Foo.Bar.age = 33
    Foo.Bar.rider = true
:::
::::

::: paragraph
Instead of providing a map with all the parameters then a single
parameter can also be set using builder style as shown:
:::

:::: listingblock
::: content
``` highlight
Foo foo = new Foo();

PropertyBindingSupport.build().bind(context, foo, "name", "James");
PropertyBindingSupport.build().bind(context, foo, "bar.age", "33");
PropertyBindingSupport.build().bind(context, foo, "bar.rider", "true");
```
:::
::::

::: paragraph
Which is more common to do as follows:
:::

:::: listingblock
::: content
``` highlight
Foo foo = new Foo();

PropertyBindingSupport.build().withCamelContext(context).withTarget(foo)
    .withProperty("name", "James");
    .withProperty("bar.age", "33");
    .withProperty("bar.rider", "true")
    .bind();
```
:::
::::

::: paragraph
In the example above then we are setting nested values on foo via
`bar.age` and `bar.rider`. This is possible because Foo class has a
`getBar` method that returns the `Bar` instance to use:
:::

:::: listingblock
::: content
``` highlight
    private Bar bar = new Bar();

    public Bar getBar() {
        return bar;
    }
```
:::
::::

::: paragraph
It's a common practice for POJO classes to not create nested instances,
but instead on demand. So suppose `private Bar bar = new Bar();` was not
present in the Foo class. In this situation then Camel will automatic
create a new instance of `Bar` using its default no-arg constructor. For
more advanced use-cases then you can specify how the Bar instance should
be created, such as via a factory method, or pass in constructor
parameters.
:::

::: paragraph
For example suppose Bar has a constructor parameter that accepts a
boolean, we can pass that information via `#class:` as shown:
:::

:::: listingblock
::: content
``` highlight
PropertyBindingSupport.build().withCamelContext(context).withTarget(foo)
    .withProperty("name", "James");
    .withProperty("bar", "#class:com.mycompany.Bar(true)")
    .withProperty("bar.age", "33");
    .withProperty("bar.rider", "true")
    .bind();
```
:::
::::

:::::::::: sect2
### Using fluent builder class {#_using_fluent_builder_class}

::: paragraph
When you are in need to configure a bean via *fluent builder class*,
such as the following example:
:::

:::: listingblock
::: content
``` highlight
public class MyDriverBuilder {

 private String url;
 private String username;
 private String password;

 public MyDriverBuilder url(String url) {
     this.url = url;
     return this;
 }

 public MyDriverBuilder username(String username) {
     this.username = username;
     return this;
 }

 public MyDriverBuilder password(String password) {
     this.password = password;
     return this;
 }

 public MyDriver build() {
     return new MyDriver(url, username, password);
 }
}
```
:::
::::

::: paragraph
And you want to create an instance of `MyDriver` via the
`MyDriverBuilder` class, then this can be done as follows:
:::

:::: listingblock
::: content
``` highlight
MyDriver driver = PropertyBindingSupport.build()
  .withCamelContext(context)
  .withTarget(new MyDriverBuilder())
  .withFluentBuilder(true)
  .withProperty("url", "localhost:1234")
  .withProperty("username", "scott")
  .withProperty("password", "tiger")
  .build(MyDriver.class);
```
:::
::::

::: paragraph
Notice how we use the `build(MyDriver.class)` to build the bean via the
target class `.withTarget(new MyDriverBuilder())`. The build method will
by default invoke `build` as the builder method, but you can specify the
name, such as `.build(MyDriver.class, "myBuilderMethod");`
:::
::::::::::
::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::

:::::: sect1
## More details {#_more_details}

::::: sectionbody
::: paragraph
Property binding is notably used when running Camel in standalone mode
with Camel Main, or using Camel Spring Boot, Camel K, Camel Kafka
Connector, or Camel Quarkus. All these runtimes have a similar way of
configuring via property bindings such as from `application.properties`
files.
:::

::: paragraph
See more at [Camel Main](components:others:main.html)
:::
:::::
::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
