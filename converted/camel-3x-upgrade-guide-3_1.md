::: {#header}
# Apache Camel 3.x Upgrade Guide
:::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
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

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Upgrading Camel 3.0 to 3.1 {#_upgrading_camel_3_0_to_3_1}

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
:::: sect2
### camel-ahc, camel-netty-http, camel-undertow {#_camel_ahc_camel_netty_http_camel_undertow}

::: paragraph
These Camel components now no longer have dependency on Javax Servlet.
:::
::::

:::: sect2
### camel-undertow {#_camel_undertow}

::: paragraph
The exception class `HttpOperationFailedException` is now from package
`org.apache.camel.http.base` instead of `org.apache.camel.http.common`.
The Camel undertow producer throws this exception.
:::
::::

:::::: sect2
### camel-bean {#_camel_bean}

::: paragraph
The bean component has been changed to behave as singleton scoped by
default. This means that the bean is created or looked up once and
reused.
:::

::: paragraph
The option `cache` has been deprecated in favour of the new `scope`
option that by default is `Singleton`. You can set this to `Prototype`
to use the old behaviour.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | ::: paragraph                     |
| Note                              | Setting this to Prototype will    |
| :::                               | let Camel create/lookup a new     |
|                                   | bean instance, per use; which     |
|                                   | acts as prototype scoped. However |
|                                   | beware that if you lookup the     |
|                                   | bean, then the registry that      |
|                                   | holds the bean, would return a    |
|                                   | bean accordingly to its           |
|                                   | configuration, which can be       |
|                                   | singleton or prototype scoped.    |
|                                   | For example if you use Spring, or |
|                                   | CDI, which has their own settings |
|                                   | for setting bean scopes.          |
|                                   | :::                               |
+-----------------------------------+-----------------------------------+
:::
::::::

::::::::::: sect2
### camel-etcd {#_camel_etcd}

::: paragraph
The `camel-etcd` component has changed its endpoint syntax from
`etcd:action/path` to `etcd-keys:path`, `etcd-stats:path`, or
`etcd-watch:path`.
:::

::: paragraph
For example before
:::

:::: listingblock
::: content
``` highlight
etcd:stats/leader
```
:::
::::

::: paragraph
Should be changed to
:::

:::: listingblock
::: content
    etcd-stats:leader
:::
::::

::: paragraph
This change was needed as the 3 actions could not share the same
component/endpoint and had to be separated.
:::
:::::::::::

::::: sect2
### camel-ftp {#_camel_ftp}

:::: literalblock
::: content
    The stepwise functionality (stepwise=true) is not supported for stream download (treamDownload=true).
:::
::::
:::::

:::::: sect2
### camel-irc {#_camel_irc}

::: paragraph
The `camel-irc` component has changed its endpoint syntax and removed
option #room as a part of the url path. Allowed syntax is:
:::

:::: listingblock
::: content
``` highlight
irc:nick@host[:port]?[options]
```
:::
::::
::::::

:::::::::: sect2
### camel-milo {#_camel_milo}

::: paragraph
The `camel-milo` client component has changed its endpoint syntax from
`milo-client:tcp` to `milo-client:opc.tcp`. For example before
:::

:::: listingblock
::: content
``` highlight
milo-client:tcp://foo:bar@localhost:1234
```
:::
::::

::: paragraph
Should be changed to
:::

:::: listingblock
::: content
    milo-client:opc.tcp://foo:bar@localhost:1234
:::
::::

::: paragraph
The `camel-milo` server component requires Java 9 at runtime. Property
`strictEndpointUrlsEnabled` is no longer supported.
Properties\`hostName\` and `serverName` are replaced by `path`. To
successfully use certificates for secured communication, JCE
Jurisdiction Policy File Default has to be **Unlimited** (which is by
default since Java 9+).
:::
::::::::::

:::::::::: sect2
### camel-nats {#_camel_nats}

::: paragraph
The `camel-nats` component has changed its endpoint syntax from
`nats:servers` to `nats:topic`. For example before
:::

:::: listingblock
::: content
``` highlight
nats:myserver:4222?topic=myTopic
```
:::
::::

::: paragraph
Should be changed to
:::

:::: listingblock
::: content
    nats:myTopic?servers=myserver:4222
:::
::::

::: paragraph
This change is motivated by allowing to configure servers on component
level, and also for Spring Boot auto-configuration etc.
:::
::::::::::

:::::::::: sect2
### camel-nsq {#_camel_nsq}

::: paragraph
The `camel-nsq` component has changed its endpoint syntax from
`nsq:servers` to `nsq:topic`. For example before
:::

:::: listingblock
::: content
``` highlight
nsq:myserver:4161/myTopic
```
:::
::::

::: paragraph
Should be changed to
:::

:::: listingblock
::: content
    nsq:myTopic?servers=myserver:4161
:::
::::

::: paragraph
This change is motivated by allowing to configure servers on component
level, and also for Spring Boot auto-configuration etc.
:::
::::::::::

:::::::::: sect2
### camel-ipfs {#_camel_ipfs}

::: paragraph
The `camel-ipfs` component has changed its endpoint syntax from
`nsq:host:port/command` to `ipfs:command`. The host and port is now
configured on the component level instead.
:::

::: paragraph
For example before
:::

:::: listingblock
::: content
``` highlight
ipfs:127.0.0.1:5001/add
```
:::
::::

::: paragraph
Should be changed to
:::

:::: listingblock
::: content
    ipfs:add
:::
::::
::::::::::

::::::::::: sect2
### camel-xmlsecurity {#_camel_xmlsecurity}

::: paragraph
The `camel-xmlsecurity` component has changed its endpoint syntax from
`xmlsecurity:command/name` to `xmlsecurity-sign:name`,
`xmlsecurity-verify:name`.
:::

::: paragraph
For example before
:::

:::: listingblock
::: content
``` highlight
xmlsecurity:verify/foo
```
:::
::::

::: paragraph
Should be changed to
:::

:::: listingblock
::: content
    xmlsecurity-verify:foo
:::
::::

::: paragraph
This change was needed as the 2 commands could not share the same
component/endpoint and had to be separated.
:::
:::::::::::

:::: sect2
### spi-annotations {#_spi_annotations}

::: paragraph
The `spi-annotations` JAR is mandatory but was mistakenly defined as
optional scope. The content of this JAR is now embedded directly into
`camel-api` JAR so end users does not have to include or depend on
`spi-annotations` JAR anymore.
:::
::::

:::::: sect2
### camel-core-engine and camel-jaxp {#_camel_core_engine_and_camel_jaxp}

::: paragraph
XML and JAXB has been moved out of camel-base and camel-core-engine.
:::

::: paragraph
The module camel-jaxp has been renamed to camel-xml-jaxp.
:::

::: paragraph
The camel-xml-jaxp JAR has XML parsers and type converters. The
camel-xml-jaxb has support for loading XML DSL routes using JAXB. An
alternative is to use the new camel-xml-io for loading XML routes which
is more light-weight and faster than JAXB.
:::
::::::

::::::: sect2
### JAXB is now optional {#_jaxb_is_now_optional}

::: paragraph
JAXB is now optional in Camel and only needed when using XML routes with
the `camel-xml-jaxb` JAR for loading and parsing the routes with JAXB.
There is an alternative implementation with `camel-xml-io` (see above).
:::

::: paragraph
This means that `jaxb-core` and `jaxb-impl` JARs no longer are needed on
the classpath and as such has been removed as dependency in the various
Camel `pom.xml` files.
:::

::: paragraph
There are a number of components that uses JAXB such as `camel-spring`,
`camel-blueprint`, `camel-cdi` for their support of using XML for beans
and Camel XML routes. And a few components such as `camel-soap` etc.
:::

::: paragraph
But at general then Camel is now lighter in classpath dependency by not
requiring to have JAXB present.
:::
:::::::

:::::::::::: sect2
### Package scanning \@TypeConverter {#_package_scanning_typeconverter}

::: paragraph
Camel has now been configured to not package scan for custom
`@Converter` classes on startup. Type converters are now loaded and
registered in faster way via source code generated loader classes by
having `@Converter(loader = true)` specified. If you have custom
converters and have not migrated to use source code generated loaders,
you can enable package scanning by setting
:::

:::: listingblock
::: content
``` highlight
camelContext.setLoadTypeConverters(true);
```
:::
::::

::: paragraph
And in XML:
:::

:::: listingblock
::: content
``` highlight
<camelContext loadTypeConverters="true">
...
</camelContext>
```
:::
::::

::: paragraph
And in Spring Boot `application.properties`:
:::

:::: listingblock
::: content
``` highlight
camel.loadTypeConverters=true
```
:::
::::
::::::::::::

:::: sect2
### Graceful Shutdown Timeout {#_graceful_shutdown_timeout}

::: paragraph
When shutting down Camel, then the default timeout has changed from 300
seconds (5 minutes) to 45 seconds. The 45 seconds was chosen as 30
seconds is a common timeout to use for remote protocols, so we wanted to
give Camel a bit more time, and hence added 15 seconds so the default is
45 seconds.
:::
::::

:::: sect2
### Message History {#_message_history}

::: paragraph
The message history is now default disabled (due to optimize core for
lower footprint out of the box). See the [Message
History](components:eips:message-history.html) documentation for how to
enabled message history.
:::
::::

::::::::: sect2
### Inflight Repository {#_inflight_repository}

::: paragraph
The inflight repository now does no longer allow browsing each
individual exchange (due to optimize core for lower footprint out of the
box). To enable browsing then you can turn this on via:
:::

:::: listingblock
::: content
``` highlight
    context.getInflightRepository().setInflightBrowseEnabled(true);
```
:::
::::

::: paragraph
And in XML DSL:
:::

:::: listingblock
::: content
``` highlight
<camelContext inflightRepositoryBrowseEnabled="true">

</camelContext>
```
:::
::::
:::::::::

:::: sect2
### Component Extension Verifier {#_component_extension_verifier}

::: paragraph
When using component verifier
(`org.apache.camel.component.extension.ComponentVerifierExtension`) then
you would know need to add `camel-core-catalog` to the classpath to make
this useable. If the JAR is missing, there will be an exception stating
that `RuntimeCamelCatalog` is not found and that this JAR should be
added.
:::
::::

:::: sect2
### ManagedRuntimeCatalog {#_managedruntimecatalog}

::: paragraph
The `ManagedRuntimeCatalog` JMX MBean is removed and no longer
available.
:::
::::

::::::: sect2
### Spring Boot JMX {#_spring_boot_jmx}

::: paragraph
The `camel-management` dependency of `camel-spring-boot` was removed as
Spring Boot 2.2+ disables JMX by default.
:::

::: paragraph
To continue using JMX with Camel Spring Boot add the following
dependency:
:::

:::: listingblock
::: content
``` highlight
<dependency>
  <groupId>org.apache.camel</groupId>
  <artifactId>camel-management</artifactId>
</dependency>
```
:::
::::
:::::::

:::::::::: sect2
### Custom components {#_custom_components}

::: paragraph
Camel now uses Camel Package Maven Plugin instead of `camel-apt` APT
compiler to generate component meta data.
:::

::: paragraph
Custom components should then change in the pom.xml from:
:::

:::: listingblock
::: content
``` highlight
<dependency>
  <groupId>org.apache.camel</groupId>
  <artifactId>apt</artifactId>
  <scope>provided</scope>
</dependency>
```
:::
::::

::: paragraph
To the following:
:::

:::: listingblock
::: content
``` highlight
      <plugin>
        <groupId>org.apache.camel</groupId>
        <artifactId>camel-package-maven-plugin</artifactId>
        <version>${camel-version}</version>
        <executions>
          <execution>
            <id>generate</id>
            <goals>
              <goal>generate-component</goal>
            </goals>
            <phase>process-classes</phase>
          </execution>
        </executions>
      </plugin>
      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>build-helper-maven-plugin</artifactId>
        <executions>
          <execution>
            <phase>initialize</phase>
            <goals>
              <goal>add-source</goal>
              <goal>add-resource</goal>
            </goals>
            <configuration>
              <sources>
                <source>src/generated/java</source>
              </sources>
              <resources>
                <resource>
                  <directory>src/generated/resources</directory>
                </resource>
              </resources>
            </configuration>
          </execution>
        </executions>
      </plugin>
      <!--
      Recompile source after generated loader classes have been created.
      The maven-compiler-plugin must be defined in the POM after the
      camel-package-maven-plugin so recompile runs after generated
      sources have been created.

      Adjust configuration for the JDK version your project uses.
      -->
      <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.8.1</version>
          <configuration>
              <source>1.11</source>
              <target>1.11</target>
          </configuration>
          <executions>
              <execution>
                  <id>recompile</id>
                  <goals>
                      <goal>compile</goal>
                  </goals>
                  <phase>process-classes</phase>
              </execution>
          </executions>
      </plugin>
```
:::
::::
::::::::::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect2
### API changes {#_api_changes}

:::::::::: sect3
#### log changed to private static LOG {#_log_changed_to_private_static_log}

::: paragraph
The `ServiceSupport` class has changed its logging from instance to
static, which means any inherited class that uses `log` would need to
change the code to compile. This may happen in custom Camel components.
:::

::: paragraph
Before you may have:
:::

:::: listingblock
::: content
``` highlight
    log.debug("Sending message to foobar service: {}", messageId);
```
:::
::::

::: paragraph
You then need to migrate the logging to also be static:
:::

:::: listingblock
::: content
``` highlight
    private static final Logger LOG = LoggerFactory.getLogger(FooBarProducer.class);

    LOG.debug("Sending message to foobar service: {}", messageId);
```
:::
::::
::::::::::

::::::::: sect3
#### Exchange {#_exchange}

::: paragraph
The `Exchange` API has been modified slightly as part of an optimization
effort. The return value of `getCreated` was changed from
`java.util.Date` to `long` which is the time millis. The
`Exchange.CREATED_TIMESTAMP` is no longer stored as exchange property,
but you should use the `getCreated` method on `Exchange`. The return
value of `isExternalRedelivered` was changed from `Boolean` to
`boolean`.
:::

::: paragraph
Some of the advanced and API for component developers on `Exchange` has
been moved to an extended interface `ExtendedExchange`. The following
methods have been moved:
:::

::: ulist
- setFromEndpoint

- setFromRouteId

- setUnitOfWork

- addOnCompletion

- containsOnCompletion

- handoverCompletions
:::

::: paragraph
You can use these methods by adapting to the extended exchange as shown
below:
:::

:::: listingblock
::: content
``` highlight
exchange.adapt(ExtendedExchange.class).addOnCompletion(...);
```
:::
::::
:::::::::

:::: sect3
#### Message {#_message}

::: paragraph
The message ID will now default to use the same id as Exchange ID as
messages are associated with the exchange and using different IDs does
not offer much value. Another reason is to optimize for performance to
avoid generating new IDs. A few Camel components do provide their own
message IDs such as the JMS components.
:::
::::

::::: sect3
#### UnitOfWork {#_unitofwork}

::: paragraph
Advanced Camel users whom implement a custom `UnitOfWork` should
implement the new \`isBeforeAfterProcess()\' method and return true or
false, whether Camel should invoke the before and after processor
methods.
:::

::: paragraph
The method `getId` has been removed.
:::
:::::

:::: sect3
#### Cookies {#_cookies}

::: paragraph
Cookies from `camel-http-common` has been moved into a new
`camel-http-base` JAR. The package `org.apache.camel.http.common.cookie`
is renamed to `org.apache.camel.http.base.cookie`.
:::
::::

::::::::: sect3
#### Exchange.ROUTE_STOP {#_exchange_route_stop}

::: paragraph
To signal an `Exchange` to stop continue routing has changed from
setting the exchange property `Exchange.ROUTE_STOP` to true. Instead you
should now use the `setRouteStop` method on the `Exchange` API.
:::

:::: listingblock
::: content
``` highlight
    exchange.setProperty(Exchange.ROUTE_STOP, Boolean.TRUE);
```
:::
::::

::: paragraph
Should now be:
:::

:::: listingblock
::: content
``` highlight
    exchange.setRouteStop(true);
```
:::
::::
:::::::::

::::::::: sect3
#### Exchange.ROLLBACK_ONLY and Exchange.ROLLBACK_ONLY_LAST {#_exchange_rollback_only_and_exchange_rollback_only_last}

::: paragraph
To signal an `Exchange` to rollback a transaction has changed from
setting the exchange property `Exchange.ROLLBACK_ONLY` to true. Instead
you should now use the `setRollbackOnly` method on the `Exchange` API
(the same for rollback only last).
:::

:::: listingblock
::: content
``` highlight
    exchange.setProperty(Exchange.ROLLBACK_ONLY, Boolean.TRUE);
```
:::
::::

::: paragraph
Should now be:
:::

:::: listingblock
::: content
``` highlight
    exchange.setRollbackOnly(true);
```
:::
::::
:::::::::

::::::::: sect3
#### Exchange.ERRORHANDLER_HANDLED {#_exchange_errorhandler_handled}

::: paragraph
The exchange property `Exchange.ERRORHANDLER_HANDLED` was used to
indicate that the error handling mechanism for a given exchange had
completed. This property sometimes had to be conveyed by aggregation
strategies, so instead of
:::

:::: listingblock
::: content
``` highlight
    oldExchange.getProperties().put(
            Exchange.ERRORHANDLER_HANDLED,
            newExchange.getProperties().get(Exchange.ERRORHANDLER_HANDLED));
```
:::
::::

::: paragraph
one should now use:
:::

:::: listingblock
::: content
``` highlight
    Boolean handled = newExchange.adapt(ExtendedExchange.class)
            .getErrorHandlerHandled();
    oldExchange.adapt(ExtendedExchange.class)
            .setErrorHandlerHandled(handled);
```
:::
::::
:::::::::

:::: sect3
#### ModelHelper removed {#_modelhelper_removed}

::: paragraph
The class `org.apache.camel.model.ModelHelper` has been removed. Instead
you can use its functionality from `ExtendedCamelContext` by the
`getModelToXMLDumper` and `getXMLRoutesDefinitionLoader` methods which
has APIs similar to `ModelHelper`.
:::
::::

:::: sect3
#### JsonSchemaHelper removed {#_jsonschemahelper_removed}

::: paragraph
The class `org.apache.camel.tooling.util.JSonSchemaHelper` has been
removed. Instead you can use utils coming from camel-util-json and the
class `org.apache.camel.tooling.util.PackageHelper`
:::
::::

:::: sect3
#### camel-xml-jaxp {#_camel_xml_jaxp}

::: paragraph
The class
`org.apache.camel.processor.validation.PredicateValidatingProcessor` has
moved from `camel-xml-jaxp` JAR to `camel-support` JAR and renamed to
`org.apache.camel.support.processor.PredicateValidatingProcessor`.
:::
::::

:::::: sect3
#### Java DSL {#_java_dsl}

::: paragraph
The Java DSL has been revisited and the following methods have been
removed:
:::

::: ulist
- ExpressionClause::body(Supplier\<Object\>)

- MulticastDefinition::onPrepare(Supplier\<Processor\>)

- ProcessorDefinition::process(Supplier\<Processor\>)

- ProcessorDefinition::setBody(Supplier\<Result\>)

- RecipientListDefinition::onPrepare(Supplier\<Processor\>)

- SplitDefinition::onPrepare(Supplier\<Processor\>)

- WireTapDefinition::newExchange(Supplier\<Processor\>)

- WireTapDefinition::onPrepare(Supplier\<Processor\>)
:::

::: paragraph
This change is motivated by the need to remove method ambiguity for
untyped languages such as Groovy and JavaScript, for more info see
[https://issues.apache.org/jira/browse/CAMEL-14300](https://issues.apache.org/jira/browse/CAMEL-14300){.bare}
:::
::::::

::::: sect3
#### CamelContext {#_camelcontext}

::: paragraph
Some unused methods have been removed from `CamelContext` which were not
part of the public API. The following methods have been removed:
:::

::: ulist
- getProducerServicePool

- setProducerServicePool

- getPollingConsumerServicePool

- setPollingConsumerServicePool
:::
:::::

:::: sect3
#### Internal API changes {#_internal_api_changes}

::: paragraph
Removed the method `getProcessors` from `Pipeline` as you should use the
`next` method instead to access a read-only view of the processors.
:::
::::

:::: sect3
#### \@Experimental {#_experimental}

::: paragraph
The `@Experimental` annotation is moved from `meta-annotations` JAR to
`camel-api` and moved from package `org.apache.camel.meta` to
`org.apache.camel`. And the meta-annotations has been removed.
:::
::::

::::::::::::::: sect3
#### Property Placeholders {#_property_placeholders}

::: paragraph
The support for out-of-band property placeholders has been removed. This
means that XML that were using the
[`http://camel.apache.org/schema/placeholder`](http://camel.apache.org/schema/placeholder){.bare}
namespace and that the java builders using the
`.placeholder(key, value).` have to be modified.
:::

:::: listingblock
::: content
``` highlight
    from("direct:start")
        .multicast()
        .placeholder("stopOnException", "stop")
        .to("mock:a")
```
:::
::::

::: paragraph
should be rewritten as:
:::

:::: listingblock
::: content
``` highlight
    from("direct:start")
        .multicast()
        .stopOnException("{{stop}}")
        .to("mock:a")
```
:::
::::

::: paragraph
and
:::

:::: listingblock
::: content
``` highlight
   <route>
        <from uri="direct:start"/>
        <multicast prop:stopOnException="stop">
            <to uri="mock:a"/>
        </multicast>
    </route>
```
:::
::::

::: paragraph
should be rewritten as:
:::

:::: listingblock
::: content
``` highlight
   <route>
        <from uri="direct:start"/>
        <multicast stopOnException="{{stop}}">
            <to uri="mock:a"/>
        </multicast>
    </route>
```
:::
::::
:::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
