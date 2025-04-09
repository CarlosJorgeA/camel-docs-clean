::: {#header}
# Apache Camel 4.x Upgrade Guide
:::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
::::: {#preamble}
:::: sectionbody
::: paragraph
This document is for helping you upgrade your Apache Camel application
from Camel 4.x to 4.y. For example, if you are upgrading Camel 4.0 to
4.2, then you should follow the guides from both 4.0 to 4.1 and 4.1 to
4.2.
:::
::::
:::::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Upgrading Camel 4.8 to 4.9 {#_upgrading_camel_4_8_to_4_9}

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::::: sect2
### camel-api {#_camel_api}

::: paragraph
Added `setLazyBeanStrategy`/`getLazyBeanStrategy` methods to
`org.apache.camel.spi.CamelBeanPostProcessor`.
:::

::: paragraph
Renamed `Exchange.ACTIVE_SPAN` to `Exchange.OTEL_ACTIVE_SPAN`. Renamed
`ExchangePropertyKey.ACTIVE_SPAN` to
`ExchangePropertyKey.OTEL_ACTIVE_SPAN`.
:::
:::::

:::::: sect2
### camel-management {#_camel_management}

::: paragraph
The `queueSize` attribute on endpoints which are
`ManagedBrowseableEndpoint` is changed from returning a `Long` value to
an `Integer` value.
:::

:::: sect3
#### Using Route Templates {#_using_route_templates}

::: paragraph
Camel will now ensure the created routes via route templates always use
unique *nodePrefixId* to ensure there are no duplicate id clashes when
the template is used for creating multiple routes.
:::
::::
::::::

:::: sect2
### camel-xml-io {#_camel_xml_io}

::: paragraph
The XML dumper no longer includes attributes which are using default
values.
:::
::::

:::: sect2
### camel-jackson {#_camel_jackson}

::: paragraph
The option `useWriter` on JSON data format and camel-jackson, has been
replaced with `combineUnicodeSurrogates` as it is intended for 4-bytes
characters such as Japanese.
:::
::::

::::: sect2
### camel-jms {#_camel_jms}

::: paragraph
The camel-jms component has changed its default `HeaderFilterStrategy`
to also remove any `Camel…​` headers, to act similar to other Camel
components. If the old classic behavior is needed then you can configure
the `JmsComponent` to use
`org.apache.camel.component.jms.ClassicJmsHeaderFilterStrategy` as
header filter strategy which is the old implementation.
:::

::: paragraph
Added `int limit` as parameter to the `browse` method in
`org.apache.camel.component.jms.QueueBrowseStrategy`.
:::
:::::

:::: sect2
### camel-opentelemetry {#_camel_opentelemetry}

::: paragraph
The `camel-opentelemetry` component has had significant bug fixes to
handle span activation/deactivations better when Camel route messages
synchronously and asynchronously. This component should also work better
on Spring Boot.
:::
::::

:::: sect2
### camel-pubnub {#_camel_pubnub}

::: paragraph
Upgraded the pubnub client to 10.x, which removed the `cipherKey`
option.
:::
::::

:::: sect2
### camel-smooks {#_camel_smooks}

::: paragraph
Upgraded Smooks from version 2.0.0-RC4 to version 2.0.1 which has [API
breaking
changes](https://www.smooks.org/documentation/#migrating_from_smooks_1_7_to_2_0)
in Smooks.
:::
::::

::::::::: sect2
### camel-hashicorp-vault properties function {#_camel_hashicorp_vault_properties_function}

::: paragraph
The syntax for retrieving a single field of a secret has been changed.
:::

::: paragraph
From this:
:::

::: paragraph
`{{hashicorp:secret:database/username}}`
:::

::: paragraph
to this:
:::

::: paragraph
`{{hashicorp:secret:database#username}}`
:::

::: paragraph
You could find more details on CAMEL-21179 issue
:::
:::::::::

::::::::: sect2
### camel-aws-secrets-manager properties function {#_camel_aws_secrets_manager_properties_function}

::: paragraph
The syntax for retrieving a single field of a secret has been changed.
:::

::: paragraph
From this:
:::

::: paragraph
`{{aws:database/username}}`
:::

::: paragraph
to this:
:::

::: paragraph
`{{aws:database#username}}`
:::

::: paragraph
You could find more details on CAMEL-21179 issue
:::
:::::::::

::::::::: sect2
### camel-google-secret-manager properties function {#_camel_google_secret_manager_properties_function}

::: paragraph
The syntax for retrieving a single field of a secret has been changed.
:::

::: paragraph
From this:
:::

::: paragraph
`{{gcp:database/username}}`
:::

::: paragraph
to this:
:::

::: paragraph
`{{gcp:database#username}}`
:::

::: paragraph
You could find more details on CAMEL-21179 issue
:::
:::::::::

::::::::: sect2
### camel-azure-key-vault properties function {#_camel_azure_key_vault_properties_function}

::: paragraph
The syntax for retrieving a single field of a secret has been changed.
:::

::: paragraph
From this:
:::

::: paragraph
`{{azure:database/username}}`
:::

::: paragraph
to this:
:::

::: paragraph
`{{azure:database#username}}`
:::

::: paragraph
You could find more details on CAMEL-21179 issue
:::
:::::::::

::::: sect2
### camel-aws {#_camel_aws}

::: paragraph
The `camel-aws2-s3` when using `headBucket` operation will now store the
result in a header named `CamelAwsS3BucketExists` whether the bucket
exists or not.
:::

::: paragraph
Previously an
`software.amazon.awssdk.services.s3.model.NoSuchBucketException` was
thrown. However, this would require using error handling to just check
whether the bucket existed. Returning a boolean makes it easier in Camel
routes to decide what to do. You may also set the option
`ignoreBody=true` to not change the message body.
:::
:::::

::::: sect2
### camel-test {#_camel_test}

::: paragraph
The `CamelTestSupport` class was modified so that the JUnit 5 extension
code that was part of the class itself, was moved to a separate class.
This is part of the work being done on
[CAMEL-20837](https://issues.apache.org/jira/browse/CAMEL-20837) to
modernize the base test code.
:::

::: paragraph
In many cases, code should work with no changes provided they are not
using any of the API methods that were deprecated in Camel 4.7.0.
Additionally, starting with Camel 4.9, we strongly recommend users to
avoid relying on single-instance contexts (created via
`@TestInstance(TestInstance.Lifecycle.PER_CLASS)`), as this is
considered a deprecated functionality that will be removed in the
future. The logs will print a warning message if this behavior is
detected.
:::
:::::

:::: sect2
### camel-debezium {#_camel_debezium}

::: paragraph
To avoid split package that can be a problem in environments like OSGI,
each camel-debezium module has its own subpackage corresponding to the
database type. So for example, all the classes of the module
`camel-debezium-postgres` have been moved to a dedicated package which
is `org.apache.camel.component.debezium.postgres` instead of having
everything under the root package `org.apache.camel.component.debezium`.
:::
::::

::::: sect2
### Deprecated components {#_deprecated_components}

::: paragraph
The following components were marked as deprecated:
:::

::: ulist
- `camel-etcd3`
:::
:::::

:::::: sect2
### Removed deprecated components {#_removed_deprecated_components}

::: paragraph
The following experimental DSL has been removed:
:::

::: ulist
- `camel-groovy-dsl`

- `camel-js-dsl`

- `camel-jsh-dsl`
:::

::: paragraph
The Camel team is only focusing on Java, XML and YAML DSL.
:::
::::::

::::::: sect2
### Removed API {#_removed_api}

:::::: sect3
#### Kotlin DSL {#_kotlin_dsl}

::: paragraph
The Kotlin DSL, which was deprecated in Camel 4.7.0, has now been
removed. The routes must be migrated to another DSL such as Java, YAML
or XML.
:::

::: paragraph
The following modules have been removed:
:::

::: ulist
- `camel-kotlin-dsl`

- `camel-kotlin-api`
:::
::::::
:::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
