::: {#header}
# Apache Camel 3.x Upgrade Guide
:::

:::::::::::::::::::::::::::::::::::::::::::::: {#content}
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

:::::::::::::::::::::::::::::::::::::::::: sect1
## Upgrading Camel 3.11 to 3.12 {#_upgrading_camel_3_11_to_3_12}

::::::::::::::::::::::::::::::::::::::::: sectionbody
::::::: sect2
### API changes {#_api_changes}

::: paragraph
The methods `getComponentNames` and `getLanguageNames` on `CamelContext`
have been changed to return `Set<String>` instead of `List<String>`.
:::

::: paragraph
Added `getExchangePattern` to `Endpoint` which is a method that already
exists on `DefaultEndpoint`. However the method is needed to be exposed
in the API as well.
:::

::: paragraph
The `ThroughputLogger` has changed the return type from `int` to `long`
in the `getReceivedCounter` method.
:::

::: paragraph
Removed the `dataSonnet(Expression)` methods from `RouteBuilder` as they
should not be used; use the methods that take `String` as type.
:::
:::::::

:::::: sect2
### Data Formats {#_data_formats}

::: paragraph
We had to fix all the data-formats options that take a Java classname to
support using the `.class` type in Java vs using a string value in
XML/YAML.
:::

::: paragraph
This means the following options have been renamed:
:::

::: ulist
- `camel-asn1` renamed `clazzName` to `unmarshalType`

- `camel-avro` renamed `collectionTypeName` to `collectionType`, and
  `jsonViewTypeName` to `jsonView`, and `unmarshalTypeName` to
  `unmarshalType`

- `camel-cbor` renamed `collectionTypeName` to `collectionType`, and
  `unmarshalTypeName` to `unmarshalType`

- `camel-jacksonxml` renamed `collectionTypeName` to `collectionType`,
  and `jsonViewTypeName` to `jsonView`, and `unmarshalTypeName` to
  `unmarshalType`

- `camel-json` renamed `collectionTypeName` to `collectionType`, and
  `jsonViewTypeName` to `jsonView`, and `unmarshalTypeName` to
  `unmarshalType`

- `camel-protobuf` renamed `collectionTypeName` to `collectionType`, and
  `jsonViewTypeName` to `jsonView`, and `unmarshalTypeName` to
  `unmarshalType`

- `camel-yaml` renamed `unmarshalTypeName` to `unmarshalType`
:::
::::::

::::: sect2
### Rest DSL {#_rest_dsl}

::: paragraph
The `clientRequestValidation` now includes one more check:
:::

::: ulist
- Parsing error of the message body (JSon, XML or Auto binding mode must
  be enabled). (Returns HTTP Status 400)
:::
:::::

::::: sect2
### camel-aws2-ddb {#_camel_aws2_ddb}

::: paragraph
The `iteratorType` option has been dropped in favour of the
`streamIteratorType` option. Possible values are `FROM_LATEST` and
`FROM_START`.
:::

::: paragraph
The `sequencenumberprovider` functionality is no longer supported.
:::
:::::

:::: sect2
### camel-catalog {#_camel_catalog}

::: paragraph
Remove the APIs to return the website documentation in ascii doc and
html format, it is the methods with naming pattern `…​AsciiDoc` and
`…​HtmlDoc`.
:::
::::

:::: sect2
### camel-cdi {#_camel_cdi}

::: paragraph
The XML DSL with `camel-cdi` has removed the deprecated `<proxy>`
functionality.
:::
::::

:::: sect2
### camel-http-common {#_camel_http_common}

::: paragraph
The method
`org.apache.camel.http.common.HttpHelper.readResponseBodyFromInputStream`
is renamed to `cacheResponseBodyFromInputStream`, because it must cache
the response body as it is used with the `PollingConsumer` in
`camel-http`, that otherwise may fail if the original `InputStream` is
closed by the underlying HTTP client.
:::
::::

:::: sect2
### camel-jms {#_camel_jms}

::: paragraph
The `camel-jms` component now better support Apache Artemis in streaming
mode for large messages support, when Artemis is using a pooled
connection pool (previously pooled was not supported). Now the option
`artemisStreamingEnabled` must explicit be set to `true` to enable
support for Artemis streaming mode. Previously Camel tried to
auto-detect this.
:::
::::

::::: sect2
### camel-google-sheets {#_camel_google_sheets}

::: paragraph
The `google-sheets-stream` component has changed the syntax, from:
`google-sheets-stream:apiName` to: `google-sheets-stream:spreadsheetId`
:::

::: paragraph
The old `apiName` option was not in use, and therefore it has been
replaced with `spreadsheetId` which used to be query parameter.
:::
:::::

:::: sect2
### camel-kamelet / route templates {#_camel_kamelet_route_templates}

::: paragraph
Kamelet parameters that are named such as `host`, `port` are now always
used with their configured value. Before the value in use may have been
from an ENV variable with the same name.
:::
::::

:::: sect2
### camel-kafka {#_camel_kafka}

::: paragraph
The `camel-kafka` component was refactored to avoid blocking API calls
in the Kafka consumer. As part of the refactoring the configuration
`consumerStreams` was removed. In previous versions this setting
configured the size thread pool used by the component to create Kafka
consumers. As of 3.12, the size of the thread pool is set to be the same
as the number of concurrent consumers (set by `consumersCount`).
:::
::::

:::: sect2
### camel-spark {#_camel_spark}

::: paragraph
The `camel-spark` component has been upgraded from Spark 2.x to 3.x.
:::
::::

:::: sect2
### camel-influxdb {#_camel_influxdb}

::: paragraph
The `camel-influxdb` won't autocreate the database if not present
anymore. With CAMEL-16892 we introduced the checkDatabaseExistence and
autoCreateDatabase options for this purpose. Both of the options are
false by default. So, you'll need to set both to true if you want to
have the older releases\' behavior, or create the database yourself
before using the camel component.
:::
::::

:::: sect2
### camel-huawei {#_camel_huawei}

::: paragraph
All Huawei Cloud components use AK/SK as security keys to authenticate
against the hauwei cloud backend services. Initally AK was mapped to
authenticationKey in the endpoint class. To be in-line with Huawei
Cloud's naming conventions, we have renamed this option from
`authenticationKey` to `accessKey` for all components. This is a
breaking change for users who are already using huawei cloud components
until version 3.11.x.
:::
::::

:::: sect2
### camel-spring-cloud-\*-starter {#_camel_spring_cloud_starter}

::: paragraph
The deprecated camel-spring-boot cloud-starter components
camel-spring-cloud-starter, camel-spring-cloud-consul-starter,
camel-spring-cloud-netflix-starter and
camel-spring-cloud-zookeeper-starter have been removed.
:::
::::
:::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
