::: {#header}
# CamelContext Auto Configuration
:::

::::::::::::::::::: {#content}
::::: {#preamble}
:::: sectionbody
::: paragraph
The [CamelContext](camelcontext.html) is autoconfigured when running
Camel with either [Camel Main](components:others:main.html), Camel
Spring Boot, Camel Quarkus.
:::
::::
:::::

::::::::::::::: sect1
## Camel Autoconfiguration {#_camel_autoconfiguration}

:::::::::::::: sectionbody
::: paragraph
Under these runtimes then the autoconfiguration is performed by shared
code from the `camel-main` JAR to ensure the configuration is similar on
these runtimes.
:::

::: paragraph
The autoconfiguration is executed in several steps:
:::

::: {.olist .arabic}
1.  Configure `CamelContext` (and more such as components) from
    [properties](components::properties-component.html) from external
    sources like `application.properties|yaml`

2.  Configure optional services that have been registered in the
    [Registry](registry.html)
:::

::::: sect2
### Autoconfiguration of Properties {#_autoconfiguration_of_properties}

::: paragraph
This is used for configuring the standard set of more than 100 options
which are listed in the *Camel Main Options* table at [Camel
Main](components:others:main.html).
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | When using Camel on Spring Boot,  |
| Note                              | then these options are prefixed   |
| :::                               | with `camel.springboot`, and not  |
|                                   | `camel.main`.                     |
+-----------------------------------+-----------------------------------+
:::
:::::

::::::: sect2
### Autoconfiguration of Optional Services {#_autoconfiguration_of_optional_services}

::: paragraph
After configuring the standard options, then Camel will look in the
[Registry](registry.html) for custom services to be used. For example,
to plug in a custom `UnitOfWorkFactory`.
:::

::: paragraph
The services can be anything that can be plugged into Camel (typically
services that implement an SPI interface `org.apache.camel.spi`).
:::

::: paragraph
The following SPI services can only a single instance (singleton) be in
the [Registry](registry.html).
:::

+-------------+--------------------------------------------------------+
| SPI         | Description                                            |
+=============+========================================================+
| `Async      | To use a custom async processor await manager          |
| ProcessorAw |                                                        |
| aitManager` |                                                        |
+-------------+--------------------------------------------------------+
| `Bac        | To use a custom backlog tracer                         |
| klogTracer` |                                                        |
+-------------+--------------------------------------------------------+
| `Cla        | To use a custom class resolver. This is only necessary |
| ssResolver` | if you run Camel on a special application server to    |
|             | deal with classloading.                                |
+-------------+--------------------------------------------------------+
| `Debugger`  | To use a custom [debugger](debugger.html)              |
+-------------+--------------------------------------------------------+
| `Ev         | To use a custom event notifier factory                 |
| entFactory` |                                                        |
+-------------+--------------------------------------------------------+
| `Excha      | To use a custom [exchange                              |
| ngeFactory` | factory](exchange-pooling.html)                        |
+-------------+--------------------------------------------------------+
| `E          | To use a custom [thread pool                           |
| xecutorServ | manager](threading-model.html)                         |
| iceManager` |                                                        |
+-------------+--------------------------------------------------------+
| `           | To use a custom factory finder resolver. This is only  |
| FactoryFind | necessary if you run Camel on a special application    |
| erResolver` | server to deal with classloading.                      |
+-------------+--------------------------------------------------------+
| `HealthChe  | To use a custom [health check                          |
| ckRegistry` | registry](health-check.html)                           |
+-------------+--------------------------------------------------------+
| `Inflight   | To use a custom in flight repository                   |
| Repository` |                                                        |
+-------------+--------------------------------------------------------+
| `Managem    | To use a custom JMX MBean object naming                |
| entObjectNa |                                                        |
| meStrategy` |                                                        |
+-------------+--------------------------------------------------------+
| `Manageme   | To use a custom JMX management strategy                |
| ntStrategy` |                                                        |
+-------------+--------------------------------------------------------+
| `           | To use a custom factory for [message                   |
| MessageHist | history](components:eips:message-history.html)         |
| oryFactory` |                                                        |
+-------------+--------------------------------------------------------+
| `Mo         | To use a custom `JAXBContext` factory (only needed if  |
| delJAXBCont | you run Camel on a special application server to deal  |
| extFactory` | with JAXB classloading)                                |
+-------------+--------------------------------------------------------+
| `Nod        | To use a custom factory for creating auto generated    |
| eIdFactory` | node ids                                               |
+-------------+--------------------------------------------------------+
| `Proces     | To use a custom factory for creating                   |
| sorFactory` | [EIP]                                                  |
|             | (components:eips:enterprise-integration-patterns.html) |
|             | processors                                             |
+-------------+--------------------------------------------------------+
| `Propertie  | To use a custom properties component                   |
| sComponent` |                                                        |
+-------------+--------------------------------------------------------+
| `Reacti     | To use a custom reactive engine in the Camel routing   |
| veExecutor` | engine                                                 |
+-------------+--------------------------------------------------------+
| `Route      | To use a custom [route                                 |
| Controller` | controller](route-controller.html)                     |
+-------------+--------------------------------------------------------+
| `Ru         | To use a custom runtime [endpoint](endpoint.html)      |
| ntimeEndpoi | registry                                               |
| ntRegistry` |                                                        |
+-------------+--------------------------------------------------------+
| `Shutdo     | To use a custom [shutdown                              |
| wnStrategy` | strategy](graceful-shutdown.html)                      |
+-------------+--------------------------------------------------------+
| `StartupSt  | To use a custom startup recorder                       |
| epRecorder` |                                                        |
+-------------+--------------------------------------------------------+
| `ThreadP    | To use a custom [thread pool                           |
| oolFactory` | factory](threading-model.html)                         |
+-------------+--------------------------------------------------------+
| `UnitOfW    | To use a custom unit of work factory                   |
| orkFactory` |                                                        |
+-------------+--------------------------------------------------------+
| `Uui        | To use a custom [uuid generator](uuidgenerator.html)   |
| dGenerator` |                                                        |
+-------------+--------------------------------------------------------+

