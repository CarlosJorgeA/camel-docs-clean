::: {#header}
# Apache Camel 3.x Upgrade Guide
:::

:::::::::::::::::::::::::::::::::: {#content}
::::: {#preamble}
:::: sectionbody
::: paragraph
This document is for helping you upgrade your Apache Camel application
from Camel 3.x to 3.y. For example if you are upgrading Camel 3.0 to
3.2, then you should follow the guides from both 3.0 to 3.1 and 3.1 to
3.2.
:::
::::
:::::

:::::::::::::::::::::::::::::: sect1
## Upgrading Camel 3.2 to 3.3 {#_upgrading_camel_3_2_to_3_3}

::::::::::::::::::::::::::::: sectionbody
:::::: sect2
### camel-jackson {#_camel_jackson}

::: paragraph
The dependency on `jackson-module-jaxb-annotations` and the option
`enableJaxbAnnotationModule` have been removed. To enable support for
JAXB annotations, users have to:
:::

::: ulist
- explicit add `jackson-module-jaxb-annotations`

- configure the `JacksonDataFormat` and/or the `JacksonTypeConverters`
  module classes, example:

  ::::: listingblock
  ::: title
  JacksonDataFormat
  :::

  ::: content
  ``` highlight
  JacksonDataFormat formatPojo = new JacksonDataFormat(TestJAXBPojo.class);
  formatPojo.setModuleClassNames(JaxbAnnotationModule.class.getName());
  ```
  :::
  :::::

  ::::: listingblock
  ::: title
  JacksonTypeConverters
  :::

  ::: content
  ``` highlight
  context.getGlobalOptions().put(JacksonConstants.ENABLE_TYPE_CONVERTER, "true");
  context.getGlobalOptions().put(JacksonConstants.TYPE_CONVERTER_TO_POJO, "true");
  context.getGlobalOptions().put(JacksonConstants.TYPE_CONVERTER_MODULE_CLASS_NAMES, JaxbAnnotationModule.class.getName());
  ```
  :::
  :::::
:::

::: paragraph
this change affects also the Java DSL and as consequence the
`enableJaxbAnnotationModule` option for the Json DataFormat definition
has been removed.
:::
::::::

::::: sect2
### Template components {#_template_components}

::: paragraph
The template components which supports using a header to specify a
dynamic resource to use as template, instead of the configured template
on the endpoint is now enabled out of the box. If you want to use this
feature then you must turn on `allowTemplateFromHeader=true` on either
the endpoint or component level.
:::

::: paragraph
This applies to the following templating components: camel-freemarker,
camel-velocity, camel-mvel, camel-mustache, camel-string-template,
camel-chunk, camel-jolt, camel-jslt, camel-robotframework.
:::
:::::

:::: sect2
### SupervisingRouteController {#_supervisingroutecontroller}

::: paragraph
The `SupervisingRouteController` has been refactored and its
configuration has been simplified. And when configuring from spring
boot, then the auto configuration parameters has been changed from
`camel.supervising.controller` into the general `camel.springboot` which
allows to share the same configuration across runtimes so its the same
in Spring Boot, Camel Main, Camel K, Camel Quarkus, etc.
:::
::::

:::: sect2
### Camel Karaf {#_camel_karaf}

::: paragraph
The following components is no longer supported in OSGi and has been
removed from the Camel Karaf features file: camel-undertow,
camel-jgroups, camel-jgroups-raft, camel-infinspan.
:::
::::

:::: sect2
### camel-maven-plugin {#_camel_maven_plugin}

::: paragraph
The `embedded` goal has been removed (was never really in use). Use
`run` goal instead.
:::
::::

::::: sect2
### API changes {#_api_changes}

::: paragraph
The dump model classes in package `org.apache.camel.support.dump` has
been removed as they were not in use by Camel.
:::

::: paragraph
In relation to the dump model classes removal, in camel-karaf the
following commands were removed: `context-info`, `route-info`,
`route-profile` and `route-step`.
:::
:::::

:::::::::::: sect2
### camel-main {#_camel_main}

::: paragraph
The following methods have been
[relocated](https://issues.apache.org/jira/browse/CAMEL-15005) from
`org.apache.camel.main.BaseMainSupport` to
`org.apache.camel.main.MainConfigurationProperties`:
:::

::: ulist
- getConfigurationClasses

- setConfigurationClasses

- addConfigurationClass

- addConfiguration

- getConfigurations

- setConfigurations

- getRouteBuilderClasses

- setRouteBuilderClasses

- getRouteBuilders

- getRoutesBuilders

- setRoutesBuilders

- addRoutesBuilder
:::

::: paragraph
This means that as example, the following snippet:
:::

:::: listingblock
::: content
``` highlight
Main main = new Main();
main.addRoutesBuilder(....)
```
:::
::::

::: paragraph
Should become
:::

:::: listingblock
::: content
``` highlight
Main main = new Main();
main.configure().addRoutesBuilder(....)
```
:::
::::

::: paragraph
See
[https://issues.apache.org/jira/browse/CAMEL-15005](https://issues.apache.org/jira/browse/CAMEL-15005){.bare}
:::
::::::::::::
:::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
