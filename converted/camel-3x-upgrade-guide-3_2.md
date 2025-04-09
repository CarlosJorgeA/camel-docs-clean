::: {#header}
# Apache Camel 3.x Upgrade Guide
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
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

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Upgrading Camel 3.1 to 3.2 {#_upgrading_camel_3_1_to_3_2}

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
:::: sect2
### JndiRegistry {#_jndiregistry}

::: paragraph
The deprecated class `org.apache.camel.impl.JndiRegistry` has been
removed. The class `org.apache.camel.support.jndi.JndiBeanRepository`
located in `org.apache.camel:camel-support` should be used instead.
:::
::::

::::::: sect2
### Rest Configuration {#_rest_configuration}

::: paragraph
The rest configuration has been simplified and there is now a single
RestConfiguration instance
([CAMEL-13844](https://issues.apache.org/jira/browse/CAMEL-13844)) per
Camel Context.
:::

::: paragraph
The following methods have been removed:
:::

::: ulist
- org.apache.camel.CamelContext#addRestConfiguration(RestConfiguration
  restConfiguration)

- org.apache.camel.CamelContext#getRestConfiguration(String component,
  boolean defaultIfNotFound)

- org.apache.camel.CamelContext#getRestConfigurations
:::

::: paragraph
[https://issues.apache.org/jira/browse/CAMEL-13844](https://issues.apache.org/jira/browse/CAMEL-13844){.bare}
:::
:::::::

:::::: sect2
### Camel Cloud {#_camel_cloud}

::: paragraph
The following `ServiceDiscovery` implementation has been removed:
:::

::: ulist
- org.apache.camel.impl.cloud.CachingServiceDiscovery

- org.apache.camel.impl.cloud.CachingServiceDiscoveryFactory
:::

::: paragraph
[https://issues.apache.org/jira/browse/CAMEL-14813](https://issues.apache.org/jira/browse/CAMEL-14813){.bare}
:::
::::::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect2
### Camel with Karaf and OSGi {#_camel_with_karaf_and_osgi}

::: paragraph
Camel on Apache Karaf / OSGi has been moved to its own project at:
[https://github.com/apache/camel-karaf](https://github.com/apache/camel-karaf){.bare}
:::

::: paragraph
The Maven dependencies has changed the groupid from `org.apache.camel`
to `org.apache.camel.karaf`.
:::

::: paragraph
For example the `camel-blueprint` would be changed from
:::

:::: listingblock
::: content
``` highlight
<dependency>
  <groupId>org.apache.camel</groupId>
  <artifactId>camel-blueprint</artifactId>
  <version>3.1.0</version>
</dependency>
```
:::
::::

::: paragraph
To:
:::

:::: listingblock
::: content
``` highlight
<dependency>
  <groupId>org.apache.camel.karaf</groupId>
  <artifactId>camel-blueprint</artifactId>
  <version>3.2.0</version>
</dependency>
```
:::
::::

::: paragraph
The Camel Karaf features are the same as before, you can still install
Camel in Karaf shell via:
:::

:::: listingblock
::: content
``` highlight
feature:repo-add camel 3.2.0
feature:install camel
```
:::
::::

::::::::::::::::::::::::::::::::::::::: sect3
#### Other components involved {#_other_components_involved}

::: ulist
- Camel-test-blueprint
:::

:::: listingblock
::: content
``` highlight
<dependency>
  <groupId>org.apache.camel</groupId>
  <artifactId>camel-test-blueprint</artifactId>
  <version>3.1.0</version>
</dependency>
```
:::
::::

::: paragraph
To:
:::

:::: listingblock
::: content
``` highlight
<dependency>
  <groupId>org.apache.camel.karaf</groupId>
  <artifactId>camel-test-blueprint</artifactId>
  <version>3.2.0</version>
</dependency>
```
:::
::::

::: ulist
- Camel-test-karaf
:::

:::: listingblock
::: content
``` highlight
<dependency>
  <groupId>org.apache.camel</groupId>
  <artifactId>camel-test-karaf</artifactId>
  <version>3.1.0</version>
</dependency>
```
:::
::::

::: paragraph
To:
:::

:::: listingblock
::: content
``` highlight
<dependency>
  <groupId>org.apache.camel.karaf</groupId>
  <artifactId>camel-test-karaf</artifactId>
  <version>3.2.0</version>
</dependency>
```
:::
::::

::: ulist
- Camel-eventadmin
:::

:::: listingblock
::: content
``` highlight
<dependency>
  <groupId>org.apache.camel</groupId>
  <artifactId>camel-eventadmin</artifactId>
  <version>3.1.0</version>
</dependency>
```
:::
::::

::: paragraph
To:
:::

:::: listingblock
::: content
``` highlight
<dependency>
  <groupId>org.apache.camel.karaf</groupId>
  <artifactId>camel-eventadmin</artifactId>
  <version>3.2.0</version>
</dependency>
```
:::
::::

::: ulist
- Camel-kura
:::

:::: listingblock
::: content
``` highlight
<dependency>
  <groupId>org.apache.camel</groupId>
  <artifactId>camel-kura</artifactId>
  <version>3.1.0</version>
</dependency>
```
:::
::::

::: paragraph
To:
:::

:::: listingblock
::: content
``` highlight
<dependency>
  <groupId>org.apache.camel.karaf</groupId>
  <artifactId>camel-kura</artifactId>
  <version>3.2.0</version>
</dependency>
```
:::
::::

::: ulist
- Camel-osgi-activator
:::

:::: listingblock
::: content
``` highlight
<dependency>
  <groupId>org.apache.camel</groupId>
  <artifactId>camel-osgi-activator</artifactId>
  <version>3.1.0</version>
</dependency>
```
:::
::::

::: paragraph
To:
:::

:::: listingblock
::: content
``` highlight
<dependency>
  <groupId>org.apache.camel.karaf</groupId>
  <artifactId>camel-osgi-activator</artifactId>
  <version>3.2.0</version>
</dependency>
```
:::
::::

::: ulist
- Camel-paxlogging
:::

:::: listingblock
::: content
``` highlight
<dependency>
  <groupId>org.apache.camel</groupId>
  <artifactId>camel-paxlogging</artifactId>
  <version>3.1.0</version>
</dependency>
```
:::
::::

::: paragraph
To:
:::

:::: listingblock
::: content
``` highlight
<dependency>
  <groupId>org.apache.camel.karaf</groupId>
  <artifactId>camel-paxlogging</artifactId>
  <version>3.2.0</version>
</dependency>
```
:::
::::
:::::::::::::::::::::::::::::::::::::::

::::::: sect3
#### Introducing a camel-karaf-bom {#_introducing_a_camel_karaf_bom}

::: paragraph
We introduce a camel-karaf-bom too, so the end-users should be able to
have all of this dependencies easier:
:::

:::: listingblock
::: content
``` highlight
    <dependencyManagement>
        <dependencies>
            <!-- Add Camel BOM -->
            <dependency>
                <groupId>org.apache.camel</groupId>
                <artifactId>camel-bom</artifactId>
                <version>${project.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!-- Add Camel Karaf BOM -->
            <dependency>
                <groupId>org.apache.camel.karaf</groupId>
                <artifactId>camel-karaf-bom</artifactId>
                <version>${camel.karaf.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```
:::
::::

::: paragraph
In this way you'll get both the boms and all the \"goodies\"
:::
:::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: sect2
### camel-elytron {#_camel_elytron}

::: paragraph
Transitive dependencies from org.wildfly.security:wildfly-elytron were
removed.
:::
::::

:::: sect2
### camel-spark-rest {#_camel_spark_rest}

::: paragraph
This component has been removed. Use any of the other REST capable
components, such as `camel-jetty`, `camel-netty-http` or
`camel-undertow`.
:::
::::

::::: sect2
### EIPs with cacheSize option {#_eips_with_cachesize_option}

::: paragraph
The `cacheSize` option on EIPs has been improved to reduce memory usage
when the cache is disabled by setting the value to -1. One of the
optimizations is that new endpoints will not be added to the endpoint
registry, but discarded after use. This avoids storing additional
endpoints in the cache (memory) as the cache should be disabled
(cacheSize=-1).
:::

::: paragraph
See more details in the documentation for the `cacheSize` option on the
EIPs.
:::
:::::

::::::::::::::::::: sect2
### Configuring components via Java setters {#_configuring_components_via_java_setters}

::: paragraph
Configuring Camel components from plain Java code has changed in some
components where they were using delegate setters for a nested
configuration class. These delegates has been removed, to ensure
configuration is more consistent and aligned with how endpoints is
configured as well, and by using source code generated configurer
classes.
:::

::: paragraph
The following Camel components has been affected and changed on the
component level:
:::

::: ulist
- camel-aws

- camel-aws2

- camel-consul

- camel-etcd

- camel-infinispan

- camel-kafka

- camel-servicenow

- camel-ssh

- camel-stomp

- camel-xmlsecurity

- camel-yammer
:::

::: paragraph
This only affects if you are configuring these components using Java
code or XML `<bean>` style.
:::

::: paragraph
For example
:::

:::: listingblock
::: content
``` highlight
KafkaComponent kafka = new KafkaComponent();
kafka.setBrokers("mybroker1:1234,mybroker2:1234");
```
:::
::::

::: paragraph
Should now be:
:::

:::: listingblock
::: content
``` highlight
KafkaComponent kafka = new KafkaComponent();
kafka.getConfiguration().setBrokers("mybroker1:1234,mybroker2:1234");
```
:::
::::

::: paragraph
And in XML:
:::

:::: listingblock
::: content
``` highlight
<bean id="kafka" class="org.apache.camel.component.kafka.KafkaComponent">
  <property name="brokers" value="mybroker1:1234,mybroker2:1234"/>
</bean>
```
:::
::::

::: paragraph
Should now be:
:::

:::: listingblock
::: content
``` highlight
<bean id="kafka" class="org.apache.camel.component.kafka.KafkaComponent">
  <property name="configuration">
    <property name="brokers" value="mybroker1:1234,mybroker2:1234"/>
  </property>
</bean>
```
:::
::::
:::::::::::::::::::

::::::::::: sect2
### Configuring components via Spring Boot auto configuration {#_configuring_components_via_spring_boot_auto_configuration}

::: paragraph
Configuring Camel spring boot components has changed its option keys to
be flattened and have the `.configuration` prefix removed now.
:::

::: paragraph
Before in application.properties
:::

:::: listingblock
::: content
``` highlight
camel.component.kafka.configuration.brokers=mybroker1:1234,mybroker2:1234
```
:::
::::

::: paragraph
Should now be
:::

:::: listingblock
::: content
``` highlight
camel.component.kafka.brokers=mybroker1:1234,mybroker2:1234
```
:::
::::

::: paragraph
This applies to all the Camel spring boot *starter* JARs where basically
`.configuration` should be removed.
:::
:::::::::::

:::::::::: sect2
### Configuring camel-activemq, camel-amqp and camel-stomp via Spring Boot auto configuration {#_configuring_camel_activemq_camel_amqp_and_camel_stomp_via_spring_boot_auto_configuration}

::: paragraph
When configuring these components from spring boot auto-configuration
then the URL for the broker was named `broker-u-r-l` in the spring boot
auto configuration support. This has been renamed to `broker-url` and a
few other options too.
:::

::: paragraph
Before:
:::

:::: listingblock
::: content
``` highlight
camel.component.activemq.broker-u-r-l=tcp://localhost:61616
```
:::
::::

::: paragraph
After:
:::

:::: listingblock
::: content
``` highlight
camel.component.activemq.broker-url=tcp://localhost:61616
```
:::
::::
::::::::::

:::::::::: sect2
### camel-any23 {#_camel_any23}

::: paragraph
The XML DSL has changed for the `<configuration>` element, which now is
flattened so the key/values should be configured on it directly:
:::

::: paragraph
Before:
:::

:::: listingblock
::: content
``` highlight
<dataFormats>
  <any23 id="any23" baseURI ="http://mock.foo/bar" outputFormat="TURTLE" >
    <configuration>
      <property key="any23.extraction.metadata.nesting" value="off" />
      <property key="another-key" value="another-value" />
    </configuration>
    <extractors>html-head-title</extractors>
  </any23>
</dataFormats>
```
:::
::::

::: paragraph
After:
:::

:::: listingblock
::: content
``` highlight
<dataFormats>
  <any23 id="any23" baseURI ="http://mock.foo/bar" outputFormat="TURTLE" >
    <configuration key="any23.extraction.metadata.nesting" value="off"/>
    <configuration key="another-key" value="another-value"/>
    <extractors>html-head-title</extractors>
  </any23>
</dataFormats>
```
:::
::::
::::::::::

:::: sect2
### camel-avro {#_camel_avro}

::: paragraph
The avro component and data format has been split up into two JARs. The
dataformat is in `camel-avro` JAR and the component in `camel-avro-rpc`
JAR.
:::
::::

:::: sect2
### camel-infinispan {#_camel_infinispan}

::: paragraph
Camel now requires endpoint URIs to include context-path which means the
endpoint URI `infinispan` should be changed to `infinispan:current`.
:::
::::

::::: sect2
### google-pubnub {#_google_pubnub}

::: paragraph
The google-pubnub component has been improved to use a new Java library
and become faster.
:::

::: paragraph
Support for Apache Karaf has been removed.
:::
:::::

:::::::::::::: sect2
### camel-xstream {#_camel_xstream}

::: paragraph
The XML DSL has changed for the `<converters>`, `<alias>`,
`implicitCollections`, and `omitFields` elements, which now is flattened
so the key/values should be configured on it directly.
:::

::: paragraph
Before:
:::

:::: listingblock
::: content
``` highlight
<xstream id="xstream-1" mode="NO_REFERENCES"
    permissions="-org.apache.camel.dataformat.xstream.*,org.apache.camel.dataformat.xstream.PurchaseHistory,org.apache.camel.dataformat.xstream.PurchaseOrder">
    <converters>
        <converter class="org.apache.camel.dataformat.xstream.XStreamConfigurationTest$PurchaseOrderConverter" />
    </converters>
    <aliases>
                <alias name="purchase-order" class="org.apache.camel.dataformat.xstream.PurchaseOrder" />
    </aliases>
    <implicitCollections>
       <class name="org.apache.camel.dataformat.xstream.PurchaseHistory">
          <field>history</field>
       </class>
    </implicitCollections>
</xstream>
```
:::
::::

::: paragraph
After:
:::

:::: listingblock
::: content
``` highlight
<xstream id="xstream-1" mode="NO_REFERENCES"
    permissions="-org.apache.camel.dataformat.xstream.*,org.apache.camel.dataformat.xstream.PurchaseHistory,org.apache.camel.dataformat.xstream.PurchaseOrder">
    <converters key="purchase-converter" value="org.apache.camel.dataformat.xstream.XStreamConfigurationTest$PurchaseOrderConverter"/>
    <aliases key="purchase-order" value="org.apache.camel.dataformat.xstream.PurchaseOrder"/>
    <implicitCollections key="org.apache.camel.dataformat.xstream.PurchaseHistory" value="history"/>
</xstream>
```
:::
::::

::: paragraph
Multiple values for `implicitCollections` and `omitFields` can be
separated by comma
:::

::: paragraph
For example:
:::

:::: listingblock
::: content
``` highlight
<implicitCollections key="org.apache.camel.dataformat.xstream.PurchaseHistory" value="history,adress"/>
```
:::
::::
::::::::::::::

:::: sect2
### camel-weather {#_camel_weather}

::: paragraph
This component has been upgraded from using Apache Http Client 3.x to
4.x and is therefore not fully backwards compatible. Some options for
configurer and setting proxy is removed. You can however configure this
directly on a custom `HttpClient` instance and set this on the
`WeatherComponent` to use.
:::
::::

:::::::: sect2
### Endpoint URIs without context path {#_endpoint_uris_without_context_path}

::: paragraph
Previously Camel components may work by referring to their name only
without a colon and context path (eg `log`) that for a few components
would allow them to create an endpoint anyway.
:::

::: paragraph
Now this is not allowed and Camel will throw an
`NoSuchEndpointException`.
:::

::: paragraph
An endpoint by its logical id can still be referred by the id only, eg
:::

:::: listingblock
::: content
``` highlight
Endpoint endpoint = camelContext.getEndpoint("myCoolEndpoint");
```
:::
::::
::::::::

::::::::: sect2
### Error handling {#_error_handling}

::: paragraph
The context scope error handling has been modified a bit. The processors
in those `onException` and `onCompletion` are not shared between routes
anymore. This should have little effect in most cases. If there is a
need to have a single set of processors involved (such as when using a
loadbalancer or other stateful patterns), then an intermediary route
needs to be used. The following excerpt:
:::

:::: listingblock
::: content
``` highlight
onException(Exception.class).handled(true)
    .loadBalance().roundRobin().id("round")
    .to("mock:error", "mock:error2", "mock:error3");
```
:::
::::

::: {.olist .lowerroman}
i.  needs to be rewritten as:
:::

:::: listingblock
::: content
``` highlight
onException(Exception.class).handled(true).to("direct:error");

from("direct:error").loadBalance().roundRobin().id("round")
    .to("mock:error", "mock:error2", "mock:error3");
```
:::
::::
:::::::::

:::: sect2
### camel-cluster {#_camel_cluster}

::: paragraph
The base support for cluster in `org.apache.camel.cluster` has been
moved out of `camel-core-engine` into separate JAR named
`camel-cluster`.
:::
::::

::::::::::::: sect2
### Configuring milli seconds {#_configuring_milli_seconds}

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | If upgrading to Camel 3.4 or      |
| Note                              | newer then do NOT do this because |
| :::                               | Camel 3.4 adds back support for   |
|                                   | this.                             |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
Camel was using a type converter from `String` → `long` that accepted a
time pattern which allowed to configure long values such as `2s` for 2
seconds, e.g. `2000`. And more complex such as `8h15m` for 8 hours and
15 minutes.
:::

::: paragraph
However as this was implemented as part of `String` → `long` type
conversion which then adds a little bit of overhead during routing, when
converting from String to plain numbers.
:::

::: paragraph
To make Camel routing engine as fast as possible this feature has been
removed.
:::

::: paragraph
For example a timer with a 5 second period
:::

:::: listingblock
::: content
``` highlight
from("timer:foo?period=5s")
```
:::
::::

::: paragraph
Should now be specified as numeric only:
:::

:::: listingblock
::: content
``` highlight
from("timer:foo?period=5000")
```
:::
::::
:::::::::::::

:::: sect2
### Main in camel-spring {#_main_in_camel_spring}

::: paragraph
The `org.apache.camel.spring.Main` class has been moved out of
`camel-spring` JAR into its own JAR named `camel-spring-main`.
:::
::::

::::: sect2
### Main in camel-test-blueprint {#_main_in_camel_test_blueprint}

::: paragraph
The `org.apache.camel.test.blueprint.Main` class has been renamed to
`org.apache.camel.test.blueprint.Main` and moved into its own
`camel-test-blueprint` JAR.
:::

::: paragraph
To use the camel-maven-plugin goal `camel:run` with OSGi plugin, you now
need to add the following dependency to the classpath
`org.apache.camel.karaf:camel-blueprint-main`.
:::
:::::

::::::::::::::: sect2
### API changes {#_api_changes}

:::: sect3
#### DefaultComponent {#_defaultcomponent}

::: paragraph
The deprecated method `preProcessUri` has been removed.
:::
::::

:::: sect3
#### CamelContext {#_camelcontext}

::: paragraph
The method `getEndpoint` now throws `NoSuchEndpointException` directly
instead of being wrapped within an `FailedToResolveEndpoint`.
:::
::::

:::: sect3
#### JavaUuidGenerator {#_javauuidgenerator}

::: paragraph
The `org.apache.camel.impl.engine.JavaUuidGenerator` class has been
removed. Its a very slow UUID generator and its not recommended to be
used.
:::
::::

:::: sect3
#### PropertiesComponent {#_propertiescomponent}

::: paragraph
The `org.apache.camel.component.properties.PropertiesFunction` has been
moved to `org.apache.camel.spi.PropertiesFunction` and its now possible
to add custom functions on the
`org.apache.camel.spi.PropertiesComponent` interface.
:::
::::

:::::: sect3
#### JMX Connector configuration removed {#_jmx_connector_configuration_removed}

::: paragraph
The JMX Connector configuration in camel-management has been
[removed](https://issues.apache.org/jira/browse/CAMEL-14811) in Camel
3.2.0. If you want to support the ability to use JMX with Camel
remotely, then just use the default JVM JMX remote capabilities. For
example, use the following (insecure) JVM settings to be able to manage
Camel remotely on localhost, port 9913:
:::

:::: listingblock
::: content
``` highlight
-Dcom.sun.management.jmxremote.port=9913
-Dcom.sun.management.jmxremote.authenticate=false
-Dcom.sun.management.jmxremote.ssl=false
-Djava.rmi.server.hostname=localhost
```
:::
::::
::::::
:::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
