::: {#header}
# Apache Camel 3.x Upgrade Guide
:::

:::::::::::::::::::::::::::::::::::::: {#content}
::::: {#preamble}
:::: sectionbody
::: paragraph
This document is for helping you upgrade your Apache Camel application
from Camel 3.x to 3.y. For example, if you are upgrading Camel 3.0 to
3.2, then you should follow the guides from both 3.0 to 3.1 and 3.1 to
3.2.
:::
::::
:::::

:::::::::::::::::::::::::::::::::: sect1
## Upgrading Camel 3.17 to 3.18 {#_upgrading_camel_3_17_to_3_18}

::::::::::::::::::::::::::::::::: sectionbody
:::::: sect2
### camel-core {#_camel_core}

::: paragraph
Camel will now mask all known secret values when logging endpoint URIs
to avoid leaking sensitive details such as from stack traces. Previously
only a subset of known *secret* keys was masked.
:::

::: paragraph
The type converter from `InputStream` to `byte[]` will now close the
input stream after the conversion.
:::

::: paragraph
The `TimeUtils.printDuration` method outputs now in a more compact
format (especially for long durations). Before `4d12h57m49s` and now
`4d12h`. Use `precise=true` to include all details.
:::
::::::

:::: sect2
### camel-console {#_camel_console}

::: paragraph
The `AbstractDevConsole` has changed the method `doCall` into two
separate methods `doCallText` and `doCallJson` to better separate output
between text and json based.
:::
::::

:::::::::::::: sect2
### camel-cxf {#_camel_cxf}

::: paragraph
The `camel-cxf` JAR has been split up into SOAP vs. REST and Spring and
non-Spring JARs.
:::

::: paragraph
Users should therefore choose `camel-cxf` to migrate among the following
JARs:
:::

::: ulist
- `camel-cxf-soap`

- `camel-cxf-spring-soap`

- `camel-cxf-rest`

- `camel-cxf-spring-rest`

- `camel-cxf-transport`

- `camel-cxf-spring-transport`
:::

::: paragraph
For example, if you were using CXF for SOAP and with Spring XML, then
you would need to migrate from using `camel-cxf` to
`camel-cxf-spring-soap` and `camel-cxf-spring-transport`.
:::

::: paragraph
When using Spring Boot, then you need to migrate from
`camel-cxf-starter` to use SOAP or REST:
:::

::: ulist
- `camel-cxf-soap-starter`

- `camel-cxf-rest-starter`
:::

::: paragraph
The `camel-cxf` XML XSD schemas have also changed namespaces:
:::

+-----------------------------------+-----------------------------------+
| Old Namespace                     | New Namespace                     |
+===================================+===================================+
| [`http://camel.                   | [`http://camel.apache.org/s       |
| apache.org/schema/cxf`](http://ca | chema/cxf/jaxws`](http://camel.ap |
| mel.apache.org/schema/cxf){.bare} | ache.org/schema/cxf/jaxws){.bare} |
+-----------------------------------+-----------------------------------+
| [`http://c                        | [`http://camel.apache.            |
| amel.apache.org/schema/cxf/camel- | org/schema/cxf/jaxws/camel-cxf.xs |
| cxf.xsd`](http://camel.apache.org | d`](http://camel.apache.org/schem |
| /schema/cxf/camel-cxf.xsd){.bare} | a/cxf/jaxws/camel-cxf.xsd){.bare} |
+-----------------------------------+-----------------------------------+
| [`http://camel.                   | [`http://camel.apache.org/s       |
| apache.org/schema/cxf`](http://ca | chema/cxf/jaxrs`](http://camel.ap |
| mel.apache.org/schema/cxf){.bare} | ache.org/schema/cxf/jaxrs){.bare} |
+-----------------------------------+-----------------------------------+
| [`http://c                        | [`http://camel.apache.            |
| amel.apache.org/schema/cxf/camel- | org/schema/cxf/jaxrs/camel-cxf.xs |
| cxf.xsd`](http://camel.apache.org | d`](http://camel.apache.org/schem |
| /schema/cxf/camel-cxf.xsd){.bare} | a/cxf/jaxrs/camel-cxf.xsd){.bare} |
+-----------------------------------+-----------------------------------+

