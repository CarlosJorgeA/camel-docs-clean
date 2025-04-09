::: {#header}
# Context Reload
:::

:::::::::::::::::::::::::::::: {#content}
::::::::: {#preamble}
:::::::: sectionbody
::: paragraph
The context reload functionality in Camel is capable of reloading all
existing routes and property placeholders upon an external triggered
event.
:::

::: paragraph
For example, if you are using [AWS
Secrets](components::aws-secrets-manager-component.html), then enabling
context-reload would then reload Camel routes upon a secret is updated
in AWS.
:::

::: paragraph
The context reload is limited to refresh the following on reload:
:::

::: ulist
- [property placeholders](using-propertyplaceholder.html)

- all existing [routes](routes.html) (no changes to structure of routes;
  see [route-reload.html](route-reload.html)\])
:::

::: paragraph
General services in [CamelContext](camelcontext.html) and java beans or
Camel [processor.html](processor.html) is not updated.
:::
::::::::
:::::::::

::::::::::::: sect1
## Using context reloading {#_using_context_reloading}

:::::::::::: sectionbody
::: paragraph
The context reloading can be configured in Java or with Spring Boot,
Quarkus in the following way:
:::

:::: listingblock
::: content
``` highlight
CamelContext context = ...

ContextReloadStrategy reload = new DefaultContextReloadStrategy();
context.addService(reload);
```
:::
::::

::: paragraph
And with Camel Quarkus / Camel Main you can configure this in
`application.properties:`
:::

:::: listingblock
::: content
``` highlight
# turn on context reloading
camel.main.context-reload-enabled = true
```
:::
::::

::: paragraph
And in Spring Boot:
:::

:::: listingblock
::: content
``` highlight
# turn on context reloading
camel.springboot.context-reload-enabled = true
```
:::
::::
::::::::::::
:::::::::::::

:::::::: sect1
## Triggering context reloading {#_triggering_context_reloading}

::::::: sectionbody
::: paragraph
Any custom code can trigger context reloading. This is done by ensuring
the context reload is enabled (see the note above), and then from Java
you can get hold of `ContextReloadStrategy` as follows:
:::

:::: listingblock
::: content
``` highlight
ContextReloadStrategy reload = context.hasService(ContextReloadStrategy.class);
if (reload != null) {
    // trigger reload
    reload.onReload(this);
}
```
:::
::::

::: paragraph
The method `onReload` will then reload all the [property
placeholders](using-propertyplaceholder.html) and then afterward reload
all existing [routes](routes.html).
:::
:::::::
::::::::

::::: sect1
## See Also {#_see_also}

:::: sectionbody
::: paragraph
See related [route-reload.html](route-reload.html).
:::
::::
:::::
::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
