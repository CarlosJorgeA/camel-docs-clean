::: {#header}
# Apache Camel 3.x Upgrade Guide
:::

::::::::::::::::::::::::::::::::::::::::: {#content}
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

::::::::::::::::::::::::::::::::::::: sect1
## Upgrading Camel 3.4 to 3.5 {#_upgrading_camel_3_4_to_3_5}

:::::::::::::::::::::::::::::::::::: sectionbody
::::: sect2
### FluentProducerTemplate {#_fluentproducertemplate}

::: paragraph
The template will now automatic clear its state when sending the
message, this avoids end users having to call `clearAll` after usage, in
case the template should be reused to send other messages.
:::

::: paragraph
After the template has been started / used the first time, then its
general configuration cannot be altered later, instead create a new
template.
:::
:::::

:::: sect2
### PackageScanClassResolver {#_packagescanclassresolver}

::: paragraph
The `PackageScanClassResolver` will now skip abstract classes in its
`findImplementations` method.
:::
::::

:::: sect2
### camel-bean {#_camel_bean}

::: paragraph
The `bean(class)` EIP will now lookup in the registry first whether
there is a single bean instance of the given class type and use the
existing bean (for singleton scope) instead of creating a new instance.
:::
::::

:::::: sect2
### camel-cassandraql {#_camel_cassandraql}

::: paragraph
Load-balancing policy has changed. There are no existing load balancing
policies (see [upgrade
guide](https://docs.datastax.com/en/developer/java-driver/4.3/upgrade_guide/#load-balancing-policy)).
To customize load balancing policy, please use the parameter
`loadBalancingPolicyClass` and provide own implementation of load
balancing policy.
:::

::: paragraph
Session impelentation class has changed from
`com.datastax.driver.core.Session` to
`com.datastax.oss.driver.api.core.CqlSession` (see [upgrade
guide](https://docs.datastax.com/en/developer/java-driver/4.3/upgrade_guide/#session)).
This could have direct impact on the type of parameter `beanRef` if you
are using `Provided Session reference`.
:::

::: paragraph
There is a new parameter `datacenter` (with default value
`datacenter1`). Data center has to be defined for the proper behavior of
the default load balancing policy.
:::
::::::

:::: sect2
### camel-sjms {#_camel_sjms}

::: paragraph
The default value for `keepAliveDelay` was changed from `-1` to `5000`
in the batch jms consumer.
:::
::::

::::: sect2
### camel-weather {#_camel_weather}

::: paragraph
The option `httpClient` is now a query parameter.
:::

::: paragraph
A new parameter `geoLocationProvider` was introduced to allow the use of
a custom `GeolocationProvider` implementation.
:::
:::::

:::: sect2
### Camel Karaf {#_camel_karaf}

::: paragraph
The following feature has been removed due to no longer being compatible
with OSGi: `camel-couchbase`.
:::
::::

::::: sect2
### LifecycleStrategy {#_lifecyclestrategy}

::: paragraph
The `LifecycleStrategy` has been enhanced and provides some additional
methods and changes in the behavior:
:::

::: ulist
- **onContextInitializing**: is invoked when the context is about to be
  initialized, this was previopusly achieved by `onContextInitialized`
  but as the name was a misleading

- **onContextInitialized**: is now invoked when the context is
  initialized

- **onContextStarting**: is invoked when the context is about to start,
  this was previously achieved by the `onContextStart` which is now
  deprecated.

- **onContextStarted**: is now invoked when the context is started

- **onContextStopping**: is invoked when the context is about to be
  stopped, this was previously achieved by the `onContextStop` which is
  now deprecated.

- **onContextStop**: is now invoked when the context is stopped
:::
:::::

:::::: sect2
### JUnit 5 testing {#_junit_5_testing}

::: paragraph
Camel has been migrated from JUnit 4 to JUnit 5 as our default testing
in all the components. The new test components reflect this with the
following JAR names:
:::

::: ulist
- camel-test-junit5

- camel-test-spring-junit5

- camel-testcontainers-junit5

- camel-testcontainers-spring-junit5
:::

::: paragraph
The older test components supports only JUnit 4.
:::
::::::

:::::::::: sect2
### Spring Boot testing with JUnit 5 {#_spring_boot_testing_with_junit_5}

::: paragraph
Testing Camel on Spring Boot with JUnit 5 have also changed slightly to
use JUnit 5 APIs and testing style.
:::

::: paragraph
The following set of dependencies can be used:
:::

:::: listingblock
::: content
``` highlight
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-test-spring-junit5</artifactId>
    <scope>test</scope>
</dependency>
```
:::
::::

::: paragraph
Then in your test classes you should annotate the Spring Boot test class
with the new `@CamelSpringBootTest` (from package
`org.apache.camel.test.spring.junit5.CamelSpringBootTest` in
camel-test-spring-junit5 JAR) annotation as shown:
:::

:::: listingblock
::: content
``` highlight
@CamelSpringBootTest
@SpringBootTest(classes = MyCamelApplication.class)
public class MyCamelApplicationJUnit5Test {

    @Autowired
    private CamelContext camelContext;

    @Test
    public void shouldProduceMessages() throws Exception {
        // here you can write your unit test
    }

}
```
:::
::::
::::::::::
::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
