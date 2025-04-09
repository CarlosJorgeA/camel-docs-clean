::: {#header}
# Apache Camel 3.x Upgrade Guide
:::

::::::::::::::::::::::::::::::::::::::::::::: {#content}
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

::::::::::::::::::::::::::::::::::::::::: sect1
## Upgrading Camel 3.18 to 3.19 {#_upgrading_camel_3_18_to_3_19}

:::::::::::::::::::::::::::::::::::::::: sectionbody
::::: sect2
### camel-api {#_camel_api}

::: paragraph
Added `addClassLoader` method to `org.apache.camel.spi.ClassResolver`.
:::

::: paragraph
The default TLS protocol is changed from `TLSv1.2` to `TLSv1.3` in Camel
JSSE support.
:::
:::::

:::: sect2
### camel-management {#_camel_management}

::: paragraph
The context MBean (`ManagedCamelContextMBean`) total counter is changed
to count only once while an *exchange* is being routed through multiple
routes. Previously the counter was a total aggregation of all the routes
the *exchange* was processed. For example if an *exchange* is routed via
A, B and C; then previously the total counter was +3 (+1 for route A, +1
for route B, +1 for route C). This is now corrected so the total is +1
on the context MBean.
:::
::::

:::: sect2
### camel-main {#_camel_main}

::: paragraph
The option `camel.main.eager-classloading` has been removed.
:::
::::

:::: sect2
### camel-ftp {#_camel_ftp}

::: paragraph
The default TLS protocol is changed from `TLSv1.2` to `TLSv1.3`.
:::
::::

:::: sect2
### camel-kafka {#_camel_kafka}

::: paragraph
Updated the documentation to reflect the default TLS protocol in Kafka
Clients running on JDK11+ is `TLSv1.2,TLSv1.3` (prefer v1.3 but can fall
back to v1.2).
:::
::::

:::: sect2
### camel-netty / camel-netty-http {#_camel_netty_camel_netty_http}

::: paragraph
The default TLS protocol is changed from `TLSv1,TLSv1.1,TLSv1.2` to
`TLSv1.2,TLSv1.3`.
:::
::::

::::::::: sect2
### camel-yaml-dsl {#_camel_yaml_dsl}

::: paragraph
Removed using `template` as a custom alias for `routeTemplate` or
`route-template`.
:::

::: paragraph
Removed the `tod` custom alias for `toD` or `to-d`.
:::

::: paragraph
A route template can now also define a `route` which allows specifying
route options that otherwise is not possible when using `from`.
:::

::: paragraph
For example, you can now disable stream-caching, as shown in the snippet
below that are from a Kamelet:
:::

:::: listingblock
::: content
``` highlight
spec:
  template:
    route:
      stream-caching: false
      message-history: true
      from:
        uri: timer:tick
        parameters:
          period: "{{period}}"
        steps:
          - set-body:
              constant: "{{message}}"
          - set-header:
              name: "Content-Type"
              constant: "{{contentType}}"
          - to: kamelet:sink
```
:::
::::
:::::::::

:::: sect2
### camel-salesforce {#_camel_salesforce}

::: paragraph
The URI format for consumer operations has changed. All consumer URIs
now use the `subscribe` operation. E.g.,
`salesforce:subscribe:<topic_name>`,
`salesforce:subscribe:event/<event_name>`,
`salesforce:subscribe:data/ChangeEvents`.
:::
::::

:::: sect2
### camel-consul {#_camel_consul}

::: paragraph
The deprecated options were removed and should be replaced by the
following options:
:::

+-----------------------------------+-----------------------------------+
| Deprecated                        | Replace with                      |
+===================================+===================================+
| `connectTimeoutMillis`            | `connectTimeout`                  |
+-----------------------------------+-----------------------------------+
| `readTimeoutMillis`               | `readTimeout`                     |
+-----------------------------------+-----------------------------------+
| `writeTimeoutMillis`              | `writeTimeout`                    |
+-----------------------------------+-----------------------------------+
::::

:::: sect2
### camel-google-bigquery-sql {#_camel_google_bigquery_sql}

::: paragraph
Parameters in form of `@name` are extracted from the body or message and
their type is preserved and translated into corresponding
`com.google.cloud.bigquery.StandardSQLTypeName`. See the
[documentation](https://cloud.google.com/java/docs/reference/google-cloud-bigquery/latest/com.google.cloud.bigquery.QueryParameterValue)
for more information. (Conversion to StandardSQLTypeName.STRING was used
for each type before)
:::
::::

:::: sect2
### camel-telegram {#_camel_telegram}

::: paragraph
The component was migrated from the Async HTTP Client to the builtin
HTTP client from Java 11 and newer. As such, \* the parameter
`clientConfig`, that received an `AsyncHTTPClientConfiguration` instance
was removed \* the parameter `client`, that received an
`AsyncHttpClient` instance, was modified to receive a HTTPClient
instance.
:::
::::

:::: sect2
### xtokenize language {#_xtokenize_language}

::: paragraph
The xtokenize language has moved from `camel-xml-jaxp` to `camel-stax`
JAR because a stax parser was needed anyway to use the language.
:::
::::

:::: sect2
### camel-karaf {#_camel_karaf}

::: paragraph
Upgraded from Karaf 4.3.x to Karaf 4.4.x, which requires JDK11+.
:::
::::

:::: sect2
### camel-http {#_camel_http}

::: paragraph
Added `followRedirects` option with default value `false` on component
and endpoint level. The introduction of this option changes the default
redirect behaviour for producers from following all `GET` and `HEAD`
redirects, to not following any redirects.
:::
::::

::::: sect2
### Deprecated Components {#_deprecated_components}

::: paragraph
The following components that had been marked as deprecated, were
removed in this release:
:::

::: ulist
- camel-ahc

- camel-ahc-ws
:::
:::::
::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
