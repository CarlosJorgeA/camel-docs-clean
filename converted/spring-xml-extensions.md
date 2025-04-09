::: {#header}
# Spring XML
:::

:::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
:::::::: {#preamble}
::::::: sectionbody
::: paragraph
Using Camel with Spring XML files, is a classic way, of using XML DSL
with Camel. Camel has historically been using Spring XML for a long
time. The Spring framework started with XML files as a popular and
common configuration for building Spring applications.
:::

::: paragraph
The following is an example of what it looks like:
:::

:::: listingblock
::: content
``` highlight
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://camel.apache.org/schema/spring http://camel.apache.org/schema/spring/camel-spring.xsd
    ">

    <camelContext xmlns="http://camel.apache.org/schema/spring">
        <route>
            <from uri="direct:a"/>
            <choice>
                <when>
                    <xpath>$foo = 'bar'</xpath>
                    <to uri="direct:b"/>
                </when>
                <when>
                    <xpath>$foo = 'cheese'</xpath>
                    <to uri="direct:c"/>
                </when>
                <otherwise>
                    <to uri="direct:d"/>
                </otherwise>
            </choice>
        </route>
    </camelContext>

</beans>
```
:::
::::
:::::::
::::::::

:::::::::::::::: sect1
## Using Spring XML {#_using_spring_xml}

::::::::::::::: sectionbody
::: paragraph
The following dependency needs to be added to your pom.xml so that
Spring XML files can be scanned by Camel:
:::

:::: listingblock
::: content
``` highlight
 <dependency>
     <groupId>org.apache.camel.springboot</groupId>
     <artifactId>camel-spring-boot-xml-starter</artifactId>
 </dependency>
```
:::
::::

::: paragraph
You can use Spring XML files to specify Camel routes using XML DSL as
shown:
:::

:::: listingblock
::: content
``` highlight
<camelContext id="camel-A" xmlns="http://camel.apache.org/schema/spring">
  <route>
    <from uri="seda:start"/>
    <to uri="mock:result"/>
  </route>
</camelContext>
```
:::
::::

:::::::: sect2
### Configuring Components and Endpoints {#_configuring_components_and_endpoints}

::: paragraph
You can configure your Component or [Endpoint](endpoint.html) instances
in your Spring XML as follows in this example.
:::

:::: listingblock
::: content
``` highlight
<camelContext id="camel" xmlns="http://camel.apache.org/schema/spring">
</camelContext>

<bean id="activemq" class="org.apache.camel.component.activemq.ActiveMQComponent">
  <property name="connectionFactory">
    <bean class="org.apache.activemq.ActiveMQConnectionFactory">
      <property name="brokerURL" value="tcp:someserver:61616"/>
    </bean>
  </property>
</bean>
```
:::
::::

::: paragraph
Which allows you to configure a component using any name, but its common
to use the same name eg (`activemq`). Then you can refer to the
component using `activemq:destinationName`.
:::

::: paragraph
This works by the Camel lazily fetching components from the Spring
context for the scheme name you use for Endpoint [URI](uris.html)s.
:::
::::::::
:::::::::::::::
::::::::::::::::

:::::::::::::::::::::::::: sect1
## Using Java DSL with Spring XML files {#_using_java_dsl_with_spring_xml_files}

::::::::::::::::::::::::: sectionbody
::: paragraph
You can use Java Code to define your [RouteBuilder](route-builder.html)
implementations. These can be defined as beans in spring and then
referenced in your camel context e.g.
:::

:::: listingblock
::: content
``` highlight
<camelContext xmlns="http://camel.apache.org/schema/spring">
  <routeBuilder ref="myBuilder"/>
</camelContext>

<bean id="myBuilder" class="org.apache.camel.spring.example.test1.MyRouteBuilder"/>
```
:::
::::

::::::::::::: sect2
### Using package scanning {#_using_package_scanning}

::: paragraph
Camel also provides a powerful feature that allows for the automatic
discovery and initialization of routes in given packages. This is
configured by adding tags to the camel context in your spring context
definition, specifying the packages to be recursively searched for
RouteBuilder implementations. To use this feature in 1.X, requires a
\<package\>\</package\> tag specifying a comma separated list of
packages that should be searched e.g.
:::

:::: listingblock
::: content
``` highlight
<camelContext>
  <packageScan>
    <package>com.foo</package>
    <excludes>**.*Excluded*</excludes>
    <includes>**.*</includes>
  </packageScan>
</camelContext>
```
:::
::::

::: paragraph
This will scan for `RouteBuilder` classes in the *com.foo* and
sub-packages.
:::

::: paragraph
You can also filter the classes with includes or excludes such as:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
  <packageScan>
    <package>com.foo</package>
    <excludes>**.*Special*</excludes>
  </packageScan>
</camelContext>
```
:::
::::

::: paragraph
Which will skip classes that has *Special* in the name.
:::

::: paragraph
Exclude patterns are applied before the include patterns. If no include
or exclude patterns are defined then all the Route classes discovered in
the packages will be returned.
:::

::: paragraph
`?` matches one character `*` matches zero or more characters `**`
matches zero or more segments of a fully qualified name
:::
:::::::::::::

:::::::::: sect2
### Using context scanning {#_using_context_scanning}

::: paragraph
You can allow Camel to scan the container context, e.g. the Spring
ApplicationContext for route builder instances. This allow you to use
the Spring **\<component-scan\>** feature and have Camel pickup any
**`RouteBuilder`** instances which was created by Spring in its scan
process.
:::

:::: listingblock
::: content
``` highlight
<!-- enable Spring @Component scan -->
<context:component-scan base-package="org.apache.camel.spring.issues.contextscan"/>

<camelContext xmlns="http://camel.apache.org/schema/spring">
    <!-- and then let Camel use those @Component scanned route builders -->
    <contextScan/>
</camelContext>
```
:::
::::

::: paragraph
This allows you to just annotate your routes using the Spring
**`@Component`** and have those routes included by Camel:
:::

:::: listingblock
::: content
``` highlight
@Component
public class MyRoute extends RouteBuilder {

    @Override
    public void configure() throws Exception {
        from("direct:start")
            .to("mock:result");
    }
}
```
:::
::::

::: paragraph
You can also use the ANT style for inclusion and exclusion, as mentioned
above in the package scan section.
:::
::::::::::
:::::::::::::::::::::::::
::::::::::::::::::::::::::

::::: sect1
## Additional configuration of Spring XML {#_additional_configuration_of_spring_xml}

:::: sectionbody
::: paragraph
See more details at [Camel Spring XML Auto
Configuration](advanced-configuration-of-camelcontext-using-spring.html).
:::
::::
:::::
::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
