::: {#header}
# What are the dependencies?
:::

:::::::::::: {#content}
::::: sect1
## Java support {#_java_support}

:::: sectionbody
::: ulist
- Camel 2 requires JDK 8

- Camel 3 requires JDK 8 and supports JDK 11

- Camel 3.15.0+ requires JDK 11

- Camel 3.17.0+ requires JDK 11 and supports JDK 17

- Camel 4.0.0+ requires JDK 17
:::
::::
:::::

::::: sect1
## Camel JAR Dependencies {#_camel_jar_dependencies}

:::: sectionbody
::: paragraph
Camel core itself is lightweight, and only requires the `slf4j-api`
logging API jar.
:::
::::
:::::

::::: sect1
## Components {#_components}

:::: sectionbody
::: paragraph
All the [Components](components::index.html) have a range of 3rd party
jars they depend on. They are listed in the maven pom files which files
they require.
:::
::::
:::::
::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
