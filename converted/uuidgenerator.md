::: {#header}
# UUID Generator
:::

:::::::::::::::::::: {#content}
:::::::: {#preamble}
::::::: sectionbody
::: paragraph
Camel supports third-party UUID generator(s).
:::

::: paragraph
A useful scenario is to use a simple counter for testing purpose. With
this, it is easier to correlate the exchanges in the log/debugger.
:::

::: paragraph
Camel uses UUIDs in the exchange and message ids, and other unique ids
it uses.
:::

::: paragraph
You only have to implement `org.apache.camel.spi.UuidGenerator` and tell
Camel, that it should use your custom implementation:
:::
:::::::
::::::::

::::::::: sect1
## Configuring UuidGenerator {#_configuring_uuidgenerator}

:::::::: sectionbody
:::: listingblock
::: content
``` highlight
getContext().setUuidGenerator(new MyCustomUuidGenerator());
```
:::
::::

::: paragraph
Camel will configure this UUID generator by doing a lookup in the Spring
bean registry to find the bean of the type
`org.apache.camel.spi.UuidGenerator`.
:::

:::: listingblock
::: content
``` highlight
<bean id="simpleUuid" class="org.apache.camel.support.SimpleUuidGenerator" />

<camelContext id="camel" xmlns="http://camel.apache.org/schema/spring">
  <route>
    <from uri="direct:start" />
    <to uri="mock:result" />
  </route>
</camelContext>
```
:::
::::
::::::::
:::::::::

:::::: sect1
## Standard UUID Generators {#_standard_uuid_generators}

::::: sectionbody
::: paragraph
Camel comes with the following implementations out of the box:
:::

::: ulist
- `org.apache.camel.support.ClassicUuidGenerator`: this is the classic
  Camel 2.x generator

- `org.apache.camel.support.DefaultUuidGenerator`: default generator (32
  chars) optimized for Camel usage

- `org.apache.camel.support.ShortUuidGenerator`: Is 50% the size of the
  default (16 chars) optimized for Camel usage

- `org.apache.camel.support.SimpleUuidGenerator`: This implementation
  uses internally a `java.util.concurrent.atomic.AtomicLong` and
  increases the ID for every call by one. Starting with 1 as the first
  id.
:::
:::::
::::::
::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
