::: {#header}
# Property placeholders
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
:::::: {#preamble}
::::: sectionbody
::: paragraph
Camel has extensive support for property placeholders, which can be used
*almost anywhere* in your Camel [routes](routes.html),
[endpoints](endpoint.html), [DSL](dsl.html), and [route
configuration](route-configuration.html), [bean
integration](bean-integration.html) and elsewhere.
:::

::: paragraph
Property placeholders are used to define a *placeholder* instead of the
actual value. This is important as you would want to be able to make
your applications external configurable, such as values for network
addresses, port numbers, authentication credentials, login tokens, and
configuration in general.
:::
:::::
::::::

:::::: sect1
## Properties component {#_properties_component}

::::: sectionbody
::: paragraph
Camel provides the [Properties](components::properties-component.html)
out of the box from the core, which is responsible for handling and
resolving the property placeholders.
:::

::: paragraph
See the [Properties](components::properties-component.html)
documentation for how to configure Camel to known from which location(a)
to load properties.
:::
:::::
::::::

::::::::::::::::::::::::::::::::: sect1
## Property placeholder syntax {#_property_placeholder_syntax}

:::::::::::::::::::::::::::::::: sectionbody
::: paragraph
The value of a Camel property can be obtained by specifying its key name
within a property placeholder, using the following syntax: `{{key}}`
:::

::: paragraph
For example:
:::

:::: listingblock
::: content
``` highlight
{{file.uri}}
```
:::
::::

::: paragraph
where `file.uri` is the property key.
:::

::: paragraph
Property placeholders can be used to specify parts, or all, of an
endpoint's URI by embedding one or more placeholders in the URI's string
definition.
:::

::::::: sect2
### Using property placeholder with default value {#_using_property_placeholder_with_default_value}

::: paragraph
You can specify a default value to use if a property with the key does
not exist, where the default value is the text after the colon:
:::

:::: listingblock
::: content
``` highlight
{{file.url:/some/path}}
```
:::
::::

::: paragraph
In this case the default value is `/some/path`.
:::
:::::::

::::::::::::: sect2
### Using optional property placeholders {#_using_optional_property_placeholders}

::: paragraph
Camel's elaborate property placeholder feature supports optional
placeholders, which is defined with the `?` (question mark) as prefix in
the key name, as shown:
:::

:::: listingblock
::: content
``` highlight
{{?myBufferSize}}
```
:::
::::

::: paragraph
If a value for the key exists then the value is used, however if the key
does not exist, then Camel understands this, such as when used in
[Endpoints](endpoint.html):
:::

:::: listingblock
::: content
``` highlight
file:foo?bufferSize={{?myBufferSize}}
```
:::
::::

::: paragraph
Then the `bufferSize` option will only be configured in the endpoint, if
a placeholder exists. Otherwise the option will not be set on the
endpoint, meaning the endpoint would be *restructued* as:
:::

:::: listingblock
::: content
``` highlight
file:foo
```
:::
::::

::: paragraph
Then the option `bufferSize` is not in specified at all, and this would
allow Camel to use the standard default value for `bufferSize` if any
exists.
:::
:::::::::::::

::::::::: sect2
### Reverse a boolean value {#_reverse_a_boolean_value}

::: paragraph
If a property placeholder is a boolean value, then it is possible to
negate (reverse) the value by using `!` as prefix in the key.
:::

:::: listingblock
::: content
``` highlight
integration.ftpEnabled=true
```
:::
::::

:::: listingblock
::: content
``` highlight
from("ftp:....").autoStartup("{{integration.ftpEnabled}}")
    .to("kafka:cheese")

from("jms:....").autoStartup("{{!integration.ftpEnabled}}")
    .to("kafka:cheese")
```
:::
::::

::: paragraph
In the example above then the FTP route or the JMS route should only be
started. So if the FTP is enabled then JMS should be disable, and
vise-versa. We can do this be negating the `autoStartup` in the JMS
route, by using `!integration.ftpEnabled` as the key.
:::
:::::::::
::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Using property placeholders {#_using_property_placeholders}

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
When using property placeholders in the endpoint [URIs](uris.html) you
should use this with the syntax `{{key}}` as shown in this example:
:::

:::: listingblock
::: content
``` highlight
cool.end = mock:result
where = cheese
```
:::
::::

::: paragraph
And in Java DSL:
:::

:::: listingblock
::: content
``` highlight
from("direct:start")
    .to("{{cool.end}}");
```
:::
::::

::: paragraph
And in XML DSL:
:::

:::: listingblock
::: content
``` highlight
<route>
  <from uri="direct:start"/>
  <to uri="{{cool.end}}"/>
</route>
```
:::
::::

::: paragraph
A property placeholder may also just be a one part in the endpoint URI.
A common use-case is to use a placeholder for an endpoint option such as
the size of the write buffer in the file endpoint:
:::

:::: listingblock
::: content
``` highlight
buf = 8192
```
:::
::::

:::: listingblock
::: content
``` highlight
from("direct:start")
    .to("file:outbox?bufferSize={{buf}}");
```
:::
::::

::: paragraph
And in XML DSL:
:::

:::: listingblock
::: content
``` highlight
<route>
  <from uri="direct:start"/>
  <to uri="file:outbox?bufferSize={{buf}}"/>
</route>
```
:::
::::

::: paragraph
However the placeholder can be anywhere, so it could also be the name of
a mock endpoint
:::

:::: listingblock
::: content
``` highlight
from("direct:start")
    .to("mock:{{where}}");
```
:::
::::

::: paragraph
In the example above the mock endpoint, is already hardcoded to start
with `mock:`, and the `where` placeholder has the value `cheese` so the
resolved uri becomes `mock:cheese`.
:::

::::::::::::::::::::: sect2
### Property placeholders referring to other properties (nested placeholders) {#_property_placeholders_referring_to_other_properties_nested_placeholders}

::: paragraph
You can also have properties with refer to each other such as:
:::

:::: listingblock
::: content
``` highlight
cool.foo=result
cool.concat=mock:{{cool.foo}}
```
:::
::::

::: paragraph
Notice how `cool.concat` refer to another property.
:::

::: paragraph
And the route in XML:
:::

:::: listingblock
::: content
``` highlight
<route>
  <from uri="direct:start"/>
  <to uri="{{cool.concat}}"/>
</route>
```
:::
::::

::::::::::::: sect3
#### Turning off nested placeholders {#_turning_off_nested_placeholders}

::: paragraph
If the placeholder value contains data that interfere with the property
placeholder syntax `{{` and `}}` (such as JSon data), you can be then
explicit turn off nested placeholder by `?nested=false` in the key name,
such as shown:
:::

:::: listingblock
::: content
``` highlight
<route>
  <from uri="direct:start"/>
  <to uri="elasticsearch:foo?query={{myQuery?nested=false}}"/>
</route>
```
:::
::::

::: paragraph
In the example above the placeholder *myQuery* placeholder value is as
follows
:::

:::: listingblock
::: content
``` highlight
{"query":{"match_all":{}}}
```
:::
::::

::: paragraph
Notice how the json query ends with `}}` which interfere with the Camel
property placeholder syntax.
:::

::: paragraph
Nested placeholders can also be turned off globally on the
[Properties](components::properties-component.html) component, such as:
:::

:::: listingblock
::: content
``` highlight
CamelContext context = ...
context.getPropertiesComponent().setNestedPlaceholder(false);
```
:::
::::
:::::::::::::
:::::::::::::::::::::

:::::: sect2
### Escape a property placeholder {#_escape_a_property_placeholder}

::: paragraph
The property placeholder can be problematic if the double curly brackets
are used by a third party library like for example a query in
ElasticSearch of type `{"query":{"match_all":{}}}`.
:::

::: paragraph
To work around that it is possible to escape the double curly brackets
with a backslash character like for example `\{{ property-name \}}`.
This way, it won't be interpreted as a property placeholder to resolve
and will be resolved as `{{ property-name }}`.
:::

::: paragraph
If for some reason, the backslash character before the double curly
brackets must not be interpreted as an escape character, it is possible
to add another backslash in front of it to escape it, it will then be
seen as a backslash.
:::
::::::

::::::::: sect2
### Using property placeholders multiple times {#_using_property_placeholders_multiple_times}

::: paragraph
You can of course also use placeholders several times:
:::

:::: listingblock
::: content
``` highlight
cool.start=direct:start
cool.showid=true
cool.result=result
```
:::
::::

::: paragraph
And in this route we use `cool.start` two times:
:::

:::: listingblock
::: content
``` highlight
from("{{cool.start}}")
    .to("log:{{cool.start}}?showBodyType=false&showExchangeId={{cool.showid}}")
    .to("mock:{{cool.result}}");
```
:::
::::
:::::::::

:::::: sect2
### Using property placeholders with producer template {#_using_property_placeholders_with_producer_template}

::: paragraph
You can also your property placeholders when using
[ProducerTemplate](producertemplate.html) for example:
:::

:::: listingblock
::: content
``` highlight
template.sendBody("{{cool.start}}", "Hello World");
```
:::
::::
::::::

:::::: sect2
### Using property placeholders with consumer template {#_using_property_placeholders_with_consumer_template}

::: paragraph
This can also be done when using
[ConsumerTemplate](consumertemplate.html), such as:
:::

:::: listingblock
::: content
``` highlight
Object body = template.receiveBody("{{cool.start}}");
```
:::
::::
::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::: sect1
## Resolving property placeholders on cloud {#_resolving_property_placeholders_on_cloud}

::::::::: sectionbody
::: paragraph
When you are running your Camel application on the cloud you may want to
automatically scan any Configmap or Secret as it was an application
properties. Given the following Secret:
:::

:::: listingblock
::: content
    apiVersion: v1
    data:
      my-property: Q2FtZWwgNC44
    kind: Secret
    metadata:
      name: my-secret
    type: Opaque
:::
::::

::: paragraph
You can mount it in your Pod container, for instance, under
`/etc/camel/conf.d/_secrets/my-secret`. Now, just make your Camel
application be aware where to scan your configuration via
`camel.main.cloud-properties-location = /etc/camel/conf.d/_secrets/my-secret`
application properties. It's a comma separated value, so, you can add as
many Secrets/Configmaps you need.
:::

::: paragraph
At runtime, you will be able to read the configuration transparently as
`{{ my-property }}` as you're doing with the rest of properties.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | the same configuration works with |
| Note                              | Configmap.                        |
| :::                               |                                   |
+-----------------------------------+-----------------------------------+
:::
:::::::::
::::::::::

::::::::::::::::::: sect1
## Resolving property placeholders from Java code {#_resolving_property_placeholders_from_java_code}

:::::::::::::::::: sectionbody
::: paragraph
If you need to resolve property placeholder(s) from some Java code, then
Camel has two APIs for this:
:::

::: ulist
- You can use the method `resolveProperty` on the `PropertiesComponent`
  to resolve a single property from Java code.

- Use the method `resolvePropertyPlaceholders` on the `CamelContext` to
  resolve (one or more) property placeholder(s) in a String.
:::

::: paragraph
For example to resolve a placeholder with key foo, you can do:
:::

:::: listingblock
::: content
``` highlight
Optional<String> prop = camelContext.getPropertiesComponent().resolveProperty("foo");
if (prop.isPresent()) {
    String value = prop.get();
    ....
}
```
:::
::::

::: paragraph
This API is to lookup a single property and returns a
`java.util.Optional` type.
:::

::: paragraph
The `CamelContext` have another API which is capable of resolving
multiple placeholders, and interpolate placeholders from an input
String. Lets try with an example to explain this:
:::

:::: listingblock
::: content
``` highlight
String msg = camelContext.resolvePropertyPlaceholders("{{greeting}} Camel user, Camel is {{cool}} dont you think?");
```
:::
::::

::: paragraph
The input string is a text statement which have two placeholders that
will be resolved, for example:
:::

:::: listingblock
::: content
``` highlight
greeting = Hi
cool = awesome
```
:::
::::

::: paragraph
Will be resolved to:
:::

:::: listingblock
::: content
``` highlight
Hi Camel user, Camel is awesome dont you think?
```
:::
::::
::::::::::::::::::
:::::::::::::::::::

::::::::::::::::::: sect1
## Using property placeholders for any kind of attribute in Spring XML files {#_using_property_placeholders_for_any_kind_of_attribute_in_spring_xml_files}

:::::::::::::::::: sectionbody
::: paragraph
Previously it was only the `xs:string` type attributes in the XML DSL
that support placeholders. For example often a timeout attribute would
be a `xs:int` type and thus you cannot set a string value as the
placeholder key. This is now possible using a special placeholder
namespace.
:::

::: paragraph
In the example below we use the `prop` prefix for the namespace
`http://camel.apache.org/schema/placeholder`. Now we can use `prop:` as
prefix to configure any kind of XML attributes in Spring XML files.
:::

::: paragraph
In the example below we want to use a placeholder for the
`stopOnException` option in the
[Multicast](components:eips:multicast-eip.html) EIP. The
`stopOnException` is a `xs:boolean` type, so we cannot configure this
as:
:::

:::: listingblock
::: content
``` highlight
<multicast stopOnException="{{stop}}">
   ...
</multicast>
```
:::
::::

::: paragraph
Instead, we must use the `prop:` namespace, so we must add this
namespace in the top of the XML file in the `<beans>` tag.
:::

::: paragraph
To configure the option we must then use the `prop:optionName` as shown
below:
:::

:::: listingblock
::: content
``` highlight
<multicast prop:stopOnException="stop">
  ...
</multicast>
```
:::
::::

::: paragraph
The complete example is below:
:::

:::: listingblock
::: content
``` highlight
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:prop="http://camel.apache.org/schema/placeholder"
       xsi:schemaLocation="
           http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
           http://camel.apache.org/schema/spring http://camel.apache.org/schema/spring/camel-spring.xsd">

    <bean id="damn" class="java.lang.IllegalArgumentException">
        <constructor-arg index="0" value="Damn"/>
    </bean>

    <camelContext xmlns="http://camel.apache.org/schema/spring">
        <propertyPlaceholder id="properties" location="classpath:myprop.properties"/>
        <route>
            <from uri="direct:start"/>
            <!-- use prop namespace, to define a property placeholder, which maps to option stopOnException={{stop}} -->
            <multicast prop:stopOnException="stop">
                <to uri="mock:a"/>
                <throwException ref="damn"/>
                <to uri="mock:b"/>
            </multicast>
        </route>
    </camelContext>
</beans>
```
:::
::::

::: paragraph
In our properties file we have the value defined as:
:::

:::: listingblock
::: content
``` highlight
stop = true
```
:::
::::
::::::::::::::::::
:::::::::::::::::::

:::::::::::::: sect1
## Bridging Camel property placeholders with Spring XML files {#_bridging_camel_property_placeholders_with_spring_xml_files}

::::::::::::: sectionbody
::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | If you are using Spring Boot then |
| Note                              | this does not apply. This is only |
| :::                               | for legacy Camel and Spring       |
|                                   | applications which are using      |
|                                   | Spring XML files.                 |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
The Spring Framework does not allow third-party frameworks such as
Apache Camel to seamless hook into the Spring property placeholder
mechanism. However, you can bridge Spring and Camel by declaring a
Spring bean with the type
`org.apache.camel.spring.spi.BridgePropertyPlaceholderConfigurer`, which
is a Spring
`org.springframework.beans.factory.config.PropertyPlaceholderConfigurer`
type.
:::

::: paragraph
To bridge Spring and Camel you must define a single bean as shown below:
:::

:::: listingblock
::: content
``` highlight
<!-- bridge spring property placeholder with Camel -->
<!-- you must NOT use the <context:property-placeholder at the same time, only this bridge bean -->
<bean id="bridgePropertyPlaceholder" class="org.apache.camel.spring.spi.BridgePropertyPlaceholderConfigurer">
  <property name="location" value="classpath:org/apache/camel/component/properties/cheese.properties"/>
</bean>
```
:::
::::

::: paragraph
You **must not** use the spring `<context:property-placeholder>`
namespace at the same time; this is not possible.
:::

::: paragraph
After declaring this bean, you can define property placeholders using
both the Spring style, and the Camel style within the `<camelContext>`
tag as shown below:
:::

:::: listingblock
::: content
``` highlight
<!-- a bean that uses Spring property placeholder -->
<!-- the ${hi} is a spring property placeholder -->
<bean id="hello" class="org.apache.camel.component.properties.HelloBean">
  <property name="greeting" value="${hi}"/>
</bean>

<camelContext xmlns="http://camel.apache.org/schema/spring">
  <!-- in this route we use Camels property placeholder {{ }} style -->
  <route>
    <from uri="direct:{{cool.bar}}"/>
    <bean ref="hello"/>
    <to uri="{{cool.end}}"/>
  </route>
</camelContext>
```
:::
::::

::: paragraph
Notice how the hello bean is using pure Spring property placeholders
using the `${}` notation. And in the Camel routes we use the Camel
placeholder notation with `{{key}}`.
:::
:::::::::::::
::::::::::::::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Using property placeholder functions {#_using_property_placeholder_functions}

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
The [Properties](components::properties-component.html) component
includes the following functions out of the box:
:::

::: ulist
- `env` - A function to lookup the property from OS environment
  variables

- `sys` - A function to lookup the property from Java JVM system
  properties

- `bean` - A function to lookup the property from the return value of
  bean's method (requires `camel-bean` JAR)

- `service` - A function to lookup the property from OS environment
  variables using the service naming idiom

- `service.name` - A function to lookup the property from OS environment
  variables using the service naming idiom returning the hostname part
  only

- `service.port` - A function to lookup the property from OS environment
  variables using the service naming idiom returning the port part only
:::

::: paragraph
These functions are intended to make it easy to lookup values from the
environment, as shown in the example below:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
    <route>
        <from uri="direct:start"/>
        <to uri="{{env:SOMENAME}}"/>
        <to uri="{{sys:MyJvmPropertyName}}"/>
    </route>
</camelContext>
```
:::
::::

::: paragraph
You can use default values as well, so if the property does not exist,
you can define a default value as shown below, where the default value
is a `log:foo` and `log:bar` value.
:::

:::: listingblock
::: content
``` highlight
<camelContext>
    <route>
        <from uri="direct:start"/>
        <to uri="{{env:SOMENAME:log:foo}}"/>
        <to uri="{{sys:MyJvmPropertyName:log:bar}}"/>
    </route>
</camelContext>
```
:::
::::

::: paragraph
The service function is for looking up a service which is defined using
OS environment variables using the service naming idiom, to refer to a
service location using `hostname : port`
:::

::: ulist
- *NAME***\_SERVICE_HOST**

- *NAME***\_SERVICE_PORT**
:::

::: paragraph
in other words the service uses `_SERVICE_HOST` and `_SERVICE_PORT` as
prefix. So if the service is named FOO, then the OS environment
variables should be set as
:::

:::: listingblock
::: content
``` highlight
export $FOO_SERVICE_HOST=myserver
export $FOO_SERVICE_PORT=8888
```
:::
::::

::: paragraph
For example if the FOO service a remote HTTP service, then we can refer
to the service in the Camel endpoint uri, and use the HTTP component to
make the HTTP call:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
    <route>
        <from uri="direct:start"/>
        <to uri="http://{{service:FOO}}/myapp"/>
    </route>
</camelContext>
```
:::
::::

::: paragraph
And we can use default values if the service has not been defined, for
example to call a service on localhost, maybe for unit testing.
:::

:::: listingblock
::: content
``` highlight
<camelContext>
<route>
    <from uri="direct:start"/>
    <to uri="http://{{service:FOO:localhost:8080}}/myapp"/>
</route>
</camelContext>
```
:::
::::

::: paragraph
The bean function (you need to have `camel-bean` JAR on classpath) is
for looking up the property from the return value of bean's method.
:::

::: paragraph
Assuming we have registered a bean named \'foo\' that has a method
called \'bar\' that returns a directory name, then we can refer to the
bean's method in the camel endpoint url, and use the file component to
poll a directory:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
<route>
    <from uri="file:{{bean:foo.bar}}"/>
    <to uri="direct:result"/>
</route>
</camelContext>
```
:::
::::

::: {.admonitionblock .important}
+-----------------------------------+-----------------------------------+
| ::: title                         | The method must be a public       |
| Important                         | no-arg method (i.e. no            |
| :::                               | parameters) and return a value    |
|                                   | such as a String, boolean, int.   |
+-----------------------------------+-----------------------------------+
:::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect2
### Using Kubernetes property placeholder functions {#_using_kubernetes_property_placeholder_functions}

::: paragraph
The `camel-kubernetes` component include the following functions:
:::

::: ulist
- `configmap` - A function to lookup the string property from Kubernetes
  ConfigMaps.

- `configmap-binary` - A function to lookup the binary property from
  Kubernetes ConfigMaps.

- `secret` - A function to lookup the string property from Kubernetes
  Secrets.

- `secret-binary` - A function to lookup the binary property from
  Kubernetes Secrets.
:::

::: paragraph
The syntax for both functions are:
:::

:::: listingblock
::: content
``` highlight
configmap:name/key[:defaultValue]
```
:::
::::

::: paragraph
Where the default value is optional, for example the following will
lookup `myKey`, and fail if there is no such configmap.
:::

:::: listingblock
::: content
``` highlight
configmap:mymap/mykey
```
:::
::::

::: paragraph
In this example then it would not fail as a default value is provided:
:::

:::: listingblock
::: content
``` highlight
configmap:mymap/mykey:123
```
:::
::::

::: paragraph
If the value stored in the configmap is in binary format, so it is
stored as `Binary Data`, it will be downloaded in a file, and it returns
the absolute path of the file
:::

:::: listingblock
::: content
``` highlight
configmap-binary:mymap/mybinkey
```
:::
::::

::: paragraph
it returns a path like `/tmp/camel11787545916150467474/mybinkey`
:::

::: paragraph
Before the Kubernetes property placeholder functions can be used they
need to be configured with either (or both)
:::

::: ulist
- path - A *mount path* that must be mounted to the running pod, to load
  the configmaps or secrets from local disk.

- kubernetes client - **Autowired** An
  `io.fabric8.kubernetes.client.KubernetesClient` instance to use for
  connecting to the Kubernetes API server.
:::

::: paragraph
Camel will first use *mount paths* (if configured) to lookup, and then
fallback to use the `KubernetesClient`.
:::

:::::::: sect3
#### Configuring mount paths for ConfigMaps and Secrets {#_configuring_mount_paths_for_configmaps_and_secrets}

::: paragraph
The configuration of the *mount path* are used by the given order:
:::

::: {.olist .arabic}
1.  Reading configuration property with keys
    `camel.kubernetes-config.mount-path-configmaps` and
    `camel.kubernetes-config.mount-path-secrets`.

2.  Use JVM system property with key `camel.k.mount-path.configmaps` and
    `camel.k.mount-path.secrets` (Camel K compatible).

3.  Use OS ENV variable with key `CAMEL_K_MOUNT_PATH_CONFIGMAPS` and
    `CAMEL_K_MOUNT_PATH_SECRETS` (Camel K compatible).
:::

::: paragraph
For example to use `/etc/camel/resources/` as mount path, you can
configure this in the `application.properties`:
:::

:::: listingblock
::: content
``` highlight
camel.kubernetes-config.mount-path-configmaps = /etc/camel/myconfig/
camel.kubernetes-config.mount-path-secrets = /etc/camel/mysecrets/
```
:::
::::
::::::::

:::::::::::::: sect3
#### Configuring Kubernetes Client {#_configuring_kubernetes_client}

::: paragraph
Camel will autowire the `KubernetesClient` if a single instance of the
client exists in the running application (lookup via the
[Registry](registry.html)). Otherwise, a new `KubernetesClient` is
created. The client can be configured from either
:::

::: ulist
- Using `camel.kubernetes-config.client.` properties (see below for
  example)

- Attempt to auto-configure itself by a combination of OS Environment
  variables, reading from `~./kube/config` configuration, and service
  account token file. For more details see the
  [https://github.com/fabric8io/kubernetes-client](https://github.com/fabric8io/kubernetes-client){.bare}
  documentation.
:::

::: paragraph
You most likely only need to explicit configure the `KubernetesClient`
when you want to connect from a local computer to a remote Kubernetes
cluster, where you can specify various options, such as the masterUrl
and oauthToken as shown:
:::

:::: listingblock
::: content
``` highlight
camel.kubernetes-config.client.masterUrl = https://127.0.0.1:50179/
camel.kubernetes-config.client.oauthToken = eyJhbGciOiJSUzI1NiIsImtpZCI...
```
:::
::::

::: paragraph
The `KubernetesClient` has many options, see the
[https://github.com/fabric8io/kubernetes-client](https://github.com/fabric8io/kubernetes-client){.bare}
documentation.
:::

::: paragraph
If you only use *mount paths*, then it is good practice to disable
`KubernetesClient` which can be done by setting enabled to false as
show:
:::

:::: listingblock
::: content
``` highlight
camel.kubernetes-config.client-enabled = false
```
:::
::::

::: paragraph
When running your Camel applications inside an existing Kubernetes
cluster, then you often would not need to explicit configure the
`KubernetesClient` and can rely on default settings.
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | If you use Camel Quarkus, then it |
| Tip                               | is recommended to use their       |
| :::                               | [https://quarkus.io/guides/kube   |
|                                   | rnetes-config](https://quarkus.io |
|                                   | /guides/kubernetes-config){.bare} |
|                                   | which automatic pre-configure the |
|                                   | `KubernetesClient` which Camel    |
|                                   | then will reuse.                  |
+-----------------------------------+-----------------------------------+
:::
::::::::::::::

:::::::::::: sect3
#### Using configmap with Kubernetes {#_using_configmap_with_kubernetes}

::: paragraph
Given a configmap named `myconfig` in Kubernetes that has two entries:
:::

:::: listingblock
::: content
``` highlight
drink = beer
first = Carlsberg
```
:::
::::

::: paragraph
Then these values can be used in your Camel routes such as:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
  <route>
    <from uri="direct:start"/>
    <log message="What {{configmap:myconfig/drink}} do you want?"/>
    <log message="I want {{configmap:myconfig/first}}"/>
  </route>
</camelContext>
```
:::
::::

::: paragraph
You can also provide a default value in case a key does not exist:
:::

:::: listingblock
::: content
``` highlight
    <log message="I want {{configmap:myconfig/second:Heineken}}"/>
```
:::
::::
::::::::::::

:::::::::::::::: sect3
#### Using secrets with Kubernetes {#_using_secrets_with_kubernetes}

::: paragraph
Camel reads ConfigMaps from the Kubernetes API Server. And when RBAC is
enabled on the cluster, the ServiceAccount that is used to run the
application needs to have the proper permissions for such access.
:::

::: paragraph
A secret named `mydb` could contain username and passwords to connect to
a database such as:
:::

:::: listingblock
::: content
``` highlight
myhost = killroy
myport = 5555
myuser = scott
mypass = tiger
```
:::
::::

::: paragraph
This can be used in Camel with for example the Postrgres Sink Kamelet:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
  <route>
    <from uri="direct:rome"/>
    <setBody>
      <constant>{ "username":"oscerd", "city":"Rome"}</constant>
    </setBody>
    <to uri="kamelet:postgresql-sink?serverName={{secret:mydb/myhost}}
             &amp;serverPort={{secret:mydb/myport}}
             &amp;username={{secret:mydb/myuser}}
             &amp;password={{secret:mydb/mypass}}
             &amp;databaseName=cities
             &amp;query=INSERT INTO accounts (username,city) VALUES (:#username,:#city)"/>
  </route>
</camelContext>
```
:::
::::

::: paragraph
The postgres-sink Kamelet can also be configured in
`application.properties` which reduces the configuration in the route
above:
:::

:::: listingblock
::: content
``` highlight
camel.component.kamelet.postgresql-sink.databaseName={{secret:mydb/myhost}}
camel.component.kamelet.postgresql-sink.serverPort={{secret:mydb/myport}}
camel.component.kamelet.postgresql-sink.username={{secret:mydb/myuser}}
camel.component.kamelet.postgresql-sink.password={{secret:mydb/mypass}}
```
:::
::::

::: paragraph
Which reduces the route to:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
  <route>
    <from uri="direct:rome"/>
    <setBody>
      <constant>{ "username":"oscerd", "city":"Rome"}</constant>
    </setBody>
    <to uri="kamelet:postgresql-sink?databaseName=cities
             &amp;query=INSERT INTO accounts (username,city) VALUES (:#username,:#city)"/>
  </route>
</camelContext>
```
:::
::::
::::::::::::::::

::::::::::: sect3
#### Using configmap or secrets in local-mode {#_using_configmap_or_secrets_in_local_mode}

::: paragraph
During development you may want to run in *local mode* where you do not
need acces to a Kubernetes cluster, to lookup the configmap. In the
local mode, then Camel will lookup the configmap *keys* from local
properties, eg:
:::

::: paragraph
For example the example above with the postgresql kamelet, that was
configured using a secret:
:::

:::: listingblock
::: content
``` highlight
camel.component.kamelet.postgresql-sink.databaseName={{secret:mydb/myhost}}
camel.component.kamelet.postgresql-sink.serverPort={{secret:mydb/myport}}
camel.component.kamelet.postgresql-sink.username={{secret:mydb/myuser}}
camel.component.kamelet.postgresql-sink.password={{secret:mydb/mypass}}
```
:::
::::

::: paragraph
Now suppose we have a local Postrgres database we want to use, then we
can turn on *local mode* and specify the credentials in the same
properties file:
:::

:::: listingblock
::: content
``` highlight
camel.kubernetes-config.local-mode = true
mydb/myhost=localhost
mydb/myport=1234
mydb/myuser=scott
mydb/mypass=tiger
```
:::
::::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | Notice how the key is prefixed    |
| Note                              | with the name of the secret and a |
| :::                               | slash, eg `name/key`. This makes  |
|                                   | it easy to copy/paste from the    |
|                                   | actual use of the                 |
|                                   | configmap/secret and into the     |
|                                   | `application.properties` file.    |
+-----------------------------------+-----------------------------------+
:::
:::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::: sect2
### Using custom property placeholder functions {#_using_custom_property_placeholder_functions}

::: paragraph
The [Properties](components::properties-component.html) component allow
to plugin 3rd party functions which can be used during parsing of the
property placeholders. These functions are then able to do custom logic
to resolve the placeholders, such as looking up in databases, do custom
computations, or whatnot. The name of the function becomes the prefix
used in the placeholder.
:::

::: paragraph
This is best illustrated in the example route below, where we use `beer`
as the prefix:
:::

:::: listingblock
::: content
``` highlight
<route>
    <from uri="direct:start"/>
    <to uri="{{beer:FOO}}"/>
    <to uri="{{beer:BAR}}"/>
</route>
```
:::
::::

::: paragraph
The implementation of the function is only two methods as shown below:
:::

:::: listingblock
::: content
``` highlight
@org.apache.camel.spi.annotations.PropertiesFunction("beer")
public class MyBeerFunction implements PropertiesFunction {

    @Override
    public String getName() {
        return "beer";
    }

    @Override
    public String apply(String remainder) {
        return "mock:" + remainder.toLowerCase();
    }
}
```
:::
::::

::: paragraph
The function must implement the
`org.apache.camel.spi.PropertiesFunction` interface. The method
`getName` is the name of the function (beer). And the `apply` method is
where we implement the custom logic to do. As the sample code is from a
unit test, it just returns a value to refer to a mock endpoint.
:::

::: paragraph
You also need to have `camel-component-maven-plugin` as part of building
the component will then ensure that this custom properties function has
necessary source code generated that makes Camel able to automatically
discover the function.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | If the custom properties function |
| Note                              | need logic to startup and         |
| :::                               | shutdown, then the function can   |
|                                   | extend `ServiceSupport` and have  |
|                                   | this logic in `doStart` and       |
|                                   | `doStop` methods.                 |
+-----------------------------------+-----------------------------------+
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | For an example see the            |
| Tip                               | `camel-base64` component.         |
| :::                               |                                   |
+-----------------------------------+-----------------------------------+
:::
::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::: sect1
## Using third party property sources {#_using_third_party_property_sources}

::::::::::: sectionbody
::: paragraph
The properties component allows to plugin 3rd party sources to load and
lookup properties via the `PropertySource` API from camel-api.
:::

::: paragraph
The regular `PropertySource` will lookup the property on-demand, for
example to lookup values from a backend source such as a database or
HashiCorp Vault etc.
:::

::: paragraph
A `PropertySource` can define that it supports loading all its
properties (by implementing `LoadablePropertiesSource`) from the source
at once, for example from file system. This allows Camel properties
component to load these properties at once during startup.
:::

::: paragraph
For example the `camel-microprofile-config` component is implemented
using this. The 3rd-party `PropertySource` can automatically be
discovered from classpath when Camel is starting up. This is done by
including the file
`META-INF/services/org/apache/camel/property-source-factory` which
refers to the fully qualified class name of the `PropertySource`
implementation.
:::

::: paragraph
See [MicroProfile Config](components:others:microprofile-config.html)
component as an example.
:::

::: paragraph
You can also register 3rd-party property sources via Java API:
:::

:::: listingblock
::: content
``` highlight
PropertiesComponent pc = context.getPropertiesComponent();
pc.addPropertiesSource(myPropertySource);
```
:::
::::
:::::::::::
::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
