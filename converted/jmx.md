::: {#header}
# JMX
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
::::: {#preamble}
:::: sectionbody
::: paragraph
Camel has optional support for JMX management, which is part of the
`camel-management` and `camel-management-api` JARs.
:::
::::
:::::

:::::::: sect1
## Using JMX to manage Apache Camel {#_using_jmx_to_manage_apache_camel}

::::::: sectionbody
::: paragraph
The JMX instrumentation agent is enabled in Camel when the
`camel-management` JAR is included in the classpath. Once enabled, the
Camel runtime creates and registers MBean management objects with a
`MBeanServer` instance in the VM. This allows Camel users to instantly
obtain insights into how Camel routes perform down to the individual
processor level.
:::

::: paragraph
The high level supported types of management objects are:
:::

::: ulist
- [CamelContext](https://www.javadoc.io/doc/org.apache.camel/camel-management/current/org/apache/camel/management/mbean/ManagedCamelContext.html)

- [Component](https://www.javadoc.io/doc/org.apache.camel/camel-management/current/org/apache/camel/management/mbean/ManagedComponent.html)

- [Consumer](https://www.javadoc.io/doc/org.apache.camel/camel-management/current/org/apache/camel/management/mbean/ManagedConsumer.html)

- [DataFormat](https://www.javadoc.io/doc/org.apache.camel/camel-management/current/org/apache/camel/management/mbean/ManagedDataFormat.html)

- [Endpoint](https://www.javadoc.io/doc/org.apache.camel/camel-management/current/org/apache/camel/management/mbean/ManagedEndpoint.html)

- [Processor](https://www.javadoc.io/doc/org.apache.camel/camel-management/current/org/apache/camel/management/mbean/ManagedProcessor.html)

- [Route](https://www.javadoc.io/doc/org.apache.camel/camel-management/current/org/apache/camel/management/mbean/ManagedRoute.html)

- [Service](https://www.javadoc.io/doc/org.apache.camel/camel-management/current/org/apache/camel/management/mbean/ManagedService.html)
:::

::: paragraph
Most of these management objects also expose lifecycle operations in
addition to performance counter-attributes.
:::
:::::::
::::::::

:::::::::::::::: sect1
## Disabling or enabling Camel JMX {#_disabling_or_enabling_camel_jmx}

::::::::::::::: sectionbody
::: paragraph
JMX is only enabled in Camel when the `camel-management` JAR is included
on the classpath. To quickly enable or disable JMX, simply add or remove
this JAR from the classpath.
:::

::: paragraph
It is also possible to enable or disable JMX via configuration as
documented below.
:::

::: paragraph
You can disable the JMX instrumentation agent by setting the Java VM
system property as follows:
:::

:::: listingblock
::: content
``` highlight
-Dorg.apache.camel.jmx.disabled=true
```
:::
::::

::: paragraph
The property value is treated as `boolean`.
:::

::: paragraph
Alternatively, add a `jmxAgent` element inside the `camelContext`
element in Spring XML configuration:
:::

:::: listingblock
::: content
``` highlight
<camelContext id="camel" xmlns="http://camel.apache.org/schema/spring">
  <jmxAgent id="agent" disabled="true"/>
    ...
</camelContext>
```
:::
::::

::: paragraph
In Java, you can disable JMX directly on the `CamelContext` as follows:
:::

:::: listingblock
::: content
``` highlight
CamelContext camel = new DefaultCamelContext();
camel.disableJMX();
```
:::
::::
:::::::::::::::
::::::::::::::::

::::::::::::: sect1
## Camel JMX configuration options {#_camel_jmx_configuration_options}

:::::::::::: sectionbody
::: paragraph
Camel JMX can be configured with the following options.
:::

+-----------------+-----------------+-----------------+-----------------+
| Option          | JVM System      | Default Value   | Description     |
|                 | Property        |                 |                 |
+=================+=================+=================+=================+
| `id`            |                 |                 | **required**    |
|                 |                 |                 | The JMX agent   |
|                 |                 |                 | name            |
+-----------------+-----------------+-----------------+-----------------+
| `usePlatf       | `org.apache.cam | `true`          | Whether to use  |
| ormMBeanServer` | el.jmx.usePlatf |                 | the MBeanServer |
|                 | ormMBeanServer` |                 | from JVM.       |
+-----------------+-----------------+-----------------+-----------------+
| `mbeanServe     | `o              | `or             | The default JMX |
| rDefaultDomain` | rg.apache.camel | g.apache.camel` | domain of the   |
|                 | .jmx.mbeanServe |                 | `MBeanServer`   |
|                 | rDefaultDomain` |                 |                 |
+-----------------+-----------------+-----------------+-----------------+
| `mbeanOb        | `org.apache.ca  | `or             | The JMX domain  |
| jectDomainName` | mel.jmx.mbeanOb | g.apache.camel` | that all object |
|                 | jectDomainName` |                 | names will use  |
+-----------------+-----------------+-----------------+-----------------+
| `mbeansLevel`   |                 | `Default`       | Configure the   |
|                 |                 |                 | level for which |
|                 |                 |                 | MBeans are      |
|                 |                 |                 | registered. See |
|                 |                 |                 | section         |
|                 |                 |                 | *Registering    |
|                 |                 |                 | MBeans for      |
|                 |                 |                 | specific        |
|                 |                 |                 | resources only* |
|                 |                 |                 | for more        |
|                 |                 |                 | details.        |
+-----------------+-----------------+-----------------+-----------------+
| `only           | `org.apache     | `false`         | If this option  |
| RegisterProcess | .camel.jmx.only |                 | is enabled,     |
| orWithCustomId` | RegisterProcess |                 | then only       |
|                 | orWithCustomId` |                 | processors with |
|                 |                 |                 | a custom id set |
|                 |                 |                 | will be         |
|                 |                 |                 | registered.     |
|                 |                 |                 | This allows you |
|                 |                 |                 | to filer out    |
|                 |                 |                 | unwanted        |
|                 |                 |                 | processors in   |
|                 |                 |                 | the JMX         |
|                 |                 |                 | console.        |
+-----------------+-----------------+-----------------+-----------------+
| `s              |                 | `Default`       | Configure the   |
| tatisticsLevel` |                 |                 | level for       |
|                 |                 |                 | whether         |
|                 |                 |                 | performance     |
|                 |                 |                 | statistics is   |
|                 |                 |                 | enabled for the |
|                 |                 |                 | MBean. See      |
|                 |                 |                 | section         |
|                 |                 |                 | *Configuring    |
|                 |                 |                 | level of        |
|                 |                 |                 | granularity for |
|                 |                 |                 | performance     |
|                 |                 |                 | statistics* for |
|                 |                 |                 | more details.   |
+-----------------+-----------------+-----------------+-----------------+
| `i              | `org.apa        | `false`         | Whether to      |
| ncludeHostName` | che.camel.jmx.i |                 | include the     |
|                 | ncludeHostName` |                 | hostname in the |
|                 |                 |                 | MBean naming.   |
+-----------------+-----------------+-----------------+-----------------+
| `us             | `org.apac       | `false`         | Whether to use  |
| eHostIPAddress` | he.camel.jmx.us |                 | hostname or IP  |
|                 | eHostIPAddress` |                 | Address in the  |
|                 |                 |                 | service url     |
|                 |                 |                 | when creating   |
|                 |                 |                 | the remote      |
|                 |                 |                 | connector. By   |
|                 |                 |                 | default, the    |
|                 |                 |                 | hostname will   |
|                 |                 |                 | be used.        |
+-----------------+-----------------+-----------------+-----------------+
| `loadSta        | `org.apache.ca  | `false`         | Whether load    |
| tisticsEnabled` | mel.jmx.loadSta |                 | statistics is   |
|                 | tisticsEnabled` |                 | enabled (gather |
|                 |                 |                 | load statistics |
|                 |                 |                 | using a         |
|                 |                 |                 | background      |
|                 |                 |                 | thread per      |
|                 |                 |                 | CamelContext).  |
+-----------------+-----------------+-----------------+-----------------+
| `end            | `org.apach      | `true`          | Whether         |
| pointRuntimeSta | e.camel.jmx.end |                 | endpoint        |
| tisticsEnabled` | pointRuntimeSta |                 | runtime         |
|                 | tisticsEnabled` |                 | statistics is   |
|                 |                 |                 | enabled (gather |
|                 |                 |                 | runtime usage   |
|                 |                 |                 | of each         |
|                 |                 |                 | incoming and    |
|                 |                 |                 | outgoing        |
|                 |                 |                 | endpoint).      |
+-----------------+-----------------+-----------------+-----------------+
| `mask`          | `org.apache.    | `true`          | A flag that     |
|                 | camel.jmx.mask` |                 | indicates       |
|                 |                 |                 | whether to      |
|                 |                 |                 | remove detected |
|                 |                 |                 | sensitive       |
|                 |                 |                 | information     |
|                 |                 |                 | (such as        |
|                 |                 |                 | passwords) from |
|                 |                 |                 | MBean names and |
|                 |                 |                 | attributes.     |
+-----------------+-----------------+-----------------+-----------------+
| `upda           | `org.apache     | `false`         | Whether to      |
| teRouteEnabled` | .camel.jmx.upda |                 | allow updating  |
|                 | teRouteEnabled` |                 | routes at       |
|                 |                 |                 | runtime via JMX |
|                 |                 |                 | using the       |
|                 |                 |                 | Man             |
|                 |                 |                 | agedRouteMBean. |
|                 |                 |                 | This is         |
|                 |                 |                 | disabled by     |
|                 |                 |                 | default, but    |
|                 |                 |                 | can be enabled  |
|                 |                 |                 | for development |
|                 |                 |                 | and             |
|                 |                 |                 | troubleshooting |
|                 |                 |                 | purposes, such  |
|                 |                 |                 | as updating     |
|                 |                 |                 | routes in an    |
|                 |                 |                 | existing        |
|                 |                 |                 | running Camel   |
|                 |                 |                 | via JMX and     |
|                 |                 |                 | other tools.    |
+-----------------+-----------------+-----------------+-----------------+

::::: sect2
### Registering MBeans for specific resources only {#_registering_mbeans_for_specific_resources_only}

::: paragraph
Camel automatically registers MBeans for the context, routes, and
processors when it starts up. However, you can specify a level to
control whether MBeans are registered at startup. The levels are:
:::

::: ulist
- `Default` - Camel will register MBeans for the context, all the routes
  and the processors.

- `RoutesOnly` - Camel will register MBeans for the context and for all
  the routes (not for any processor).

- `ContextOnly` - Camel will register MBeans for the context (neither
  for any route nor for any processor).
:::
:::::

::::::: sect2
### Registering new MBeans for new routes, created by route templates, Kamelets {#_registering_new_mbeans_for_new_routes_created_by_route_templates_kamelets}

::: paragraph
Camel provides the following settings to control when to register
mbeans.
:::

+-----------------------+----------------------+----------------------+
| Option                | Default              | Description          |
+=======================+======================+======================+
| `registerAlways`      | `false`              | If enabled then      |
|                       |                      | MBeans are always    |
|                       |                      | registered.          |
+-----------------------+----------------------+----------------------+
| `registerNewRoutes`   | `true`               | If enabled then      |
|                       |                      | adding new routes    |
|                       |                      | after CamelContext   |
|                       |                      | has been started     |
|                       |                      | will also register   |
|                       |                      | MBeans from that     |
|                       |                      | given route.         |
+-----------------------+----------------------+----------------------+
| `registerR            | `false`              | If enabled then      |
| outesCreateByKamelet` |                      | adding routes        |
|                       |                      | created by Kamelet   |
|                       |                      | will also register   |
|                       |                      | MBeans from that     |
|                       |                      | given route.         |
+-----------------------+----------------------+----------------------+
| `registerRo           | `true`               | If enabled then      |
| utesCreateByTemplate` |                      | adding routes        |
|                       |                      | created by route     |
|                       |                      | template (not        |
|                       |                      | Kamelet, see option  |
|                       |                      | above) will also     |
|                       |                      | register MBeans from |
|                       |                      | that given route.    |
+-----------------------+----------------------+----------------------+

::: paragraph
By default, Camel automatically registers MBeans for all routes
configured at startup. The `registerNewRoutes` option controls whether
MBeans should also be registered for new routes added later on. This
feature can be disabled, for example, if you are adding and removing
temporary routes that do not require management.
:::

::: paragraph
In **Camel 4.5** onwards, there are additional options to configure
whether routes created from route templates or Kamelets should be
registered as MBeans or not. By default, Kamelets are now disabled with
the intention to regard a Kamelet as a component, instead of a set of
additional routes and processors MBeans that is essentially unnecessary
for management and monitoring. The option
`registerRoutesCreateByKamelet` can be set to `true` to enable MBeans,
which is how Camel 4.4 or older behaves. On the other hand, routes
created from route templates (not Kamelets) are default enabled.
:::

::: {.admonitionblock .caution}
+-----------------------------------+-----------------------------------+
| ::: title                         | However, be cautious when using   |
| Caution                           | the `registerAlways` option in    |
| :::                               | conjunction with dynamic EIP      |
|                                   | patterns, such as the [Recipient  |
|                                   | List](compone                     |
|                                   | nts:eips:recipientList-eip.html), |
|                                   | which have unique endpoints. This |
|                                   | can potentially lead to system    |
|                                   | degradation due to the increasing |
|                                   | number of MBeans in the registry  |
|                                   | from its associated               |
|                                   | services/producers. Keep in mind  |
|                                   | that an MBean is not a            |
|                                   | lightweight object and consumes   |
|                                   | memory.                           |
+-----------------------------------+-----------------------------------+
:::
:::::::
::::::::::::
:::::::::::::

:::::::::::::::::::::::::::::::: sect1
## Management naming pattern {#_management_naming_pattern}

::::::::::::::::::::::::::::::: sectionbody
::: paragraph
You can configure a naming pattern for the MBeans names that Camel
creates. The pattern is used as part of the `ObjectName` as the key
after the domain name.
:::

::: paragraph
By default, Camel will use MBean names for the
`ManagedCamelContextMBean` as follows:
:::

:::: listingblock
::: content
``` highlight
org.apache.camel:context=camel-1,type=context,name=camel-1
```
:::
::::

::: paragraph
If you configure a name on the `CamelContext` then that name is part of
the `ObjectName` as well. For example, if we have:
:::

:::: listingblock
::: content
``` highlight
<camelContext id="myCamel" ...>
```
:::
::::

::: paragraph
Then the MBean names will be as follows:
:::

:::: listingblock
::: content
``` highlight
org.apache.camel:context=myCamel,type=context,name=myCamel
```
:::
::::

::: paragraph
In the event of a naming clash within the JVM, such as when there is
already an MBean with the same name, Camel will automatically try to
resolve the issue by finding a new, available name in the
`JMXMBeanServer` using a counter. For example, the counter will be
appended to the name, resulting in an `ObjectName` such as `myCamel-1`:
:::

:::: listingblock
::: content
``` highlight
org.apache.camel:context=myCamel-1,type=context,name=myCamel
```
:::
::::

::::::::::::::::: sect2
### Naming Patterns {#_naming_patterns}

::: paragraph
This is possible because Camel uses a naming pattern by default that
supports the following tokens:
:::

::: ulist
- `#camelId#` = the CamelContext id (eg the name)

- `#name#` - same as `#camelId#`

- `#counter#` - an incrementing counter
:::

::: paragraph
If there is a naming clash in the `JMXMBeanServer` then Camel will
automatically fall back and use the `#counter#` in the pattern to remedy
this. Thus, the following patterns will then be used: `#name#-#counter#`
:::

::: paragraph
If you set an explicit naming pattern, then that pattern is always used,
and the default patterns above are **not** used.
:::

::: paragraph
This allows us to have full control, very easily, of the naming for both
the `CamelContext` id in the Registry and the JMX MBeans in the
`JMXMBeanRegistry`.
:::

::: paragraph
So if we want to explicitly name both the `CamelContext` and to use
fixed MBean names that do not change (i.e., without counters), then we
can use the `managementNamePattern` attribute:
:::

:::: listingblock
::: content
``` highlight
<camelContext id="myCamel" managementNamePattern="#name#">
```
:::
::::

::: paragraph
Then the MBean names will always be as follows:
:::

:::: listingblock
::: content
``` highlight
org.apache.camel:context=myCamel,type=context,name=myCamel
```
:::
::::

::: paragraph
In Java, you can configure the `managementNamePattern` as follows:
:::

:::: listingblock
::: content
``` highlight
context.getManagementNameStrategy().setNamePattern("#name#");
```
:::
::::
:::::::::::::::::
:::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::

:::::::::::::::: sect1
## Configuring performance statistics {#_configuring_performance_statistics}

::::::::::::::: sectionbody
::: paragraph
You can set a level for whether performance statistics are enabled or
not when Camel starts up. The levels are:
:::

::: ulist
- `Default` - Camel will enable statistics for both routes and
  processors (fine-grained).

- `Extended` - As default but with additional statistics gathered during
  runtime such as fine-grained level of usage of endpoints and more.

- `RoutesOnly` - Camel will only enable statistics for routes (coarse
  grained)

- `Off` - Camel will not use any statistics.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | ::: paragraph                     |
| Note                              | **What does statistics enabled    |
| :::                               | mean?**                           |
|                                   | :::                               |
|                                   |                                   |
|                                   | ::: paragraph                     |
|                                   | Statistics enabled means that     |
|                                   | Camel will do fine-grained        |
|                                   | performance statistics for that   |
|                                   | particular MBean. There are       |
|                                   | statistics you can see, such as   |
|                                   | number of exchanges               |
|                                   | completed/failed,                 |
|                                   | last/total/min/max/mean           |
|                                   | processing time, first/last       |
|                                   | failed time, etc.                 |
|                                   | :::                               |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
Using Java DSL, you set the statistics level by:
:::

:::: listingblock
::: content
``` highlight
// only enable routes when Camel starts
context.getManagementStrategy().setStatisticsLevel(ManagementStatisticsLevel.RoutesOnly);
```
:::
::::

::: paragraph
And from XML DSL you can do:
:::

:::: listingblock
::: content
``` highlight
<camelContext id="camel" xmlns="http://camel.apache.org/schema/spring">
    <jmxAgent id="agent" statisticsLevel="RoutesOnly"/>
 ...
</camelContext>
```
:::
::::

::::: sect2
### Performance load statistics {#_performance_load_statistics}

::: paragraph
It is possible to include load statistics for CamelContext and Route
MBeans. These statistics are for average load based on the number of
in-flight exchanges, measured over periods of 1, 5, and 15 minutes. This
is similar to load statistics on Unix systems.
:::

::: paragraph
You can enable this by setting `loadStatisticsEnabled=true`.
:::
:::::
:::::::::::::::
::::::::::::::::

:::::::::::: sect1
## Hiding sensitive information {#_hiding_sensitive_information}

::::::::::: sectionbody
::: paragraph
By default, Camel enlists MBeans in JMX such as endpoints configured
using URIs. In this configuration, there may be sensitive information
such as passwords.
:::

::: paragraph
This will mask URIs having options such as password and passphrase, and
use `xxxxxx` as the replacement value.
:::

:::::::: sect2
### Masking JMX attributes in custom components {#_masking_jmx_attributes_in_custom_components}

::: paragraph
When implementing custom Camel components, you can mark which JMX
attributes to mask with the `@ManagedAttribute` and `@ManagedOperation`
annotations.
:::

::: paragraph
The `mask` attribute can be set to `true` to indicate that the result of
this JMX attribute/operation should be masked (if enabled on JMX agent,
see above).
:::

::: paragraph
For example, on the default managed endpoints from camel-core
`org.apache.camel.api.management.mbean.ManagedEndpointMBean`, we have
declared that the `EndpointUri` JMX attribute is masked:
:::

:::: listingblock
::: content
``` highlight
@ManagedAttribute(description = "Endpoint URI", mask = true)
String getEndpointUri();
```
:::
::::
::::::::
:::::::::::
::::::::::::

:::::: sect1
## Expose JMX metrics remotely {#_expose_jmx_metrics_remotely}

::::: sectionbody
::: paragraph
When you're running the application locally, you can use any JMX client
(ie, `jconsole`) to access to the process and the the statistics
exposed. However, you may want to expose these metrics remotely in order
to be accessed by any remote process: [expose the JMX metrics
remotely](https://docs.oracle.com/javase/tutorial/jmx/remote/jconsole.html).
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | make sure to properly secure the  |
| Note                              | access to JMX services for        |
| :::                               | production workloads.             |
+-----------------------------------+-----------------------------------+
:::
:::::
::::::

:::::::::::::::: sect1
## How to use a Java Agent {#_how_to_use_a_java_agent}

::::::::::::::: sectionbody
::: paragraph
Exposing the JMX metrics with its native protocol may not always be
possible (ie, for firewall limitations, security, etc). In this case you
may recur to the availability of JSR 160 compatible Java Agents (for
instance,
[Jolokia](https://jolokia.org/reference/html/manual/agents.html#agents-jvm)),
which goal is to adapt the JMX interface with HTTP based protocol. With
this approach you can run you application attaching a Java Agent whose
goal will be to take care of exposing JMX via HTTP. As an example:
:::

:::: listingblock
::: content
``` highlight
java -javaagent:jsr160-javaagent.jar -jar my-camel-app.jar
```
:::
::::

::: paragraph
will expose certain endpoints (depending on the implementation) which
would let you interact with JMX.
:::

::: paragraph
Depending on the implementation you will need to provide to a different
set of agent configuration. For example, if you're running Jolokia
agent, then, you will need to run this configuration to expose a plain
HTTP unsecure service:
:::

:::: listingblock
::: content
``` highlight
java -javaagent:jolokia-agent-jvm-2.1.1-javaagent.jar=protocol=http,useSslClientAuthentication=false,discoveryEnabled=false,host=*,extendedClientCheck=false -jar my-camel-app.jar
```
:::
::::

::: paragraph
From that moment onward you will be able to access the service via:
:::

:::: listingblock
::: content
``` highlight
$ curl http://localhost:8778/jolokia/list/org.apache.camel | jq | more
{
  "request": {
    "path": "org.apache.camel",
    "type": "list"
  },
  "value": {
    "context=camel-1,name=\"platform-http\",type=components": {
      "op": {
        "getCamelId": {
          "args": [],
          "ret": "java.lang.String",
          "desc": "CamelId"
        },
        "isHealthCheckSupported": {
          "args": [],
          "ret": "boolean",
          "desc": "HealthCheckSupported"
        },
        "getComponentName": {
          "args": [],
          "ret": "java.lang.String",
          "desc": "ComponentName"
        },
...
```
:::
::::

::: paragraph
Each agent implementation may provide a different way to get and execute
actions on the various endpoint.
:::

::: {.admonitionblock .warning}
+-----------------------------------+-----------------------------------+
| ::: title                         | make sure to properly secure the  |
| Warning                           | access to the HTTP services for   |
| :::                               | production workloads checking the |
|                                   | specific agent configuration      |
|                                   | documentation.                    |
+-----------------------------------+-----------------------------------+
:::
:::::::::::::::
::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
