::: {#header}
# Component DSL
:::

:::::::::::::::::::::::::: {#content}
:::::: {#preamble}
::::: sectionbody
::: paragraph
Component-DSL is a builder API that allows using type-safe construction
of Camel [Components](component.html) and injecting them directly to the
[Camel Context](camelcontext.html) instead of initializing through a
constructor.
:::

::: paragraph
The Component DSL is exclusively available as part of the Java DSL.
:::
:::::
::::::

:::::::::::::: sect1
## Using Component DSL {#_using_component_dsl}

::::::::::::: sectionbody
::: paragraph
The following is an example of a Kafka component that is constructed
using the typical constructor initialization:
:::

:::: listingblock
::: content
``` highlight
KafkaComponent kafka = new KafkaComponent();
kafka.setBrokers("localhost:9090");

camelContext.addComponent("kafka", kafka);
```
:::
::::

::: paragraph
The same Java statement can be rewritten in the following more readable
way using the new `ComponentsBuilderFactory` that allows the access to
all component DSLs in Camel:
:::

:::: listingblock
::: content
``` highlight
ComponentsBuilderFactory.kafka()
                        .brokers("{{kafka.host}}:{{kafka.port}}")
                        .register(camelContext, "kafka");
```
:::
::::

::: paragraph
In order to explain it better, we can break down the above Java
statement into 3 parts:
:::

::: {.olist .arabic}
1.  `ComponentsBuilderFactory.kafka()`: This will initialize the DSL for
    `camel-kafka` component.

2.  `.brokers("{{kafka.host}}:{{kafka.port}}")`: This is the equivalent
    setter of `kafka.setBrokers("localhost:9090")` using DSL fluent
    builder.

3.  `.register(camelContext, "kafka")`: Here we register directly the
    component under name `kafka` into Camel context, of course you can
    use any component name you like, just like `addComponent` API.
:::

:::: sect2
### Type Safety {#_type_safety}

::: paragraph
Similar to the [Endpoint DSL](Endpoint-dsl.html), uses the meta-model,
which is extracted from the source and written in various JSON files, to
generate a fluent DSL for each component. This fluent DSL provides type
safety for parameters.
:::
::::
:::::::::::::
::::::::::::::

::::::::: sect1
## Dependency {#_dependency}

:::::::: sectionbody
::: paragraph
In order to use this feature, Maven users will need to add the following
dependency to their `pom.xml` for this component:
:::

::::: listingblock
::: title
pom.xml
:::

::: content
``` highlight
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-componentdsl</artifactId>
    <version>x.x.x</version>
</dependency>
```
:::
:::::

::: paragraph
And then use it just like how we demonstrated it earlier in this page.
:::
::::::::
:::::::::
::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
