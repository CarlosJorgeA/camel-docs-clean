::: {#header}
# Apache Camel 3.x Upgrade Guide
:::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
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

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Upgrading Camel 3.16 to 3.17 {#_upgrading_camel_3_16_to_3_17}

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::::::::::::::::::::::::::::::: sect2
### camel-core {#_camel_core}

:::::::::::::::::::::::::::::: sect3
#### Stream Caching {#_stream_caching}

::: paragraph
We have enabled [Stream Caching](stream-caching.html) by default on
`CamelContext`. The reason is that Camel users may often hit this
problem without knowing what is causing this, and blaming it on Apache
Camel.
:::

::: paragraph
This means that Camel would automatically cache message bodies that are
based on `java.util.InputStream` such as often seen with HTTP
components. This makes it safe to log the message body, and elsewhere.
:::

::: paragraph
The cost is a tiny overhead in the routing engine as Camel will
automatic type convert to `StreamCache` if needed. Users can turn this
off:
:::

:::: listingblock
::: content
``` highlight
CamelContext context = ...
context.setStreamCaching(false);
```
:::
::::

::: paragraph
Or via Camel Main / Camel K / Quarkus:
:::

:::: listingblock
::: content
``` highlight
camel.main.streamCachingEnabled=false
```
:::
::::

::: paragraph
Or in Spring Boot
:::

:::: listingblock
::: content
``` highlight
camel.springboot.streamCachingEnabled=false
```
:::
::::

::: paragraph
And in legacy Spring XML or OSGi Blueprint:
:::

:::: listingblock
::: content
``` highlight
<streamCaching enabled="true" .../>
```
:::
::::

::: paragraph
We also changed the default settings for stream caching to not spool to
disk, meaning that the cache is an in-memory on cache.
:::

::: paragraph
To enable spool to disk, you can configure this as follows:
:::

:::: listingblock
::: content
``` highlight
CamelContext context = ...
context.getStreamCachingStrategy().setSpoolEnabled(true);
```
:::
::::

::: paragraph
Or via Camel Main / Camel K / Quarkus:
:::

:::: listingblock
::: content
``` highlight
camel.main.streamCachingSpoolEnabled=true
```
:::
::::

::: paragraph
Or in Spring Boot
:::

:::: listingblock
::: content
``` highlight
camel.springboot.streamCachingSpoolEnabled=true
```
:::
::::

::: paragraph
And in legacy Spring XML or OSGi Blueprint:
:::

:::: listingblock
::: content
``` highlight
<streamCaching spoolEnabled="true" .../>
```
:::
::::
::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::

:::: sect2
### camel-health {#_camel_health}

::: paragraph
Camel now reports DOWN when Camel is being stopped, during the graceful
shutdown process. This ensures that Camel reports that it's not ready to
accept new traffic.
:::
::::

:::: sect2
### camel-main {#_camel_main}

::: paragraph
Added method `findRouteResourcesFromDirectory` to
`org.apache.camel.main.RoutesCollector`.
:::
::::

:::::::::::::: sect2
### camel-yaml-dsl {#_camel_yaml_dsl}

::: paragraph
Removed deprecated `spec/flows` and `spec/flow` from the kamelet yaml
loader. Must use `spec/template` as key for the kamelet template.
:::

::: paragraph
Removed *endpoint-dsl* notation which was not well known, causing
problems for tools, and yaml-validation against the json-schema
standard.
:::

::: paragraph
For example the following notation:
:::

:::: listingblock
::: content
``` highlight
- from:
    uri: "direct:start"
    steps:
      - to:
          kafka:
            topic: cheese
            brokers: mykafka:1234
```
:::
::::

::: paragraph
Should be change to:
:::

:::: listingblock
::: content
``` highlight
- from:
    uri: "direct:start"
    steps:
      - to:
          uri: "kafka:cheese?brokers=mykafka:1234"
```
:::
::::

::: paragraph
Or
:::

:::: listingblock
::: content
``` highlight
- from:
    uri: "direct:start"
    steps:
      - to:
          uri: "kafka"
          parameters:
            topic: "cheese"
            brokers: "mykafka:1234"
```
:::
::::
::::::::::::::

:::::::: sect2
### camel-spring-xml / camel-blueprint {#_camel_spring_xml_camel_blueprint}

::: paragraph
The error handling has been made universal and exposed generally in the
`camel-core-model` with intent to align error handling across DSLs.
:::

::: paragraph
However, the XML DSL for Spring `<beans>` and OSGi blueprint is legacy,
and they have their own special XML parsing and error handling.
:::

::: paragraph
This means the model classes has been renamed, which only affect Camel
end users whom has defined error handling as `<bean>` in Spring or
Blueprint XML files:
:::

::: ulist
- `org.apache.camel.builder.DeadLetterChannelBuilder` to
  `org.apache.camel.builder.LegacyDeadLetterChannelBuilder`

- `org.apache.camel.builder.DefaultErrorHandlerBuilder` to
  `org.apache.camel.builder.LegacyDefaultErrorHandlerBuilder`

- `org.apache.camel.builder.NoErrorHandlerBuilder` to
  `org.apache.camel.builder.LegacyNoErrorHandlerBuilder`

- `org.apache.camel.spring.spi.TransactionErrorHandlerBuilder` to
  `org.apache.camel.spring.spi.LegacyTransactionErrorHandlerBuilder`
:::

::: paragraph
Users who has been configured error handling using `<errorHandler>` in
Spring or Blueprint XML files should not be affected.
:::
::::::::

::::: sect2
### camel-cdi / camel-cdi-jta {#_camel_cdi_camel_cdi_jta}

::: paragraph
The class `org.apache.camel.cdi.CdiRouteBuilder` has been removed as you
can use `jtaTransactionErrorHandler` builder methods from `camel-core`
instead.
:::

::: paragraph
The class `org.apache.canel.jta.JtaTransactionErrorHandlerBuilder` has
been removed, as the JTA error handler builder can be used with the
`jtaTransactionErrorHandler` from `camel-core-model`.
:::
:::::

:::::::::: sect2
### camel-kafka {#_camel_kafka}

::: paragraph
The option `autoCommitOnStop` was removed from the Camel Kafka
component. When using `autoCommitEnable` (which is enabled by default)
the Kafka consumer will automatically commit on close.
:::

::: paragraph
When the `autoCommitEnable` is turned off, the component issues a call
to the respective commit manager during shutdown.
:::

::: paragraph
Asynchronous, Synchronous or NO-OP commit policies from the former
`autoCommitOnStop` are now determined by automatically by the value of
the `kafkaManualCommitFactory` option:
:::

::: ulist
- NO-OP is the default behavior if no `kafkaManualCommitFactory` is
  provided

- Async can be set using
  `kafkaManualCommitFactory=#class:org.apache.camel.component.kafka.consumer.DefaultKafkaManualAsyncCommitFactory`

- Sync can be set using
  `kafkaManualCommitFactory=#class:org.apache.camel.component.kafka.consumer.DefaultKafkaManualCommitFactory`
:::

::: paragraph
The deprecated constructors for the kafkaManualCommitFactory have been
removed. The constructor should now receive the following parameters:
:::

:::: listingblock
::: content
``` highlight
CamelExchangePayload camelExchangePayload, KafkaRecordPayload kafkaRecordPayload, CommitManager commitManager
```
:::
::::
::::::::::

:::: sect2
### camel-platform-http-vertx {#_camel_platform_http_vertx}

::: paragraph
The configuration for body handler file uploads has changed from `true`
to `false`. The configuration can be enabled via the
`VertxPlatformHttpServerConfiguration` class.
:::
::::

::::: sect2
### camel-opentracing / camel-opentelemetry {#_camel_opentracing_camel_opentelemetry}

::: paragraph
We aligned the MDC keys with OpenTelemetry, so they are changed from:
:::

::: ulist
- `traceId` → `trace_id`

- `spanId` → `span_id`
:::
:::::

::::::: sect2
### camel-atom {#_camel_atom}

::: paragraph
This component was refactored to support the Resume API v2. As such, the
options `filter` and `lastUpdate` where removed.
:::

:::: listingblock
::: content
``` highlight
from("atom:file:src/test/data/feed.atom?splitEntries=true&delay=500")
    .resumable().resumeStrategy(new UpdatedDateFilter(new Date()))
    .to("mock:result");
```
:::
::::

::: paragraph
More complex filters can be implemented by extending the
`UpdatedDateFilter` or by implementing a new `EntryFilter` resume
strategy.
:::
:::::::

:::: sect2
### camel-cdi {#_camel_cdi}

::: paragraph
The support for the Camel XML configuration import, that had been marked
as deprecated in previous releases, was removed.
:::
::::

::::::: sect2
### camel-cxf-blueprint {#_camel_cxf_blueprint}

::: paragraph
When using OSGi Blueprint with CXF endpoints defined in their own
namespace as below, then you must use `depends-on` to refer to the ID of
the `<camelContext>`.
:::

::: paragraph
Notice how we must use `depends-on="VerySimple-context"` in the
`cxf:cxfEndpoint` to refer to the `CamelContext`.
:::

:::: listingblock
::: content
``` highlight
<?xml version="1.0" encoding="utf-8"?>
<osgi:blueprint xmlns:xs="http://www.w3.org/2001/XMLSchema"
                xmlns:camel="http://camel.apache.org/schema/blueprint"
                xmlns:osgi="http://www.osgi.org/xmlns/blueprint/v1.0.0"
                xmlns:cxf="http://camel.apache.org/schema/blueprint/cxf">

    <camel:camelContext id="VerySimple-context">
        <camel:route id="VerySimple-route">
            <camel:from uri="VerySimple"/>
            <camel:to uri="log:proxy.VerySimple"/>
        </camel:route>
    </camel:camelContext>

    <cxf:cxfEndpoint id="VerySimple" depends-on="VerySimple-context" address="http://localhost:8088/VerySimple" serviceName="tns:VerySimple" endpointName="tns:VerySimplePort" wsdlURL="file:deploy/VerySimple.wsdl" xmlns:tns="http://www.talend.org/service/">
        <cxf:properties>
            <osgi:entry key="dataFormat" value="PAYLOAD"/>
        </cxf:properties>
    </cxf:cxfEndpoint>

</osgi:blueprint>
```
:::
::::
:::::::

:::: sect2
### camel-sftp {#_camel_sftp}

::: paragraph
The underlying JSch library has been updated
([CAMEL-17835](https://issues.apache.org/jira/browse/CAMEL-17835)) to a
more secure and actively maintained fork which has removed key types and
algorithms that rely on SHA1. For information on how these can be
restored, consult the [SFTP component](components::sftp-component.html)
documentation.
:::
::::

:::: sect2
### camel-rabbitmq {#_camel_rabbitmq}

::: paragraph
When using `skipQueueDeclare=true` you now must also set
`skipQueueBind=true` to skip both declaring and binding the queue.
:::
::::

::::: sect2
### Deprecated Components {#_deprecated_components}

::: paragraph
The following components that had been marked as deprecated, were
removed in this release:
:::

::: ulist
- camel-atomix

- camel-beanstalk

- camel-beanio

- camel-etcd

- camel-elsql

- camel-ganglia

- camel-nsq

- camel-hystrix

- camel-jing

- camel-leveldb-legacy

- camel-msv

- camel-nagios

- camel-ribbon

- camel-sip

- camel-soroush

- camel-tagsoup

- camel-yammer
:::
:::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
