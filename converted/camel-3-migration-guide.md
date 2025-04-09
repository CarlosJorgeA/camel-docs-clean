::: {#header}
# Apache Camel 2.x to 3.0 Migration Guide
:::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
:::::: {#preamble}
::::: sectionbody
::: paragraph
This document is intended to help you migrate your Apache Camel
applications from version 2.x to 3.0.
:::

::: {.admonitionblock .important}
+-----------------------------------+-----------------------------------+
| ::: title                         | If you are upgrading Camel 3.x to |
| Important                         | 3.y then use the [Camel 3.x       |
| :::                               | Upgrade                           |
|                                   | Gui                               |
|                                   | de](camel-3x-upgrade-guide.html). |
+-----------------------------------+-----------------------------------+
:::
:::::
::::::

:::::::: sect1
## Java versions {#_java_versions}

::::::: sectionbody
::: paragraph
Camel 3 supports Java 11. Support for Java 8 is best effort for early
versions of Camel 3. However, at some time in the 3.x lifeline we will
drop support for Java 8.
:::

::: paragraph
In Java 11, the JAXB modules have been **removed** from the JDK,
therefore, you will need to add them as Maven dependencies (if you use
JAXB such as when using XML DSL or the camel-jaxb component):
:::

:::: listingblock
::: content
``` highlight
<dependency>
    <groupId>javax.xml.bind</groupId>
    <artifactId>jaxb-api</artifactId>
    <version>2.3.1</version>
</dependency>

<dependency>
    <groupId>com.sun.xml.bind</groupId>
    <artifactId>jaxb-core</artifactId>
    <version>2.3.0.1</version>
</dependency>

<dependency>
    <groupId>com.sun.xml.bind</groupId>
    <artifactId>jaxb-impl</artifactId>
    <version>2.3.2</version>
</dependency>
```
:::
::::
:::::::
::::::::

::::::::: sect1
## Modularization of camel-core {#_modularization_of_camel_core}

:::::::: sectionbody
::: paragraph
One of the biggest changes is the modularization of camel-core. In Camel
2.x camel-core was one JAR file, it has now been split up into many JARs
as follows:
:::

::: ulist
- camel-api

- camel-base

- camel-caffeine-lrucache

- camel-cloud

- camel-core

- camel-jaxp

- camel-main

- camel-management-api

- camel-management

- camel-support

- camel-util

- camel-util-json
:::

::: paragraph
Maven users of Apache Camel can keep using the dependency `camel-core`
which has transitive dependencies on all of its modules, except for
`camel-main`, and therefore no migration is needed. However, users who
want to trim the size of the classes on the classpath, can use
fine-grained Maven dependencies on only the modules needed. You may find
how to do that in the examples.
:::

::: paragraph
We have also modularized many of the core components and moved them out
of `camel-core` to individual components:
:::

::: ulist
- camel-attachments

- camel-bean

- camel-browse

- camel-controlbus

- camel-dataformat

- camel-dataset

- camel-direct

- camel-directvm

- camel-file

- camel-language

- camel-log

- camel-mock

- camel-ref

- camel-rest

- camel-saga

- camel-scheduler

- camel-seda

- camel-stub

- camel-timer

- camel-validator

- camel-vm

- camel-xpath

- camel-xslt

- camel-xslt-saxon

- camel-zip-deflater
:::
::::::::
:::::::::

::::::::::: sect1
## Spring Boot starters Maven coordinate change {#_spring_boot_starters_maven_coordinate_change}

:::::::::: sectionbody
::: paragraph
The Maven `groupId` for the Spring Boot starters changed to:
`org.apache.camel.springboot`.
:::

::: paragraph
Instead of:
:::

:::: listingblock
::: content
``` highlight
<dependency>
  <groupId>org.apache.camel</groupId>
  <artifactId>camel-component-starter</artifactId>
</dependency>
```
:::
::::

::: paragraph
Use:
:::

:::: listingblock
::: content
``` highlight
<dependency>
  <groupId>org.apache.camel.springboot</groupId>
  <artifactId>camel-component-starter</artifactId>
</dependency>
```
:::
::::
::::::::::
:::::::::::

:::::: sect1
## Multiple CamelContexts per application not supported {#_multiple_camelcontexts_per_application_not_supported}

::::: sectionbody
::: paragraph
Support for multiple CamelContexts has been removed, and only 1
CamelContext per deployment is supported. The latter was not recommended
anyway and was also not 100% implemented, for example, in `camel-cdi`.
For Camel 3, only 1 `CamelContext` per deployment is recommended and
supported.
:::

::: paragraph
The `context` attribute on the various Camel annotations such as
`@EndpointInject`, `@Produce`, `@Consume` etc. has therefore been
removed.
:::
:::::
::::::

:::::: sect1
## Migrating custom components {#_migrating_custom_components}

::::: sectionbody
::: paragraph
You should depend on `camel-support` and not `camel-core` directly.
:::

::: paragraph
The classes from `org.apache.camel.impl` that are intended to support
Camel developers building custom components have been moved out of
`camel-core` into `camel-support` into the `org.apache.camel.support`
package. For example classes such as `DefaultComponent`,
`DefaultEndpoint` etc. have been moved and migration is necessary.
:::
:::::
::::::

::::: sect1
## Migrating custom languages {#_migrating_custom_languages}

:::: sectionbody
::: paragraph
The `LanguageAnnotation` annotation class has been moved from package
`org.apache.camel.language` to `org.apache.camel.support.language`.
:::
::::
:::::

::::: sect1
## Migrating DefaultShutdownStrategy {#_migrating_defaultshutdownstrategy}

:::: sectionbody
::: paragraph
The `DefaultShutdownStrategy` class has been moved from package
`org.apache.camel.impl` to `org.apache.camel.impl.engine`.
:::
::::
:::::

::::: sect1
## Deprecated APIs and Components {#_deprecated_apis_and_components}

:::: sectionbody
::: paragraph
All deprecated APIs and components from Camel 2.x have been removed in
Camel 3.
:::
::::
:::::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Migrating Camel applications {#_migrating_camel_applications}

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
:::: sect2
### Main class {#_main_class}

::: paragraph
The Camel `Main` class has been moved out of `camel-core` into
`camel-main` so you should add that as dependency if you use Main.
:::
::::

::::::: sect2
### Properties component {#_properties_component}

::: paragraph
The `properties` component has configuring custom prefix and suffix
tokens removed as if in use, they had potential issues with clashing
with simple languages and elsewhere. The default tokens are now
hardcoded and always in use.
:::

::: paragraph
The `properties` component has some advanced options removed:
`propertyPrefix`, `propertySuffix`, and `fallbackToUnaugmented`; these
options was never really useable for end users anyway. The option
`propertiesResolver` has also been removed as you should use
`PropertiesSource` instead.
:::

::: paragraph
The properties component will now use OS environment variables as
preferred value. This means you can set an OS environment variable which
will override any property values that has been set in property files,
JVM system properties etc. You can configure this with the
`environmentVariableMode` option on the properties component.
:::

::: paragraph
The `properties` component no longer support using endpoints, such as
`properties:myKey`. The properties component is now only a property
placeholder service. You can therefore no longer lookup the properties
component via `camelContext.getComponent("properties")`. Instead, you
can use `camelContext.getPropertiesComponent()`, which also returns an
interface of the properties component as
`org.apache.camel.spi.PropertiesComponent`. The implementation is still
named `org.apache.camel.component.properties.PropertiesComponent`,
however, it should rarely be used, as you should favour using the
interface instead.
:::
:::::::

:::::::::: sect2
### Removed components {#_removed_components}

::: paragraph
We have removed all deprecated components from Camel 2.x, including the
old `camel-http`, `camel-hdfs`, `camel-mina`, `camel-mongodb`,
`camel-netty`, `camel-netty-http`, `camel-quartz`, `camel-restlet` and
`camel-rx` components.
:::

::: paragraph
We removed `camel-jibx` component which wasn't working on JDK 8.
:::

::: paragraph
We removed `camel-boon` dataformat which wasn't working on JDK 9 and
later.
:::

::: paragraph
We removed the `camel-linkedin` component and there is no replacement.
:::

::: paragraph
The `camel-zookeeper` has its route policy functionality removed,
instead use `ZooKeeperClusterService` or the `camel-zookeeper-master`
component.
:::

::: paragraph
The `camel-jetty` component no longer supports producer (eg to) which
has been removed, use `camel-http` component instead.
:::

::: paragraph
The `twitter-streaming` component has been removed as it relied on the
deprecated Twitter Streaming API and is no longer functional.
:::
::::::::::

::::::::::::: sect2
### Renamed components {#_renamed_components}

::: paragraph
The `test` component has been renamed to `dataset-test` and moved out of
`camel-core` into `camel-dataset` JAR.
:::

::: paragraph
The `http4` component has been renamed to `http`, and it's corresponding
component package from `org.apache.camel.component.http4` to
`org.apache.camel.component.http`. The supported schemes are now only
`http` and `https`.
:::

::: paragraph
The `hdfs2` component has been renamed to `hdfs`, and it's corresponding
component package from `org.apache.camel.component.hdfs2` to
`org.apache.camel.component.hdfs`. The supported scheme is now `hdfs`.
:::

::: paragraph
The `mina2` component has been renamed to `mina`, and it's corresponding
component package from `org.apache.camel.component.mina2` to
`org.apache.camel.component.mina`. The supported scheme is now `mina`.
:::

::: paragraph
The `mongodb3` component has been renamed to `mongodb`, and it's
corresponding component package from
`org.apache.camel.component.mongodb3` to
`org.apache.camel.component.mongodb`. The supported scheme is now
`mongodb`.
:::

::: paragraph
The `netty4-http` component has been renamed to `netty-http`, and it's
corresponding component package from
`org.apache.camel.component.netty4.http` to
`org.apache.camel.component.netty.http`. The supported scheme is now
`netty-http`.
:::

::: paragraph
The `netty4` component has been renamed to `netty`, and it's
corresponding component package from `org.apache.camel.component.netty4`
to `org.apache.camel.component.netty`. The supported scheme is now
`netty`.
:::

::: paragraph
The `quartz2` component has been renamed to `quartz`, and it's
corresponding component package from
`org.apache.camel.component.quartz2` to
`org.apache.camel.component.quartz`. The supported scheme is now
`quartz`.
:::

::: paragraph
The `rxjava2` component has been renamed to `rxjava`, and it's
corresponding component package from
`org.apache.camel.component.rxjava2` to
`org.apache.camel.component.rxjava`.
:::

::: paragraph
We have also renamed `camel-jetty9` to `camel-jetty`. The supported
scheme is now `jetty`.
:::
:::::::::::::

::::: sect2
### Hystrix EIP {#_hystrix_eip}

::: paragraph
The Hystrix EIP has been generalized as a circuit breaker to allow
plugging in other implementations.
:::

::: paragraph
In the Java DSL you need to migrate from `.hystrix()` to
`.circuitBreaker()`. And in XML DSL `<hystrix>` should be
`<circuitBreaker>`.
:::
:::::

:::: sect2
### Using endpoint options with consumer. prefix {#_using_endpoint_options_with_consumer_prefix}

::: paragraph
Endpoints with `consumer.` prefix such as `consumer.delay=5000` are no
longer supported (deprecated in latest Camel 2.x) and you should just
use the option without the `consumer.` prefix, eg `delay=5000`.
:::
::::

:::::::::::: sect2
### Calling a processor as an endpoint {#_calling_a_processor_as_an_endpoint}

::: paragraph
Calling a processor as an endpoint from a route such as:
:::

:::: listingblock
::: content
``` highlight
from("jms:cheese")
  .to("myProcessor");
```
:::
::::

::: paragraph
Is no longer supported. Instead either use the bean component to call
the processor or use `process`:
:::

:::: listingblock
::: content
``` highlight
from("jms:cheese")
  .to("bean:myProcessor");
```
:::
::::

::: paragraph
Or
:::

:::: listingblock
::: content
``` highlight
from("jms:cheese")
  .process("myProcessor");
```
:::
::::
::::::::::::

::::: sect2
### Tracing {#_tracing}

::: paragraph
A new tracer has been implemented and the old tracer has been removed.
The new tracer logs messages at the `org.apache.camel.Tracing` logger
name which is hardcoded. The format of the output is also updated to
make it better. The tracer can be customized.
:::

::: paragraph
In JMX the `BacklogTracer` is no longer enabled by default, which you
need to enable by setting `backlogTracing=true` on CamelContext. The
backlog tracer and tracer are not the same. The former is used for
capturing a backlog of traced messages which you can poll via JMX
(needed for 3rd party tooling), where as tracer is writing to the log.
Neither of them are enabled by default, and they must be enabled to be
in use.
:::
:::::

::::::::::: sect2
### \<setHeader\> and \<setProperty\> in XML DSL {#_setheader_and_setproperty_in_xml_dsl}

::: paragraph
We have renamed the attribute `headerName` and `propertyName` in the XML
DSL for the `<setHeader>` and `<setProperty`\> EIPs, to be just `name`.
:::

::: paragraph
So migrate
:::

:::: listingblock
::: content
``` highlight
<setHeader headerName="foo"><simple>Hello ${body}</simple></setHeader>
```
:::
::::

::: paragraph
To
:::

:::: listingblock
::: content
``` highlight
<setHeader name="foo"><simple>Hello ${body}</simple></setHeader>
```
:::
::::

::: paragraph
And the same for `<setProperty>`.
:::
:::::::::::

:::::::::: sect2
### \<aggregate\> EIP in XML DSL {#_aggregate_eip_in_xml_dsl}

::: paragraph
The aggregate EIP has renamed the expressions (not the attributes) for
setting correlation size/timeout to avoid a name clash, so migrate:
:::

:::: listingblock
::: content
``` highlight
<completionSize>
  <header>mySize</header>
</completionSize>
```
:::
::::

::: paragraph
To
:::

:::: listingblock
::: content
``` highlight
<completionSizeExpression>
  <header>mySize</header>
</completionSizeExpression>
```
:::
::::

::: paragraph
And the same for `<completionTimeout>`.
:::
::::::::::

::::::::::::: sect2
### \<threads\> \<delay\> \<sample\> \<throttle\> EIP in XML {#_threads_delay_sample_throttle_eip_in_xml}

::: paragraph
These EIPs have been improved to no longer use children for routing,
instead they are collapsed as a single element like other EIPs.
:::

::: paragraph
So before you may have
:::

:::: listingblock
::: content
``` highlight
<route>
    <from uri="jms:cheese"/>
    <to uri="log:before"/>
    <threads poolSize="2">
        <to uri="bean:foo"/>
        <to uri="bean:bar"/>
    </threads>
    <to uri="log:after"/>
</route>
```
:::
::::

::: paragraph
And now you would do:
:::

:::: listingblock
::: content
``` highlight
<route>
    <from uri="jms:cheese"/>
    <to uri="log:before"/>
    <threads poolSize="2"/>
    <to uri="bean:foo"/>
    <to uri="bean:bar"/>
    <to uri="log:after"/>
</route>
```
:::
::::

::: paragraph
It is the same for the other EIPs `<delay>`, `<sample>`, and
`<throttle>`.
:::

:::: sect3
#### camel-cdi {#_camel_cdi}

::: paragraph
Support for multiple CamelContexts has been removed, and therefore
`@ContextName` has been removed. Instead, use standard CDI annotations
such as `@Named` and `@ApplicationScoped`.
:::
::::
:::::::::::::

:::: sect2
### javax.script {#_javax_script}

::: paragraph
The `camel-script` component has been removed, and there is no support
for `javax.script`, which is also deprecated in the JDK and to be
removed from Java 11 onwards.
:::
::::

::::::: sect2
### Attachments API on Message {#_attachments_api_on_message}

::: paragraph
The attachments API (`javax.activation`) has been moved out of
`org.apache.camel.message` into an extension
`org.apache.camel.attachment.AttachmentMessage` from the
`camel-attachments` JAR.
:::

::: paragraph
To use this API, you can get it via the `getMessage` method on
`Exchange`:
:::

:::: literalblock
::: content
    AttachmentMessage am = exchange.getMessage(AttachmentMessage.class);
    am.addAttachment("myAtt", new DataHandler(...));
:::
::::
:::::::

:::: sect2
### Fault API on Message {#_fault_api_on_message}

::: paragraph
The fault API has been removed from `org.apache.camel.Message` as it was
only used for SOAP-WS fault message. The `camel-cxf` and
`camel-spring-ws` components for SOAP-WS has been modified to support
fault messages from their components. The option `handleFault` has also
been removed and you now need to turn this on as endpoint or component
option on `camel-cxf` or `camel-spring-ws`.
:::
::::

:::: sect2
### getOut on Exchange {#_getout_on_exchange}

::: paragraph
The `hasOut` and `getOut` methods on `Exchange` has been deprecated in
favour of using `getMessage` instead. (Side note: `camel-core` is still
using these methods in a few places to be backwards compatible and
relies on this logic as Camel was initially designed with the concept of
IN and OUT messages inspired by the JBI and SOAP-WS specifications.)
:::
::::

::::::: sect2
### OUT message removed from Simple language and Mock component {#_out_message_removed_from_simple_language_and_mock_component}

::: paragraph
The simple language has removed the OUT message concepts eg
`${out.body}`. Also, the mock component has removed OUT message from its
assertion API, eg
:::

:::: literalblock
::: content
    mock.message(0).outBody()...
:::
::::

::: paragraph
Also, the `@OutHeaders` annotation for bean parameter binding has been
removed, instead use `@Headers` instead.
:::
:::::::

:::: sect2
### Mock component {#_mock_component}

::: paragraph
The `mock` component has been moved out of `camel-core` and as part of
this work, we had to remove a number of methods on its *assertion clause
builder* that were seldom in use.
:::
::::

:::: sect2
### ActiveMQ {#_activemq}

::: paragraph
If you are using the `activemq-camel` component, then you should migrate
to use `camel-activemq` component, where the component name has changed
from `org.apache.activemq.camel.component.ActiveMQComponent` to
`org.apache.camel.component.activemq.ActiveMQComponent`.
:::
::::

:::::: sect2
### AWS {#_aws}

::: paragraph
The component `camel-aws` has been split into multiple components:
:::

::: ulist
- camel-aws-cw

- camel-aws-ddb (which contains both ddb and ddbstreams components)

- camel-aws-ec2

- camel-aws-iam

- camel-aws-kinesis (which contains both kinesis and kinesis-firehose
  components)

- camel-aws-kms

- camel-aws-lambda

- camel-aws-mq

- camel-aws-s3

- camel-aws-sdb

- camel-aws-ses

- camel-aws-sns

- camel-aws-sqs

- camel-aws-swf
:::

::: paragraph
So you'll have to explicitly add the dependencies for these components.
From the OSGi perspective, there is still a `camel-aws` Karaf feature,
which includes all the components features.
:::
::::::

:::: sect2
### FHIR {#_fhir}

::: paragraph
The camel-fhir component has upgraded it's hapi-fhir dependency to
4.1.0; Karaf support has been dropped until the hapi-fhir Karaf features
are fixed and released. The default FHIR version has been changed to R4.
Therefore, if DSTU3 is desired, it has to be explicitly set.
:::
::::

:::: sect2
### Kafka {#_kafka}

::: paragraph
The `camel-kafka` component has removed the options `bridgeEndpoint` and
`circularTopicDetection` as this is no longer needed as the component is
acting as bridging would work on Camel 2.x. In other words `camel-kafka`
will send messages to the topic from the endpoint uri. To override this
use the `KafkaConstants.OVERRIDE_TOPIC` header with the new topic. See
more details in the `camel-kafka` component documentation.
:::
::::

::::::::: sect2
### Telegram {#_telegram}

::: paragraph
The `camel-telegram` component has moved the authorization token from
uri-path to a query parameter instead, e.g. migrate
:::

:::: literalblock
::: content
    telegram:bots/myTokenHere
:::
::::

::: paragraph
to
:::

:::: literalblock
::: content
    telegram:bots?authorizationToken=myTokenHere
:::
::::
:::::::::

::::::: sect2
### JMX {#_jmx}

::: paragraph
If you run Camel standalone with just `camel-core` as a dependency, and
you want JMX enabled out of the box, then you need to add
`camel-management` as a dependency.
:::

::: paragraph
For using `ManagedCamelContext` you now need to get this an extension
from `CamelContext` as follows:
:::

:::: literalblock
::: content
    ManagedCamelContext managed = camelContext.getExtension(ManagedCamelContext.class);
:::
::::
:::::::

:::: sect2
### XSLT {#_xslt}

::: paragraph
The XSLT component has moved out of camel-core into `camel-xslt` and
`camel-xslt-saxon`. The component is separated so `camel-xslt` is for
using the JDK XSTL engine (Xalan), and `camel-xslt-saxon` is when you
use Saxon. This means that you should use `xslt` and `xslt-saxon` as
component name in your Camel endpoint URIs. If you are using XSLT
aggregation strategy, then use
`org.apache.camel.component.xslt.saxon.XsltSaxonAggregationStrategy` for
Saxon support. And use
`org.apache.camel.component.xslt.saxon.XsltSaxonBuilder` for Saxon
support if using xslt builder. Also notice that `allowStax` is also only
supported in `camel-xslt-saxon` as this is not supported by the JDK
XSLT.
:::
::::

:::::: sect2
### camel-sql {#_camel_sql}

::: paragraph
The `JdbcAggregationRepository` optimistic locking feature has been
fixed to work on a distributed environment and every database. There is
a new `version` column that is required and must be added to the
repository:
:::

:::: listingblock
::: content
``` highlight
CREATE TABLE aggregation (
 id varchar(255) NOT NULL,
 exchange blob NOT NULL,
 version BIGINT NOT NULL,
 constraint aggregation_pk PRIMARY KEY (id)
);
CREATE TABLE aggregation_completed (
 id varchar(255) NOT NULL,
 exchange blob NOT NULL,
 version BIGINT NOT NULL,
 constraint aggregation_completed_pk PRIMARY KEY (id)
);
```
:::
::::
::::::

::::::::: sect2
### Configuring global options on CamelContext {#_configuring_global_options_on_camelcontext}

::: paragraph
In Camel 2.x we have deprecated `getProperties` on `CamelContext` in
favour of `getGlobalOptions`, so you should migrate to:
:::

:::: listingblock
::: content
``` highlight
context.getGlobalOptions().put("CamelJacksonEnableTypeConverter", "true");
context.getGlobalOptions().put("CamelJacksonTypeConverterToPojo", "true");
```
:::
::::

::: paragraph
and in XML:
:::

:::: listingblock
::: content
``` highlight
<globalOptions>
  <globalOption key="CamelJacksonEnableTypeConverter" value="true"/>
  <globalOption key="CamelJacksonTypeConverterToPojo" value="true"/>
</globalOptions>
```
:::
::::
:::::::::

:::: sect2
### Main class {#_main_class_2}

::: paragraph
The `Main` class from `camel-core`, `camel-spring` and `camel-cdi` has
been modified to only support a single `CamelContext` which was really
its intention, but there was some old crufty code for multiple Camels.
The methods `getCamelContextMap` and `getCamelContexts` have been
removed, and there is just a `getCamelContext` method now.
:::
::::

::::: sect2
### POJO annotations {#_pojo_annotations}

::: paragraph
The `ref` attribute on `@Consume`, `@Produce` and `@EndpointInject` has
been removed. Instead use the ref component in the `uri` attribute, eg
`uri = "ref:myName"`.
:::

::: paragraph
The uri attribute has been deprecated, instead use value, which allows a
shorthand style, from using `@Consume(uri = "jms:cheese")` to
`@Consume("jms:cheese")`.
:::
:::::

:::: sect2
### Routes with multiple inputs {#_routes_with_multiple_inputs}

::: paragraph
In Camel 2.x you could have 2 or more inputs to Camel routes, however
this was not supported in all use-cases in Camel, and this functionality
is seldom in use. This has also been deprecated in Camel 2.x. In Camel 3
we have removed the remaining code for specifying multiple inputs to
routes, and its now only possible to specify exactly only 1 input to a
route.
:::
::::

:::: sect2
### Crypto Component {#_crypto_component}

::: paragraph
The default signature algorithm has changed for the Crypto (JCE)
Component - it is now SHA256withRSA (before it was SHA1WithDSA).
:::
::::

:::: sect2
### Crypto DataFormat {#_crypto_dataformat}

::: paragraph
The default encryption algorithm has changed for the Crypto (JCE)
DataFormat - it is now required to set a value for it (meaning that the
default is null). Before the default value was \"DES/CBC/PKCS5Padding\".
:::
::::

:::: sect2
### JSon DataFormat {#_json_dataformat}

::: paragraph
The default JSON library with the JSON dataformat has changed from
`XStream` to `Jackson`.
:::
::::

:::: sect2
### Shiro Component {#_shiro_component}

::: paragraph
The default encryption key for the Shiro component has been removed, so
now it is mandatory to supply the key/passphrase.
:::
::::

:::: sect2
### XML Security Component {#_xml_security_component}

::: paragraph
The default signature algorithm has changed for the XML Security
Component - it is now RSA-SHA256 (before it was RSA-SHA1).
:::
::::

::::: sect2
### XML Security DataFormat {#_xml_security_dataformat}

::: paragraph
The default encryption key for the XML Security DataFormat has been
removed, so it is now mandatory to supply the key String/bytes if you
are using symmetric encryption. This means that some methods are removed
that used the `XMLSecurityDataFormat` without specifying a key.
:::

::: paragraph
In addition, the default symmetric encryption algorithm has changed from
Triple DES to AES-256 in GCM mode.
:::
:::::

:::: sect2
### Zip and GZip DataFormat {#_zip_and_gzip_dataformat}

::: paragraph
The zip and gzip dataformat has been renamed to zipdeflater and
gzipdeflater as they are for deflating using the zip/gzip compression;
and not for working with zip/gzip files. Instead use camel-zipfile
dataformat. Also these dataformats has been moved out of `camel-core`
into `camel-zip-deflater` JAR. The XML and Java DSL has also been
modified so you should migrate there too to use their new names. And if
you use these data formats you need to add the `camel-zip-deflater` as
dependency as they are no longer included as transitive dependency with
`camel-core`.
:::
::::

:::: sect2
### Simple language {#_simple_language}

::: paragraph
The functionality to change the simple language tokens for start/end
functions has been removed. The default tokens with `${xxx}` and
`$simple{xxx}` is now hardcoded (optimized). The functionality to change
these tokens was never really in use and would only confuse Camel users
if a new syntax are in use.
:::
::::

::::::::::::::::: sect2
### Moved APIs {#_moved_apis}

::: paragraph
The following API changes may affect your existing Camel applications,
which needs to be migrated.
:::

::::::: sect3
#### CamelContext {#_camelcontext}

::: paragraph
The methods on `CamelContext` that are related to catalog has been moved
into a new `CatalogCamelContext` interface, which you can access by
adapting:
:::

:::: literalblock
::: content
    CatalogCamelContext ccc = context.adapt(CatalogCamelContext.class);
:::
::::

::: paragraph
The `loadRouteDefinitions` and `loadRestDefinitions` on
`ModelCamelContext` has been changed to `addRouteDefinitions` and
`addRestDefinitions` to be aligned with the other methods. You can find
`getModelToXMLDumper` and `getXMLRoutesDefinitionLoader` loader methods
on the `ExtendedCamelContext` class.
:::
:::::::

:::::: sect3
#### ModelCamelContext {#_modelcamelcontext}

::: paragraph
If you need to access the routes model (such as `addRouteDefinitions`,
etc.), then you need to adapt form CamelContext as shown:
:::

:::: literalblock
::: content
    ModelCamelContext mcc = camelContext.adapt(ModelCamelContext.class);
:::
::::
::::::

:::::: sect3
#### Extended CamelContext {#_extended_camelcontext}

::: paragraph
The APIs on `CamelContext` has been reduced a bit to focus on relevant
API for Camel end users. The advanced use-cases and for SPI and
component developers, then some of the APIs from `CamelContext` has been
moved to `ExtendedCamelContext` which you can access via adapt:
:::

:::: literalblock
::: content
    ExtendedCamelContext ecc = context.adapt(ExtendedCamelContext.class);
:::
::::
::::::
:::::::::::::::::

:::::::::::::::::::::::::::::::::::: sect2
### Checked vs unchecked exceptions {#_checked_vs_unchecked_exceptions}

::: paragraph
Most of the Camel exception classes have been migrated to be unchecked
(e.g., extends `RuntimeException`).
:::

::: paragraph
Also, the lifecycle of the `start`, `stop` and `suspend`, `resume`
methods on `Service` and `SuspendableService` has been changed to not
throw checked exceptions.
:::

::::::::::::::::::::::::::::::::: sect3
#### Generic Information {#_generic_information}

::: paragraph
The class `SimpleRegistry` is moved from `org.apache.camel.impl` to
`org.apache.camel.support`. Also you should favour using the
`org.apache.camel.support.DefaultRegistry` instead. Also you should use
the `bind` operation instead of `put` to add entries to the
`SimpleRegistry` or `DefaultRegistry`.
:::

::: paragraph
The class `CompositeRegistry` and `PropertyPlaceholderDelegateRegistry`
has been deleted. Instead use `DefaultRegistry`.
:::

::: paragraph
The classes from `org.apache.camel.impl` that was intended to support
Camel developers building custom components has been moved out of
`camel-core` into `camel-support` into the `org.apache.camel.support`
package. If you have built custom Camel components that may have used
some of these APIs you would then need to migrate. A large part of
classes from the `org.apache.camel.impl` package have been moved to the
`org.apache.camel.impl.engine` package in `camel-base`.
:::

::: paragraph
All the classes in `org.apache.camel.util.component` has been moved from
the camel-core JAR to the package `org.apache.camel.support.component`
in the `camel-support` JAR.
:::

::: paragraph
The method `xslt` has been removed from
`org.apache.camel.builder.AggregationStrategies`. Instead use the
`XsltAggregationStrategy` from `camel-xslt` JAR directly.
:::

::: paragraph
The getter/setter for `bindingMode` on `RestEndpoint` has been changed
to use type `org.apache.camel.spi.RestConfiguration.RestBindingMode`
from `camel-api` JAR. Instead of using this type class you can also call
the setter method with string type instead.
:::

::: paragraph
The `activemq-camel` component has been moved from ActiveMQ into Camel
and it is now called `camel-activemq`, the package has been changed
accordingly to `org.apache.camel.component.activemq`
:::

::: paragraph
The method `includeRoutes` on `RouteBuilder` has been removed. This
functionality was not fully in use and was deprecated in Camel 2.x.
:::

::: paragraph
The exception `PredicateValidationException` has been moved from package
`org.apache.camel.processor.validation` to
`org.apache.camel.support.processor.PredicateValidationException`.
:::

::: paragraph
The class `org.apache.camel.util.toolbox.AggregationStrategies` has been
moved to `org.apache.camel.builder.AggregationStrategies`.
:::

::: paragraph
The class `org.apache.camel.util.toolbox.AggregationStrategies` has been
moved to `org.apache.camel.builder.AggregationStrategies`.
:::

::: paragraph
The class `org.apache.camel.processor.RedeliveryPolicy` has been moved
to `org.apache.camel.processor.errorhandler.RedeliveryPolicy`.
:::

::: paragraph
The class
`org.apache.camel.processor.loadbalancer.SimpleLoadBalancerSupport` has
been removed, instead use
`org.apache.camel.processor.loadbalancer.LoadBalancerSupport`.
:::

::: paragraph
The class `org.apache.camel.management.JmxSystemPropertyKeys` has been
moved to `org.apache.camel.api.management.JmxSystemPropertyKeys`.
:::

::: paragraph
The class `org.apache.camel.builder.xml.XPathBuilder` has been moved to
`org.apache.camel.language.xpath.XPathBuilder` and in the `camel-xpath`
JAR.
:::

::: paragraph
The annotation `org.apache.camel.language.XPath` has been moved to
`org.apache.camel.language.xpath.XPath` and in the `camel-xpath` JAR.
:::

::: paragraph
The exception `org.apache.camel.builder.xml.InvalidXPathExpression` has
been renamed to `org.apache.camel.language.xpath.InvalidXPathException`
and in the `camel-xpath` JAR.
:::

::: paragraph
The annotation `org.apache.camel.language.Bean` has been moved to
`org.apache.camel.language.bean.Bean` and in the `camel-bean` JAR.
:::

::: paragraph
The annotation `org.apache.camel.language.Simple` has been moved to
`org.apache.camel.language.simple.Simple`.
:::

::: paragraph
The annotation `org.apache.camel.Constant` has been removed, use
`@Simple` instead.
:::

::: paragraph
The annotation `org.apache.camel.language.SpEL` has been moved to
`org.apache.camel.language.spel.SpEL` and in the `camel-spring` JAR.
:::

::: paragraph
The annotation `org.apache.camel.InvokeOnHeader` and
`org.apache.camel.InvokeOnHeaders` has been moved to the
`org.apache.camel.spi` package.
:::

::: paragraph
The class `OutputStreamBuilder` has been moved from package
`org.apache.camel.converter.stream` to
`org.apache.camel.support.builder` package.
:::

::: paragraph
Rename various APIs in camel-core to fix the typo `chiper` to `cipher`.
:::

::: paragraph
The classes `ReloadStrategySupport` and `FileWatcherReloadStrategy` has
been removed.
:::

::: paragraph
The `MessageHistoryFactory` interface has some options to filter and
copy the message and a slight change in its API.
:::

::: paragraph
Removed `TypeConverterAware` as you should instead use `Exchange` as
parameter to the type converter method.
:::

::: paragraph
The `Component` and `DataFormat` interfaces now extend `Service` as
components and data formats should also have service contract to manage
their lifecycle. The default base classes already implements these
interfaces.
:::

::: paragraph
The class `FactoryFinder` has changed its API to use `Optional` as
return types instead of throwing checked `FactoryNotFoundException` or
`ClassNotFoundException` etc.
:::

::: paragraph
The option `resolvePropertyPlaceholders` on all the components has been
removed, as property placeholders are already supported via Camel Main,
Camel Spring Boot and other means.
:::
:::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::

:::::: sect2
### camel-test {#_camel_test}

::: paragraph
If you are using camel-test and override the `createRegistry` method,
for example to register beans from the `JndiRegistry` class, then this
is no longer necessary, and instead you should use the `bind` method
from the `Registry` API which you can call directly from `CamelContext`,
such as:
:::

:::: literalblock
::: content
    context.getRegistry().bind("myId", myBean);
:::
::::
::::::

:::::: sect2
### Controlling routes {#_controlling_routes}

::: paragraph
The `startRoute`, `stopRoute`, `suspendRoute`, `resumeRoute`,
`getRouteStatus`, and other related methods on `CamelContext` has been
moved to the `RouteController` as shown below:
:::

:::: literalblock
::: content
    context.getRouteController().startRoute("myRoute");
:::
::::
::::::

:::: sect2
### JMX events {#_jmx_events}

::: paragraph
All the events from package `org.apache.camel.management.event` has been
moved to the class `org.apache.camel.spi.CamelEvent` as sub-classes, for
example the event for CamelContext started would be
`CamelEvent.CamelContextStartedEvent`.
:::
::::

:::::::::::: sect2
### AdviceWith {#_advicewith}

::: paragraph
Testing using `adviceWith` currently needs to be changed from:
:::

:::: listingblock
::: content
``` highlight
context.getRouteDefinition("start").adviceWith(camelContext, new AdviceWithRouteBuilder() {
  ...
}
```
:::
::::

::: paragraph
to using style:
:::

:::: listingblock
::: content
``` highlight
ModelCamelContext mcc = camelContext.adapt(ModelCamelContext.class);
RouteReifier.adviceWith(mcc.getRouteDefinition("start"), mcc, new AdviceWithRouteBuilder() {
  ...
}
```
:::
::::

::: paragraph
However its even easier using lambda style with `AdviceWithRouteBuilder`
directly:
:::

:::: listingblock
::: content
``` highlight
AdviceWith.adviceWith(context, "myRoute", a -> {
  a.replaceFromWith("direct:start");
}
```
:::
::::
::::::::::::

:::: sect2
### Generic Classes {#_generic_classes}

::: paragraph
The class `JNDIContext` has been moved from
`org.apache.camel.util.jndi.JNDIContext` in the camel-core JAR to
`org.apache.camel.support.jndi.JNDIContext` and moved to the
`camel-support` JAR.
:::
::::

::::: sect2
### EIPs {#_eips}

::: paragraph
The `circuitBreaker` load-balancer EIP was deprecated in Camel 2.x, and
has been removed. Instead use Hystrix EIP as the load-balancer.
:::

::: paragraph
The class `ThreadPoolRejectedPolicy` has been moved from
`org.apache.camel.ThreadPoolRejectedPolicy` to
`org.apache.camel.util.concurrent.ThreadPoolRejectedPolicy`.
:::
:::::

::::: sect2
### Languages {#_languages}

::: paragraph
The simple language `property` function was deprecated in Camel 2.x and
has been removed. Use `exchangeProperty` as function name.
:::

::: paragraph
The terser language has been renamed from `terser` to `hl7terser`.
:::
:::::

:::: sect2
### JSSE {#_jsse}

::: paragraph
The classes from `org.apache.camel.util.jsse` has been moved to
`org.apache.camel.support.jsse`.
:::
::::

:::::::::::::::::::: sect2
### Helpers and support {#_helpers_and_support}

::: paragraph
The class `AsyncProcessorHelper` has been moved from
`org.apache.camel.util.AsyncProcessorHelper` in the camel-core JAR to
`org.apache.camel.support.AsyncProcessorHelper` and moved to the
`camel-support` JAR.
:::

::: paragraph
The class `AsyncProcessorConverterHelper` has been moved from
`org.apache.camel.util.AsyncProcessorConverterHelper` in the camel-core
JAR to `org.apache.camel.support.AsyncProcessorConverterHelper` and
moved to the `camel-support` JAR.
:::

::: paragraph
The class `CamelContextHelper` has been moved from
`org.apache.camel.util.CamelContextHelper` in the camel-core JAR to
`org.apache.camel.support.CamelContextHelper` and moved to the
`camel-support` JAR.
:::

::: paragraph
The class `EndpointHelper` has been moved from
`org.apache.camel.util.EndpointHelper` in the camel-core JAR to
`org.apache.camel.support.EndpointHelper` and moved to the
`camel-support` JAR.
:::

::: paragraph
The class `EventHelper` has been moved from
`org.apache.camel.util.EventHelper` in the camel-core JAR to
`org.apache.camel.support.EventHelper` and moved to the `camel-support`
JAR.
:::

::: paragraph
The class `ExchangeHelper` has been moved from
`org.apache.camel.util.ExchangeHelper` in the camel-core JAR to
`org.apache.camel.support.ExchangeHelper` and moved to the
`camel-support` JAR.
:::

::: paragraph
The class `GZIPHelper` has been moved from
`org.apache.camel.util.GZIPHelper` in the camel-core JAR to
`org.apache.camel.support.GZIPHelper` and moved to the `camel-support`
JAR.
:::

::: paragraph
The class `JsonSchemaHelper` has been moved from
`org.apache.camel.util.JsonSchemaHelper` in the camel-core JAR to
`org.apache.camel.support.JsonSchemaHelper` and moved to the
`camel-support` JAR.
:::

::: paragraph
The class `MessageHelper` has been moved from
`org.apache.camel.util.MessageHelper` in the camel-core JAR to
`org.apache.camel.support.MessageHelper` and moved to the
`camel-support` JAR.
:::

::: paragraph
The class `ObjectHelper` has been moved from
`org.apache.camel.util.ObjectHelper` in the camel-core JAR and split
into `org.apache.camel.support.ObjectHelper` and moved to the
`camel-support` JAR and into `org.apache.camel.util.ObjectHelper` and
moved to the `camel-util` JAR. This has been done to isolate the methods
using `camel-api` JAR: those method are in the `camel-support` JAR, the
other in the `camel-util` JAR.
:::

::: paragraph
The class `PlatformHelper` has been moved from
`org.apache.camel.util.PlatformHelper` in the camel-core JAR to
`org.apache.camel.support.PlatformHelper` and moved to the
`camel-support` JAR.
:::

::: paragraph
The class `PredicateAssertHelper` has been moved from
`org.apache.camel.util.PredicateAssertHelper` in the camel-core JAR to
`org.apache.camel.support.PredicateAssertHelper` and moved to the
`camel-support` JAR.
:::

::: paragraph
The class `ResolverHelper` has been moved from
`org.apache.camel.util.ResolverHelper` in the camel-core JAR to
`org.apache.camel.support.ResolverHelper` and moved to the
`camel-support` JAR.
:::

::: paragraph
The class `ResourceHelper` has been moved from
`org.apache.camel.util.ResourceHelper` in the camel-core JAR to
`org.apache.camel.support.ResourceHelper` and moved to the
`camel-support` JAR.
:::

::: paragraph
The class `RestProducerFactoryHelper` has been moved from
`org.apache.camel.spi.RestProducerFactoryHelper` in the camel-core JAR
to `org.apache.camel.support.RestProducerFactoryHelper` and moved to the
`camel-support` JAR.
:::

::: paragraph
The class `ServiceHelper` has been moved from
`org.apache.camel.util.ServiceHelper` in the camel-core JAR to
`org.apache.camel.support.service.ServiceHelper` and moved to the
`camel-api` JAR.
:::

::: paragraph
The class `UnitOfWorkHelper` has been moved from
`org.apache.camel.util.UnitOfWorkHelper` in the camel-core JAR to
`org.apache.camel.support.UnitOfWorkHelper` and moved to the
`camel-support` JAR.
:::
::::::::::::::::::::

::::: sect2
### Idempotent Repositories {#_idempotent_repositories}

::: paragraph
The class `FileIdempotentRepository` has been moved from
`org.apache.camel.processor.idempotent.FileIdempotentRepository` in the
camel-core JAR to
`org.apache.camel.support.processor.idempotent.FileIdempotentRepository`
and moved to the `camel-support` JAR.
:::

::: paragraph
The class `MemoryIdempotentRepository` has been moved from
`org.apache.camel.processor.idempotent.MemoryIdempotentRepository` in
the camel-core JAR to
`org.apache.camel.support.processor.idempotent.MemoryIdempotentRepository`
and moved to the `camel-support` JAR.
:::
:::::

::::: sect2
### Route Policies {#_route_policies}

::: paragraph
The class `org.apache.camel.support.RoutePolicySupport` has been moved
to the `camel-support` JAR. The return type from `startConsumer` and
`stopConsumer` has been changed from `boolean` to `void` as they always
returned `true` before.
:::

::: paragraph
The class `org.apache.camel.impl.ThrottlingInflightRoutePolicy` has been
moved to `org.apache.camel.throttling.ThrottlingInflightRoutePolicy`
:::
:::::

::::: sect2
### Aggregation {#_aggregation}

::: paragraph
The class `XsltAggregationStrategy` has been moved from
`org.apache.camel.util.toolbox.XsltAggregationStrategy` in the
camel-core JAR to
`org.apache.camel.component.xslt.XsltAggregationStrategy` and moved to
the `camel-xslt` JAR.
:::

::: paragraph
When using the option `groupedExchange` on the aggregator EIP then the
output of the aggregation is now longer also stored in the exchange
property `Exchange.GROUPED_EXCHANGE`. This behaviour was already
deprecated from Camel 2.13 onwards.
:::
:::::

:::: sect2
### Fallback type converters {#_fallback_type_converters}

::: paragraph
The `@FallbackConverter` annotation has been removed, and you should use
`@Converter(fallback = true)` instead. Also you can set
`@Converter(generateLoader = true)` on the converter class to allow
Camel to generate source code for loading type converters in a faster
way.
:::
::::

:::: sect2
### Removed JMX APIs for explaining EIPs, components, etc. {#_removed_jmx_apis_for_explaining_eips_components_etc}

::: paragraph
The APIs that could find, and explain EIPs, components, endpoints, etc.
have been removed. These APIs have little value for production runtimes,
and you can obtain this kind of information via the `camel-catalog`.
Also the related Camel Karaf commands that used these APIs has been
removed.
:::
::::

::::: sect2
### Other changes {#_other_changes}

::: paragraph
The default for use breadcrumbs has been changed from `true` to `false`.
:::

::: paragraph
The `ProducerTemplate` and `ConsumerTemplate` now fails when being used,
if `CamelContext` has not been started first.
:::
:::::

::::::: sect2
### XML DSL Migration {#_xml_dsl_migration}

::: paragraph
The XML DSL has been changed slightly.
:::

::: paragraph
The custom load balancer EIP has changed from `<custom>` to
`<customLoadBalancer>`
:::

::: paragraph
The XMLSecurity data format has renamed the attribute
`keyOrTrustStoreParametersId` to `keyOrTrustStoreParametersRef` in the
`<secureXML>` tag.
:::

::: paragraph
The `<zipFile>` data format has been renamed to `<zipfile>`.
:::
:::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::: sect1
## Migrating Camel Maven Plugins {#_migrating_camel_maven_plugins}

::::::: sectionbody
::: paragraph
The `camel-maven-plugin` has been split up into two maven plugins:
:::

::: ulist
- camel-maven-plugin

- camel-report-maven-plugin
:::

::: paragraph
The former has the `run` goal, which is intended for quickly running
Camel applications standalone.
:::

::: paragraph
The `camel-report-maven-plugin` has the `validate` and `route-coverage`
goals which is used for generating reports of your Camel projects such
as validating Camel endpoint URIs and route coverage reports, etc.
:::
:::::::
::::::::

::::: sect1
## Known Issues {#_known_issues}

:::: sectionbody
::: paragraph
There is an issue with MDC logging and correctly transferring the Camel
breadcrumb id's under certain situations with routing over asynchronous
endpoints, due to the internal routing engine refactorings. This change
also affects the `camel-zipkin` component, which may not correctly
transfer the span id's when using MDC logging as well.
:::
::::
:::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
