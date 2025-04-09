::: {#header}
# Startup Condition
:::

::::::::::::::::: {#content}
::: paragraph
**Available as of Camel 4.9**
:::

::: paragraph
You can use `StartupCondition` to let Camel perform some checks on
startup, before continuing. For example to check if a specific ENV
exists, or wait for a specific file to be created etc.
:::

::: paragraph
Camel provides a few out of the box
:::

::: ulist
- `EnvStartupCondition` - To check for a specific OS environment exists

- `FileStartupCondition` - To check for a specific file exists
:::

::: paragraph
You can implement custom conditions by implementing
`org.apache.camel.spi.StartupCondition`, and add these to the
`StartupConditionStrategy` or `Registry` such as:
:::

:::: listingblock
::: content
``` highlight
StartupConditionStrategy scs = context.getCamelContextExtension().getContextPlugin(StartupConditionStrategy.class);
scs.addStartupCondition(new MyCondition());
```
:::
::::

::: paragraph
If you use Camel Main, Spring Boot, or Quarkus, you can configure
startup conditions with configuration in `application.properties`.
:::

::: paragraph
For example to check for a specific OS environment variable exists:
:::

:::: listingblock
::: content
``` highlight
camel.startupcondition.enabled = true
camel.startupcondition.environmentVariableExists = FOO_BAR
```
:::
::::

::: paragraph
Or to use a custom condition by its class name:
:::

:::: listingblock
::: content
``` highlight
camel.startupcondition.enabled = true
camel.startupcondition.customClassNames = com.foo.MyStartupCondition
```
:::
::::
:::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
