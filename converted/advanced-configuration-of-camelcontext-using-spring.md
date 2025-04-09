::: {#header}
# Camel Spring XML Auto Configuration
:::

::::::::::::: {#content}
::::::: {#preamble}
:::::: sectionbody
::: paragraph
This is only applicable when using Spring XML files with the
`camel-spring-xml` JAR.
:::

::: paragraph
A spring XML file is the XML files that uses `<beans>` as root tag and
have an embedded `<camelContext>`. This is the classic way of using XML
DSL with Apache Camel. That was implemented before Spring Boot.
:::

::: paragraph
If you use Camel on Spring Boot, then look at [Camel Context Auto
Configuration](camelcontext-autoconfigure.html) instead.
:::
::::::
:::::::

::::::: sect1
## Autoconfiguration of Optional Services {#_autoconfiguration_of_optional_services}

:::::: sectionbody
::: paragraph
Camel will configure these functions by doing a lookup in the Spring
bean registry to find beans of the given type.
:::

::: paragraph
The following list all requires at most one bean defined. If there is
more than one bean of this type, then Camel will **not** use it.
:::

+-----------------------+----------------------+----------------------+
| Type                  | Number of beans      | Description          |
+=======================+======================+======================+
| `AsyncP               | 0..1                 | To use a third-party |
| rocessorAwaitManager` |                      | async process await  |
|                       |                      | manager.             |
+-----------------------+----------------------+----------------------+
| `BacklogTracer`       | 0..1                 | To use a third-party |
|                       |                      | [BacklogTracer](b    |
|                       |                      | acklog-tracer.html). |
+-----------------------+----------------------+----------------------+
| `ClassResolver`       | 0..1                 | To use a third-party |
|                       |                      | class resolver. More |
|                       |                      | details at           |
|                       |                      | [Pluggable Class     |
|                       |                      | Res                  |
|                       |                      | olvers](pluggable-cl |
|                       |                      | ass-resolvers.html). |
+-----------------------+----------------------+----------------------+
| `Debugger`            | 0..1                 | To use a             |
|                       |                      | [Debu                |
|                       |                      | gger](debugger.html) |
|                       |                      | usually for tooling. |
+-----------------------+----------------------+----------------------+
| `Delayer`             | 0..1                 | To use a third-party |
|                       |                      | [D                   |
|                       |                      | elayer](components:e |
|                       |                      | ips:delay-eip.html). |
+-----------------------+----------------------+----------------------+
| `EventFactory`        | 0..1                 | To use a third-party |
|                       |                      | event factory.       |
+-----------------------+----------------------+----------------------+
| `Ex                   | 0..1                 | To use a third-party |
| ecutorServiceManager` |                      | executor service     |
|                       |                      | manager. More        |
|                       |                      | details at           |
|                       |                      | [Threading           |
|                       |                      | Model](th            |
|                       |                      | reading-model.html). |
+-----------------------+----------------------+----------------------+
| `Exe                  | 0..1                 | To use a third-party |
| cutorServiceStrategy` |                      | executor service     |
|                       |                      | strategy. More       |
|                       |                      | details at           |
|                       |                      | [Threading           |
|                       |                      | Model](th            |
|                       |                      | reading-model.html). |
+-----------------------+----------------------+----------------------+
| `F                    | 0..1                 | To use a third-party |
| actoryFinderResolver` |                      | factory finder.      |
+-----------------------+----------------------+----------------------+
| `HeadersMapFactory`   | 0..1                 | To use a third-party |
|                       |                      | HeadersMapFactory    |
|                       |                      | implementation.      |
+-----------------------+----------------------+----------------------+
| `HealthCheckRegistry` | 0..1                 | To use a third-party |
|                       |                      | [HealthCheckRegistry |
|                       |                      | ](health-check.html) |
|                       |                      | implementation.      |
+-----------------------+----------------------+----------------------+
| `InflightRepository`  | 0..1                 | To use a third-party |
|                       |                      | in flight            |
|                       |                      | repository.          |
+-----------------------+----------------------+----------------------+
| `Logger`              | 0..1                 | To use provided      |
|                       |                      | org.slf4j.Logger for |
|                       |                      | [Log](components:    |
|                       |                      | :log-component.html) |
|                       |                      | component and [log() |
|                       |                      | EIP](components      |
|                       |                      | :eips:log-eip.html). |
+-----------------------+----------------------+----------------------+
| `Manageme             | 0..1                 | To use a third-party |
| ntObjectNameStrategy` |                      | strategy for naming  |
|                       |                      | `MBeans` for         |
|                       |                      | [ma                  |
|                       |                      | nagement](jmx.html). |
+-----------------------+----------------------+----------------------+
| `ManagementStrategy`  | 0..1                 | To use a third-party |
|                       |                      | strategy for         |
|                       |                      | [ma                  |
|                       |                      | nagement](jmx.html), |
|                       |                      | for example, JMX     |
|                       |                      | management.          |
+-----------------------+----------------------+----------------------+
| `M                    | 0..1                 | To use a third-party |
| essageHistoryFactory` |                      | M                    |
|                       |                      | essageHistoryFactory |
|                       |                      | implementation.      |
+-----------------------+----------------------+----------------------+
| `Mod                  | 0..1                 | To use a third-party |
| elJAXBContextFactory` |                      | model JAXB           |
|                       |                      | ContextFactory       |
+-----------------------+----------------------+----------------------+
| `NodeIdFactory`       | 0..1                 | To use a third-party |
|                       |                      | node id factory.     |
+-----------------------+----------------------+----------------------+
| `Pack                 | 0..1                 | To use a third-party |
| ageScanClassResolver` |                      | package scan         |
|                       |                      | resolver. More       |
|                       |                      | details at           |
|                       |                      | [Pluggable Class     |
|                       |                      | Res                  |
|                       |                      | olvers](pluggable-cl |
|                       |                      | ass-resolvers.html). |
+-----------------------+----------------------+----------------------+
| `ProcessorFactory`    | 0..1                 | To use a third-party |
|                       |                      | processor factory.   |
+-----------------------+----------------------+----------------------+
| `Registry`            | 0..1                 | To use a third-party |
|                       |                      | bean registry. By    |
|                       |                      | default, Camel will  |
|                       |                      | use Spring           |
|                       |                      | ApplicationContext   |
|                       |                      | (when using Spring)  |
|                       |                      | as registry.         |
+-----------------------+----------------------+----------------------+
| `Run                  | 0..1                 | To use a third-party |
| timeEndpointRegistry` |                      | `Runt                |
|                       |                      | imeEndpointRegistry` |
|                       |                      | implementation.      |
+-----------------------+----------------------+----------------------+
| `Run                  | 0..1                 | To use a third-party |
| timeEndpointRegistry` |                      | `Runt                |
|                       |                      | imeEndpointRegistry` |
|                       |                      | implementation.      |
+-----------------------+----------------------+----------------------+
| `ShutdownStrategy`    | 0..1                 | To use a third-party |
|                       |                      | shutdown strategy.   |
+-----------------------+----------------------+----------------------+
| `S                    | 0..1                 | To use a third-party |
| treamCachingStrategy` |                      | [Stream              |
|                       |                      | caching](            |
|                       |                      | stream-caching.html) |
|                       |                      | strategy.            |
+-----------------------+----------------------+----------------------+
| `ThreadPoolFactory`   | 0..1                 | To use a third-party |
|                       |                      | thread pool factory. |
|                       |                      | More details at      |
|                       |                      | [Threading           |
|                       |                      | Model](th            |
|                       |                      | reading-model.html). |
+-----------------------+----------------------+----------------------+
| `TraceFormatter`      | 0..1                 | To use a bean that   |
|                       |                      | has the tracing      |
|                       |                      | options configured.  |
+-----------------------+----------------------+----------------------+
| `` Tracer` ``         | 0..1                 | To use a third-party |
|                       |                      | [T                   |
|                       |                      | racer](tracer.html). |
+-----------------------+----------------------+----------------------+
| ``                    | 0..1                 | To use third-party   |
| UnitOfWorkFactory` `` |                      | `UnitOfWork`         |
|                       |                      | implementations      |
|                       |                      | created by the       |
|                       |                      | factory.             |
+-----------------------+----------------------+----------------------+
| `` UuidGenerator` ``  | 0..1                 | To use a third-party |
|                       |                      | [UuidGenerator](     |
|                       |                      | uuidgenerator.html). |
+-----------------------+----------------------+----------------------+

::: paragraph
And the following options have support for any number of beans defined.
:::

+-----------------------+----------------------+----------------------+
| Type                  | Number of beans      | Description          |
+=======================+======================+======================+
| `CamelClusterService` | 0..n                 | To detect            |
|                       |                      | [Clusteri            |
|                       |                      | ng](clustering.html) |
|                       |                      | services.            |
+-----------------------+----------------------+----------------------+
| `EndpointStrategy`    | 0..n                 | To use third-party   |
|                       |                      | endpoint strategies. |
+-----------------------+----------------------+----------------------+
| `EventNotifier`       | 0..n                 | To use third-party   |
|                       |                      | event notifiers.     |
+-----------------------+----------------------+----------------------+
| `H                    | 0..n                 | To use Camel [Health |
| ealthCheckRepository` |                      | Check                |
|                       |                      | ](health-check.html) |
|                       |                      | repositories.        |
+-----------------------+----------------------+----------------------+
| `InterceptStrategy`   | 0..n                 | To use your own      |
|                       |                      | [Interc              |
|                       |                      | ept](components:eips |
|                       |                      | :intercept.html)that |
|                       |                      | intercepts every     |
|                       |                      | processing step in   |
|                       |                      | all routes in the    |
|                       |                      | [CamelContext]       |
|                       |                      | (camelcontext.html). |
|                       |                      | For instance, you    |
|                       |                      | can use this to do   |
|                       |                      | an AOP like          |
|                       |                      | performance timer    |
|                       |                      | interceptor.         |
+-----------------------+----------------------+----------------------+
| `LifecycleStrategy`   | 0..n                 | To use third-party   |
|                       |                      | lifecycle            |
|                       |                      | strategies.          |
+-----------------------+----------------------+----------------------+
| `LogListener`         | 0..n                 | To use custom        |
|                       |                      | `LogListener`        |
|                       |                      | implementations.     |
+-----------------------+----------------------+----------------------+
| `MainListener`        | 0..n                 | To use custom        |
|                       |                      | `MainListener`       |
|                       |                      | implementations.     |
+-----------------------+----------------------+----------------------+
| `Mo                   | 0..n                 | To use third-party   |
| delLifecycleStrategy` |                      | model lifecycle      |
|                       |                      | strategies.          |
+-----------------------+----------------------+----------------------+
| `RoutePolicyFactory`  | 0..n                 | To use a third-party |
|                       |                      | route policy factory |
|                       |                      | to create a route    |
|                       |                      | policy for every     |
|                       |                      | route.               |
+-----------------------+----------------------+----------------------+
| `ServiceRegistry`     | 0..n                 | To use camel-cloud   |
|                       |                      | [Service             |
|                       |                      | Registries](ser      |
|                       |                      | vice-registry.html). |
+-----------------------+----------------------+----------------------+
::::::
:::::::
:::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
