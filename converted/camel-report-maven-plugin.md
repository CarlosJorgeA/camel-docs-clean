::: {#header}
# Camel Report Maven Plugin
:::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
:::::: {#preamble}
::::: sectionbody
::: paragraph
The Camel Report Maven Plugin supports the following goals
:::

::: ulist
- camel-report:validate - To validate your source code for invalid Camel
  endpoint uris

- camel-report:route-coverage - To report the coverage of your Camel
  routes after unit testing
:::
:::::
::::::

::::::::::::::::::::::::::::::::::::::::::::::: sect1
## camel-report:validate {#_camel_reportvalidate}

:::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
For validating the source code for mis-configured Camel:
:::

::: ulist
- endpoint uris

- simple expressions or predicates

- duplicate route ids

- seda/direct endpoint pairs

- configuration in properties files
:::

::: paragraph
Then you can run the `validate` goal from the command line or from
within your Java editor such as IDEA or Eclipse.
:::

:::: listingblock
::: content
    mvn camel-report:validate
:::
::::

::: paragraph
You can also enable the plugin to run automatically as part of the build
to catch these errors.
:::

:::: listingblock
::: content
``` highlight
<plugin>
  <groupId>org.apache.camel</groupId>
  <artifactId>camel-report-maven-plugin</artifactId>
  <executions>
    <execution>
      <phase>process-classes</phase>
      <goals>
        <goal>validate</goal>
      </goals>
    </execution>
  </executions>
</plugin>
```
:::
::::

::: paragraph
The phase determines when the plugin runs. In the sample above the phase
is `process-classes` which runs after the compilation of the main source
code.
:::

::: paragraph
The maven plugin can also be configured to validate the test source code
, which means that the phase should be changed accordingly to
`process-test-classes` as shown below:
:::

:::: listingblock
::: content
``` highlight
<plugin>
  <groupId>org.apache.camel</groupId>
  <artifactId>camel-report-maven-plugin</artifactId>
  <executions>
    <execution>
      <configuration>
        <includeTest>true</includeTest>
      </configuration>
      <phase>process-test-classes</phase>
      <goals>
        <goal>validate</goal>
      </goals>
    </execution>
  </executions>
</plugin>
```
:::
::::

:::::::::::::::::: sect2
### Running the goal on any Maven project {#_running_the_goal_on_any_maven_project}

::: paragraph
You can also run the validate goal on any Maven project without having
to add the plugin to the `pom.xml` file. Doing so requires to specify
the plugin using its fully qualified name. For example to run the goal
on the `camel-example-cdi` from Apache Camel you can run
:::

:::: listingblock
::: content
    $cd camel-example-cdi
    $mvn org.apache.camel:camel-report-maven-plugin:2.20.0:validate
:::
::::

::: paragraph
which then runs and outputs the following:
:::

:::: listingblock
::: content
    [INFO] ------------------------------------------------------------------------
    [INFO] Building Camel :: Example :: CDI 3.0.0
    [INFO] ------------------------------------------------------------------------
    [INFO]
    [INFO] --- camel-report-maven-plugin:3.0.0:validate (default-cli) @ camel-example-cdi ---
    [INFO] Endpoint validation success: (4 = passed, 0 = invalid, 0 = incapable, 0 = unknown components)
    [INFO] Simple validation success: (0 = passed, 0 = invalid)
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
:::
::::

::: paragraph
The validation passed, and 4 endpoints was validated. Now suppose we
made a typo in one of the Camel endpoint uris in the source code, such
as:
:::

:::: listingblock
::: content
``` highlight
@Uri("timer:foo?period=5000")
```
:::
::::

::: paragraph
is changed to include a typo error in the `period` option
:::

:::: listingblock
::: content
``` highlight
@Uri("timer:foo?perid=5000")
```
:::
::::

::: paragraph
And when running the `validate` goal again reports the following:
:::

:::: listingblock
::: content
    [INFO] ------------------------------------------------------------------------
    [INFO] Building Camel :: Example :: CDI 3.0.0
    [INFO] ------------------------------------------------------------------------
    [INFO]
    [INFO] --- camel-report-maven-plugin:3.0.0:validate (default-cli) @ camel-example-cdi ---
    [WARNING] Endpoint validation error at: org.apache.camel.example.cdi.MyRoutes(MyRoutes.java:32)

        timer:foo?perid=5000

                           perid    Unknown option. Did you mean: [period]


    [WARNING] Endpoint validation error: (3 = passed, 1 = invalid, 0 = incapable, 0 = unknown components)
    [INFO] Simple validation success: (0 = passed, 0 = invalid)
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
:::
::::
::::::::::::::::::

:::::::: sect2
### Options {#_options}

::: paragraph
The maven plugin **validate** goal supports the following options which
can be configured from the command line (use `-D` syntax), or defined in
the `pom.xml` file in the `<configuration>` tag.
:::

+----------------------+----------------------+-----------------------+
| Parameter            | Default Value        | Description           |
+----------------------+----------------------+-----------------------+
| downloadVersion      | true                 | Whether to allow      |
|                      |                      | downloading Camel     |
|                      |                      | catalog version from  |
|                      |                      | the internet. This is |
|                      |                      | needed if the project |
|                      |                      | uses a different      |
|                      |                      | Camel version than    |
|                      |                      | this plugin is using  |
|                      |                      | by default.           |
+----------------------+----------------------+-----------------------+
| failOnError          | false                | Whether to fail if    |
|                      |                      | invalid Camel         |
|                      |                      | endpoints was found.  |
|                      |                      | By default the plugin |
|                      |                      | logs the errors at    |
|                      |                      | WARN level.           |
+----------------------+----------------------+-----------------------+
| coverageThreshold    | 100                  | The minimum route     |
|                      |                      | coverage in percent   |
|                      |                      | when using            |
|                      |                      | failOnError.          |
+----------------------+----------------------+-----------------------+
| over                 | 0                    | The minimum coverage  |
| allCoverageThreshold |                      | across all routes in  |
|                      |                      | percent when using    |
|                      |                      | failOnError.          |
+----------------------+----------------------+-----------------------+
| logUnparseable       | false                | Whether to log        |
|                      |                      | endpoint URIs which   |
|                      |                      | was un-parsable and   |
|                      |                      | therefore not         |
|                      |                      | possible to validate. |
+----------------------+----------------------+-----------------------+
| includeJava          | true                 | Whether to include    |
|                      |                      | Java files to be      |
|                      |                      | validated for invalid |
|                      |                      | Camel endpoints.      |
+----------------------+----------------------+-----------------------+
| includeXml           | true                 | Whether to include    |
|                      |                      | XML files to be       |
|                      |                      | validated for invalid |
|                      |                      | Camel endpoints.      |
+----------------------+----------------------+-----------------------+
| includeTest          | false                | Whether to include    |
|                      |                      | test source code.     |
+----------------------+----------------------+-----------------------+
| includes             |                      | To filter the names   |
|                      |                      | of java and xml files |
|                      |                      | to only include files |
|                      |                      | matching any of the   |
|                      |                      | given list of         |
|                      |                      | patterns (wildcard    |
|                      |                      | and regular           |
|                      |                      | expression). Multiple |
|                      |                      | values can be         |
|                      |                      | separated by comma.   |
+----------------------+----------------------+-----------------------+
| excludes             |                      | To filter the names   |
|                      |                      | of java and xml files |
|                      |                      | to exclude files      |
|                      |                      | matching any of the   |
|                      |                      | given list of         |
|                      |                      | patterns (wildcard    |
|                      |                      | and regular           |
|                      |                      | expression). Multiple |
|                      |                      | values can be         |
|                      |                      | separated by comma.   |
+----------------------+----------------------+-----------------------+
| ig                   | true                 | Whether to ignore     |
| noreUnknownComponent |                      | unknown components.   |
+----------------------+----------------------+-----------------------+
| ignoreIncapable      | true                 | Whether to ignore     |
|                      |                      | incapable of parsing  |
|                      |                      | the endpoint uri or   |
|                      |                      | simple expression.    |
+----------------------+----------------------+-----------------------+
| ign                  | true                 | Whether to ignore     |
| oreLenientProperties |                      | components that uses  |
|                      |                      | lenient properties.   |
|                      |                      | When this is true,    |
|                      |                      | then the uri          |
|                      |                      | validation is         |
|                      |                      | stricter but would    |
|                      |                      | fail on properties    |
|                      |                      | that are not part of  |
|                      |                      | the component but in  |
|                      |                      | the uri because of    |
|                      |                      | using lenient         |
|                      |                      | properties. For       |
|                      |                      | example using the     |
|                      |                      | HTTP components to    |
|                      |                      | provide query         |
|                      |                      | parameters in the     |
|                      |                      | endpoint uri.         |
+----------------------+----------------------+-----------------------+
| ignoreDeprecated     | true                 | Whether to ignore     |
|                      |                      | deprecated options    |
|                      |                      | being used in the     |
|                      |                      | endpoint uri.         |
+----------------------+----------------------+-----------------------+
| duplicateRouteId     | true                 | Whether to validate   |
|                      |                      | for duplicate route   |
|                      |                      | ids. Route ids should |
|                      |                      | be unique and if      |
|                      |                      | there are duplicates  |
|                      |                      | then Camel will fail  |
|                      |                      | to startup.           |
+----------------------+----------------------+-----------------------+
| d                    | true                 | Whether to validate   |
| irectOrSedaPairCheck |                      | direct/seda endpoints |
|                      |                      | sending to non        |
|                      |                      | existing consumers.   |
+----------------------+----------------------+-----------------------+
| configurationFiles   | ap                   | Location of           |
|                      | plication.properties | configuration files   |
|                      |                      | to validate. The      |
|                      |                      | default is            |
|                      |                      | ap                    |
|                      |                      | plication.properties. |
|                      |                      | Multiple values can   |
|                      |                      | be separated by comma |
|                      |                      | and use wildcard      |
|                      |                      | pattern matching.     |
+----------------------+----------------------+-----------------------+
| showAll              | false                | Whether to show all   |
|                      |                      | endpoints and simple  |
|                      |                      | expressions (both     |
|                      |                      | invalid and valid).   |
+----------------------+----------------------+-----------------------+
| downloa              | false                | When sourcesArtifacts |
| dTransitiveArtifacts |                      | are declared, this    |
|                      |                      | flag can be used to   |
|                      |                      | download transitive   |
|                      |                      | dependencies,         |
|                      |                      | carefully enable this |
|                      |                      | flag since it will    |
|                      |                      | try to download the   |
|                      |                      | whole dependency      |
|                      |                      | tree.                 |
+----------------------+----------------------+-----------------------+
| sourcesArtifacts     |                      | List of sources       |
|                      |                      | transitive            |
|                      |                      | dependencies that     |
|                      |                      | contain camel routes, |
|                      |                      | this option can be    |
|                      |                      | used to download      |
|                      |                      | extra dependencies    |
|                      |                      | that contain camel    |
|                      |                      | route that your       |
|                      |                      | project may depend    |
|                      |                      | on.                   |
+----------------------+----------------------+-----------------------+
| ex                   |                      | List of extra maven   |
| traMavenRepositories |                      | repositories.         |
+----------------------+----------------------+-----------------------+

::: paragraph
For example to turn on ignoring usage of deprecated options from the
command line, you can run:
:::

:::: listingblock
::: content
    $mvn camel-report:validate -Dcamel.ignoreDeprecated=true
:::
::::

::: paragraph
Notice that you must prefix the `-D` command argument with `camel.`, eg
`camel.ignoreDeprecated` as the option name.
:::
::::::::

:::::: sect2
### Validating include test {#_validating_include_test}

::: paragraph
If you have a Maven project then you can run the plugin to validate the
endpoints in the unit test source code as well. You can pass in the
options using `-D` style as shown:
:::

:::: listingblock
::: content
    $cd myproject
    $mvn org.apache.camel:camel-report-maven-plugin:3.0.0:validate -Dcamel.includeTest=true
:::
::::
::::::

::::::: sect2
### Validate Apache Camel routes in transitive dependencies {#_validate_apache_camel_routes_in_transitive_dependencies}

::: paragraph
If your routes use `direct` or `seda` endpoints that are not present in
the current project, but the routes are declared into a dependency of
your project, you can edit the plugin configuration accordingly so that
these routes can be taken into account by the Camel validate plugin. In
particular, in order to use the validate plugin with transitive
dependencies, **sources jars are needed**, for example:
:::

::: ulist
- Given the following Camel route `from("direct:in").to("direct:out")`
  defined in the current project

- The route `from("direct:out")` is declared into a dependency of your
  project, for example `my.company:routes-dependency:1.0`

- If `routes-dependency` sources are released into a maven repository,
  the following plugin configuration can be used:
:::

:::: listingblock
::: content
``` highlight
<plugin>
  <groupId>org.apache.camel</groupId>
  <artifactId>camel-report-maven-plugin</artifactId>
  <executions>
    <execution>
      <phase>package</phase>
      <goals>
        <goal>validate</goal>
      </goals>
      <configuration>
        <sourcesArtifacts>
          <sourcesArtifact>my.company:routes-dependency:jar:sources:1.0</sourcesArtifact>
        </sourcesArtifacts>
        <extraMavenRepositories>
          <extraMavenRepository>http://internal.repo:8080/maven</extraMavenRepository>
        </extraMavenRepositories>
      </configuration>
    </execution>
  </executions>
</plugin>
```
:::
::::
:::::::
::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::::::::: sect1
## camel-report:route-coverage {#_camel_reportroute_coverage}

::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
For generating a report of the coverage of your Camel routes from unit
testing. In the same manner you can generate Java code coverage reports,
then this is the same but for Camel routes. You can therefore use this
to know which parts of your Camel routes have been used or not.
:::

::: paragraph
Camel uses JMX to capture metrics during tests which are used for
calculating route coverage. You must have `camel-management` JAR on the
classpath (can be test scoped) such as by declaring the following Maven
dependency in your pom.xml file:
:::

:::: listingblock
::: content
``` highlight
<!-- JMX is needed for route coverage testing -->
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-management</artifactId>
    <scope>test</scope>
</dependency>
```
:::
::::

::::::::::::::::::::::::::: sect2
### Enabling route coverage {#_enabling_route_coverage}

::: paragraph
You can enable route coverage while running unit tests either by:
:::

::: ulist
- setting global JVM system property enabling for all test classes

- using `@EnableRouteCoverage` annotation per test class if using
  `camel-test-spring-junit5` module

- overriding `isDumpRouteCoverage` method per test class if using
  `camel-test` module
:::

::::::::: sect3
#### Enabling via JVM system property {#_enabling_via_jvm_system_property}

::: paragraph
You can turn on the JVM system property `CamelTestRouteCoverage` to
enable route coverage for all tests cases. This can be done either in
the configuration of the `maven-surefire-plugin`:
:::

:::: listingblock
::: content
``` highlight
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-surefire-plugin</artifactId>
  <configuration>
    <systemPropertyVariables>
      <CamelTestRouteCoverage>true</CamelTestRouteCoverage>
    </systemPropertyVariables>
  </configuration>
</plugin>
```
:::
::::

::: paragraph
Or from the command line when running tests:
:::

:::: listingblock
::: content
    mvn clean test -DCamelTestRouteCoverage=true
:::
::::
:::::::::

:::::: sect3
#### Enabling via \@EnableRouteCoverage annotation {#_enabling_via_enableroutecoverage_annotation}

::: paragraph
You need to enable route coverage in the unit tests classes. You can do
this by adding the `@EnableRouteCoverage` annotation to the test class
if you are testing using `camel-test-spring-junit5`:
:::

:::: listingblock
::: content
``` highlight
@CamelSpringBootTest
@SpringBootTest(classes = SampleCamelApplication.class)
@EnableRouteCoverage
public class FooApplicationTest {
```
:::
::::
::::::

::::::::::::: sect3
#### Enabling via isDumpRouteCoverage method {#_enabling_via_isdumproutecoverage_method}

::: paragraph
However if you are using `camel-test` and your unit tests are extending
`CamelTestSupport` then you can turn on route coverage as shown:
:::

:::: listingblock
::: content
``` highlight
@Override
public boolean isDumpRouteCoverage() {
    return true;
}
```
:::
::::

::: {.admonitionblock .important}
+-----------------------------------+-----------------------------------+
| ::: title                         | Routes that can be route          |
| Important                         | coveraged **MUST** have a unique  |
| :::                               | id assigned, in other words you   |
|                                   | cannot use anonymous routes.      |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
You do this using `routeId` in Java DSL:
:::

:::: listingblock
::: content
``` highlight
from("jms:queue:cheese").routeId("cheesy")
  .to("log:foo")
  ...
```
:::
::::

::: paragraph
And in XML DSL you just assign the route id via the id attribute
:::

:::: listingblock
::: content
``` highlight
<route id="cheesy">
  <from uri="jms:queue:cheese"/>
  <to uri="log:foo"/>
  ...
</route>
```
:::
::::
:::::::::::::
:::::::::::::::::::::::::::

::::::::::::: sect2
### Generating route coverage report {#_generating_route_coverage_report}

::: paragraph
After unit testing with:
:::

:::: listingblock
::: content
    mvn test
:::
::::

::: paragraph
You can then run the goal to report the route coverage
:::

:::: listingblock
::: content
    mvn camel-report:route-coverage
:::
::::

::: paragraph
Which then reports which routes has missing route coverage with precise
source code line reporting:
:::

:::: listingblock
::: content
    [INFO] --- camel-camel-report-plugin:3.0.0:route-coverage (default-cli) @ camel-example-spring-boot-xml ---
    [INFO] Discovered 1 routes
    [INFO] Route coverage summary:

    File:   src/main/resources/my-camel.xml
    RouteId:    hello

      Line #      Count   Route
      ------      -----   -----
          28          1   from
          29          1     transform
          32          1     filter
          34          0       to
          36          1     to

    Coverage: 4 out of 5 (80.0%)
:::
::::

::: paragraph
Here we can see that the 2nd last line with `to` has `0` in the count
column, and therefore is not covered. We can also see that this is one
line 34 in the source code file, which is in the `my-camel.xml` XML
file.
:::
:::::::::::::

:::: sect2
### Options {#_options_2}

::: paragraph
The maven plugin **coverage** goal supports the following options which
can be configured from the command line (use `-D` syntax), or defined in
the `pom.xml` file in the `<configuration>` tag.
:::

+----------------------+----------------------+-----------------------+
| Parameter            | Default Value        | Description           |
+----------------------+----------------------+-----------------------+
| failOnError          | false                | Whether to fail if    |
|                      |                      | any of the routes has |
|                      |                      | not 100% coverage.    |
+----------------------+----------------------+-----------------------+
| includeTest          | false                | Whether to include    |
|                      |                      | test source code.     |
+----------------------+----------------------+-----------------------+
| includes             |                      | To filter the names   |
|                      |                      | of java and xml files |
|                      |                      | to only include files |
|                      |                      | matching any of the   |
|                      |                      | given list of         |
|                      |                      | patterns (wildcard    |
|                      |                      | and regular           |
|                      |                      | expression). Multiple |
|                      |                      | values can be         |
|                      |                      | separated by comma.   |
+----------------------+----------------------+-----------------------+
| excludes             |                      | To filter the names   |
|                      |                      | of java and xml files |
|                      |                      | to exclude files      |
|                      |                      | matching any of the   |
|                      |                      | given list of         |
|                      |                      | patterns (wildcard    |
|                      |                      | and regular           |
|                      |                      | expression). Multiple |
|                      |                      | values can be         |
|                      |                      | separated by comma.   |
+----------------------+----------------------+-----------------------+
| anonymousRoutes      | false                | **Deprecated**        |
|                      |                      | Whether to allow      |
|                      |                      | anonymous routes      |
|                      |                      | (routes without any   |
|                      |                      | route id assigned).   |
|                      |                      | By using route id's   |
|                      |                      | then it is safer to   |
|                      |                      | match the route cover |
|                      |                      | data with the route   |
|                      |                      | source code.          |
|                      |                      | Anonymous routes are  |
|                      |                      | less safe to use for  |
|                      |                      | route coverage as its |
|                      |                      | harder to know        |
|                      |                      | exactly which route   |
|                      |                      | that was tested       |
|                      |                      | corresponds to which  |
|                      |                      | of the routes from    |
|                      |                      | the source code.      |
+----------------------+----------------------+-----------------------+
| gen                  | false                | Whether to generate a |
| erateJacocoXmlReport |                      | coverage-report in    |
|                      |                      | Jacoco XML format.    |
|                      |                      | When enabled it will  |
|                      |                      | generate the file:    |
|                      |                      | `target/site/         |
|                      |                      | jacoco/xmlJacoco.xml` |
+----------------------+----------------------+-----------------------+
| generateHtmlReport   | false                | Whether to generate a |
|                      |                      | coverage-report in    |
|                      |                      | HTML format. When     |
|                      |                      | enabled it will       |
|                      |                      | generate the file:    |
|                      |                      | `t                    |
|                      |                      | arget/site/route-cove |
|                      |                      | rage/html/index.html` |
+----------------------+----------------------+-----------------------+
::::
:::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