::: {.admonitionblock .important}
+-----------------------------------+-----------------------------------+
| ::: title                         | The namespace for Apache Camel    |
| Important                         | Karaf (`camel-cxf-blueprint`) has |
| :::                               | **not** changed. This module has  |
|                                   | not been split-up and comes with  |
|                                   | both SOAP and REST combined as    |
|                                   | before.                           |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
And the `camel-cxf` SOAP component is moved to a new `jaxws`
sub-package, i.e. `org.apache.camel.component.cxf` is now
`org.apache.camel.component.cxf.jaws`.
:::

::: paragraph
This means, for example, the `CxfComponent` class is now located in
`org.apache.camel.component.cxf.jaxws`.
:::

::: paragraph
Some classes in the following packages were refactored, and the package
was updated. If you were using one of those packages and, after the
migration, a `ClassNotFoundException` is thrown, an update should be
considered
:::

+-----------------------------------+-----------------------------------+
| Old Package                       | New Package                       |
+===================================+===================================+
| `org.apache.camel.component.cxf`  | `org.a                            |
|                                   | pache.camel.component.cxf.common` |
+-----------------------------------+-----------------------------------+
| `org.apache.                      | `org                              |
| camel.component.cxf.interceptors` | .apache.camel.component.cxf.util` |
+-----------------------------------+-----------------------------------+
| `org.apac                         | `org.                             |
| he.camel.component.cxf.converter` | apache.camel.component.cxf.jaxrs` |
+-----------------------------------+-----------------------------------+
| `org.apache.camel.component.cxf`  | `org.                             |
|                                   | apache.camel.component.cxf.jaxws` |
+-----------------------------------+-----------------------------------+
| `org.a                            | `org.apache.                      |
| pache.camel.component.cxf.spring` | camel.component.cxf.spring.jaxrs` |
+-----------------------------------+-----------------------------------+
| `org.a                            | `org.apache.                      |
| pache.camel.component.cxf.spring` | camel.component.cxf.spring.jaxws` |
+-----------------------------------+-----------------------------------+
| `org.apache.camel.component.cxf`  | `org.apache.                      |
|                                   | camel.component.cxf.spring.jaxws` |
+-----------------------------------+-----------------------------------+
| `org.apache.came                  | `org.apache.came                  |
| l.component.cxf.transport.spring` | l.component.cxf.spring.transport` |
+-----------------------------------+-----------------------------------+
| `org.apache.c                     | `org.apache.came                  |
| amel.component.cxf.common.header` | l.component.cxf.transport.header` |
+-----------------------------------+-----------------------------------+
| `org.apache.ca                    | `org.apache.camel                 |
| mel.component.cxf.common.message` | .component.cxf.transport.message` |
+-----------------------------------+-----------------------------------+
::::::::::::::

:::: sect2
### camel-endpointdsl {#_camel_endpointdsl}

::: paragraph
The options for `lazyStartProducer` and `bridgeErrorHandler` has moved
into the *advanced* group.
:::
::::

:::: sect2
### camel-google-calendar {#_camel_google_calendar}

::: paragraph
The `keyResource` option has been changed to `serviceAccountKey` to
match to Google semantic. Then, if you use the `keyResource` to Google
Calendar component, you should update it to `serviceAccountKey`.
:::
::::

:::: sect2
### camel-google-drive {#_camel_google_drive}

::: paragraph
The `keyResource` option has been changed to `serviceAccountKey` to
match to Google semantic. Then, if you use the `keyResource` to Google
Drive component, you should update it to `serviceAccountKey`.
:::
::::

:::: sect2
### camel-jsonpath {#_camel_jsonpath}

::: paragraph
There is a new option `unpackArray` in **Camel 3.18.3** that unpacks a
single-element Json array, matched by a Jsonpath, into an object. This
option is disabled by default (this behaviour was enabled by default in
previous Camel versions). There is a new expression
`jsonpathUnpack(String text, Class<?> resultType)` that makes use of
this new option.
:::
::::

:::: sect2
### camel-yaml-dsl {#_camel_yaml_dsl}

::: paragraph
The YAML DSL schema files `camel-yaml-dsl.json` and `camelYamlDsl.json`
has been moved from root to `schema` sub folder in the JAR.
:::
::::

:::: sect2
### camel-karaf {#_camel_karaf}

::: paragraph
The `camel-milo` feature has been removed.
:::
::::
:::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