::: paragraph
For the following SPI services, there can be multiple (one or more)
implementations in the [Registry](registry.html).
:::

+-------------+--------------------------------------------------------+
| SPIs        | Description                                            |
+=============+========================================================+
| `CamelClus  | Adds all the custom [camel-cluster                     |
| terService` | services](clustering.html)                             |
+-------------+--------------------------------------------------------+
| `Endpoi     | Adds all the custom [endpoint                          |
| ntStrategy` | strategies](endpoint.html)                             |
+-------------+--------------------------------------------------------+
| `Eve        | Adds all the custom event notifiers                    |
| ntNotifier` |                                                        |
+-------------+--------------------------------------------------------+
| `Gl         | To use a custom supplier for [JSSE (Java               |
| obalSSLCont | Security)](camel-configuration-utilities.html)         |
| extParamete |                                                        |
| rsSupplier` |                                                        |
+-------------+--------------------------------------------------------+
| `Interce    | Adds all the custom intercept strategies               |
| ptStrategy` |                                                        |
+-------------+--------------------------------------------------------+
| `Lifecyc    | Adds all the custom lifecycle strategies               |
| leStrategy` |                                                        |
+-------------+--------------------------------------------------------+
| `L          | Adds all the log listeners                             |
| ogListener` |                                                        |
+-------------+--------------------------------------------------------+
| `M          | Adds all the custom model lifecycle strategies         |
| odelLifecyc |                                                        |
| leStrategy` |                                                        |
+-------------+--------------------------------------------------------+
| `RoutePol   | Adds all the custom [route policy                      |
| icyFactory` | factories](route-policy.html)                          |
+-------------+--------------------------------------------------------+
| `Servi      | Adds all the custom camel-cloud service registries     |
| ceRegistry` |                                                        |
+-------------+--------------------------------------------------------+
| `ThreadP    | Adds all the [thread pool                              |
| oolProfile` | profiles](threading-model.html)                        |
+-------------+--------------------------------------------------------+
| `Type       | Adds all the custom [type                              |
| Converters` | converters](type-converter.html)                       |
+-------------+--------------------------------------------------------+
:::::::
::::::::::::::
:::::::::::::::
:::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
