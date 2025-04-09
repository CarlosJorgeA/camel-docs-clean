::: {#header}
# Apache Camel 3.x Upgrade Guide
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
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

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Upgrading Camel 3.5 to 3.6 {#_upgrading_camel_3_5_to_3_6}

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
:::::: sect2
### UUID Generator {#_uuid_generator}

::: paragraph
The default UUID Generator in Camel has switched to a faster algorithm
based on UUID that were used by Camel Quarkus and Camel K. This
generator does not use the hostname as part of the ID generation and
avoids any network IO calls during initialization.
:::

::: paragraph
The old generator has been renamed and moved into a new package at
`org.apache.camel.support.ClassicUuidGenerator`. The new default
generator is now also located in camel-support as
`org.apache.camel.support.DefaultUuidGenerator` where all the out of the
box generators reside.
:::

::: paragraph
Users that want to use the old UUID generator can configure Camel to use
`org.apache.camel.support.ClassicUuidGenerator` as the generator.
:::
::::::

::::: sect2
### Simple language {#_simple_language}

::: paragraph
The simple language have been optimized to handle numeric and boolean
values more naturally. We have also optimized for unneeded type
conversions in predicates such as comparing left and right hand side in
operators with their given type as-is, or the least amount of conversion
when possible.
:::

::: paragraph
The `contains` and `not contains` operator now handles numerics
naturally. For example `${header.numbers} contains '123,456'` would
return `false` if the numbers header has the value `-123`, as the
operator now compares numerically. Previously it would compare with
String based values.
:::
:::::

:::::: sect2
### Bean component {#_bean_component}

::: paragraph
We have now implemented bean scope (singleton, request, and prototype)
for all kinds of using the bean component, whether it's the bean
endpoint, bean language (method call), bean EIP, or the bean function in
the simple language (eg `${bean:foo}`).
:::

::: paragraph
The default scope is now singleton. Previously the bean function in
simple language would be prototype scope (always lookup the bean on each
use). With singleton scope we improve the performance by avoiding the
repetitive lookup of the same bean on each usage.
:::

::: paragraph
You can use the old behaviour by setting the scope to prototype.
:::
::::::

:::: sect2
### API components upgrade {#_api_components_upgrade}

::: paragraph
The `camel-braintree`, `camel-twilio` and `camel-zendesk` has updated to
newer versions and regenerated their API signatures in the Camel
components which changed a few existing API methods and adding new API
methods as well.
:::
::::

:::::: sect2
### API components overhauled {#_api_components_overhauled}

::: paragraph
The following API component has been overhauled, by having a new Java
source parser to inspect API and fully generate Camel endpoint
configuration and documentation gathered from javadoc.
:::

::: paragraph
The components affected are:
:::

::: ulist
- camel-as2

- camel-box

- camel-braintree

- camel-fhir

- camel-google-calendar

- camel-google-drive

- camel-google-email

- camel-google-sheets

- camel-olingo2

- camel-olingo4

- camel-twilio

- camel-zendesk
:::
::::::

:::: sect2
### Languages {#_languages}

::: paragraph
All the out of the box supported languages (simple, bean, groovy,
jsonpath, xpath etc.) have been optmized to be *singleton* which
improves performance for concurrent processing that uses those languages
that would be resolved on each use. Now the languages are resolved once
during startup or first usage.
:::
::::

:::: sect2
### Expressions {#_expressions}

::: paragraph
Expressions built via `ExpressionBuilder` and similar now often require
to be initialized by calling `init` before use. This is part of an
effort to optimize Camel to eager initialize its expression and
languages. This only impacts Camel end users whom use the
`ExpressionBuilder` directly. Normal usage of Camel should not be
impacted by this change.
:::
::::

:::::::::::::::: sect2
### Customizers {#_customizers}

::: paragraph
Customizers, which are objects used to configure some of the Camel
services such as component, language and data formats, that were
previously limited to Camel Spring Boot, are now consistently used
across runtimes. To make that possible, the interfaces have been changed
and they do not use generics anymore.
:::

::: paragraph
Impacted interfaces:
:::

::: ulist
- org.apache.camel.spi.ComponentCustomizer

- org.apache.camel.spi.LanguageCustomizer

- org.apache.camel.spi.DataFormatCustomizer
:::

::: paragraph
As example the signature of the `ComponentCustomizer` interface in Camel
3.5 is:
:::

:::: listingblock
::: content
``` highlight
@FunctionalInterface
public interface ComponentCustomizer<T extends Component> {
    /**
     * Customize the specified {@link Component}.
     *
     * @param component the component to customize
     */
    void customize(T component);
}
```
:::
::::

::: paragraph
And below the Camel 3.6 version:
:::

:::: listingblock
::: content
``` highlight
@FunctionalInterface
public interface ComponentCustomizer {
    /**
     * Customize the specified {@link Component}.
     *
     * @param name   the unique name of the component
     * @param target the component to configure
     */
    void configure(String name, Component target);

    /**
     * Checks whether this customizer should be applied to the given {@link Component}.
     *
     * @param  name   the unique name of the component
     * @param  target the component to configure
     * @return        <tt>true</tt> if the customizer should be applied
     */
    default boolean isEnabled(String name, Component target) {
        return true;
    }

    @Override
    default int getOrder() {
        return 0;
    }
}
```
:::
::::

::: paragraph
As the customizers are now taken into account as part of the standard
lifecycle of the `CamelContext`, to programmatically configure a
component, it is enough to register the appropriate customizer in the
`Registry`. For example:
:::

:::: listingblock
::: content
``` highlight
public class Application {

    public static void main(String args) throws Exception {
        Main main = new Main();
        main.addConfigurationClass(MyConfiguration.class);
        main.run(args);
    }

    public static class MyConfiguration {
        @BindToRegistry
        public ComponentCustomizer logCustomizer() {
            // Use a fluent Component Customizer builder to ease the process of creating an customizer.
            return ComponentCustomizer.builder(LogComponent.class)
                    .build(component -> component.setExchangeFormatter(new DefaultExchangeFormatter()));
        }
    }
}
```
:::
::::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | ::: paragraph                     |
| Note                              | As a consequence of this change,  |
| :::                               | the Camel Spring Boot starters    |
|                                   | have been amended to use          |
|                                   | Customizers instead of creating   |
|                                   | instances of components,          |
|                                   | languages or data formats.        |
|                                   | :::                               |
+-----------------------------------+-----------------------------------+
:::
::::::::::::::::

:::: sect2
### Component Verifiers {#_component_verifiers}

::: paragraph
Camel components which provides `ComponentVerifierExtension` should have
`camel-core-catalog` added as dependency at runtime, if the verifier are
in use. You will see an exception about `camel-core-catalog` not found
on classpath otherwise.
:::
::::

:::: sect2
### SendDynamicAware {#_senddynamicaware}

::: paragraph
The API in `org.apache.camel.spi.SendDynamicAware` has changed and any
custom implementations must be updated accordingly. There is a new
abstract `org.apache.camel.support.component.SendDynamicAwareSupport`
class which can be used as base for custom implementations.
:::
::::

:::: sect2
### Stream Caching {#_stream_caching}

::: paragraph
If stream caching is enabled and an exception is thrown while converting
the message payload to `StreamCache` then the error handler can now
handle this exception (e.g. `onException`). The exception is regarded as
non-recoverable and redelivery is not in use.
:::
::::

:::::::::: sect2
### Camel Caffeine {#_camel_caffeine}

::: paragraph
To configure the component to use a pre-configured cache, it is no
longer required to use the now removed `cache` option as the component
performs a look-up in the registry based on the `cacheName` URI param.
:::

::: paragraph
For example, the following code:
:::

:::: listingblock
::: content
``` highlight
.to("caffeine-cache://cache?cache=#myCache&action=PUT&key=1")
```
:::
::::

::: paragraph
Should be replaced by:
:::

:::: listingblock
::: content
``` highlight
.to("caffeine-cache://myCache?action=PUT&key=1")
```
:::
::::
::::::::::

:::: sect2
### Camel Karaf {#_camel_karaf}

::: paragraph
The following feature has been removed due to being no longer compatible
with OSGi: `camel-atmosphere-websocket`.
:::
::::

::::: sect2
### CamelFileDataSource {#_camelfiledatasource}

::: paragraph
The class `CamelFileDataSource` has moved from `camel-http-common`
package `org.apache.camel.http.common` to `camel-attachments` package
`org.apache.camel.attachment`.
:::

::: paragraph
If your code directly depends on this class, you will need to update the
package reference to the new location.
:::
:::::

:::: sect2
### Message History {#_message_history}

::: paragraph
When message history is enabled then there is a slight performance
overhead as the history data is now stored in a
`java.util.concurrent.CopyOnWriteArrayList` due to the need of being
thread safe.
:::
::::

::::::: sect2
### Default limit set on decompressed file size {#_default_limit_set_on_decompressed_file_size}

::: paragraph
From Apache Camel 3.6.0, a default limit is enforced on the maximum size
of a decompressed file, to prevent possible denial of service attacks.
This applies to the camel-zipfile and camel-tarfile data formats. This
can be configured as follows:
:::

:::: listingblock
::: content
``` highlight
ZipFileDataFormat maxDecompressedSizeZip = new ZipFileDataFormat();
maxDecompressedSizeZip.setMaxDecompressedSize(100000000000L);

.unmarshal(maxDecompressedSizeZip)
```
:::
::::

::: paragraph
The default value if not specified corresponds to one gigabyte. An
`IOException` will be thrown if the decompressed size exceeds this
amount. Set to `-1` to disable setting a maximum decompressed size.
:::
:::::::

:::: sect2
### Camel RabbitMQ {#_camel_rabbitmq}

::: paragraph
The `camel-rabbitmq` server component properties have been changed. The
properties prefix `rabbitmq.` was replaced by `CamelRabbitmq`.
:::
::::

:::: sect2
### Camel-Slack {#_camel_slack}

::: paragraph
The options username, iconUrl and iconEmoji have been deprecated and
they will be removed in 3.7.0.
:::
::::

:::: sect2
### Camel-Hipchat {#_camel_hipchat}

::: paragraph
This component has been removed.
:::
::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
