::: {#header}
# Camel Maven Plugin
:::

::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
:::::: {#preamble}
::::: sectionbody
::: paragraph
The Camel Maven Plugin supports the following goals
:::

::: ulist
- camel:run - To run your Camel application

- camel:dev - To run your Camel application in developer mode

- camel:debug - To run your Camel application in debug mode

- camel:prepare-fatjar - To prepare your Camel application for being
  packaged as a fat-jar (such as by maven-assembly-plugin)
:::
:::::
::::::

:::::::::::::::::::::: sect1
## camel:run {#_camelrun}

::::::::::::::::::::: sectionbody
::: paragraph
The `camel:run` goal of the Camel Maven Plugin is used to run your Camel
Spring configurations in a forked JVM from Maven. A good example
application to get you started is the Spring Example.
:::

:::: listingblock
::: content
    cd examples/camel-example-spring
    mvn camel:run
:::
::::

::: paragraph
This makes it very easy to spin up and test your routing rules without
having to write a main(...​) method; it also lets you create multiple
jars to host different sets of routing rules and easily test them
independently.
:::

::: paragraph
How this works is that the plugin will compile the source code in the
maven project, then boot up a Spring ApplicationContext using the XML
configuration files on the classpath at `META-INF/spring/*.xml`
:::

::: paragraph
If you want to boot up your Camel routes a little faster, you could try
the `camel:embedded` instead.
:::

:::: sect2
### Options {#_options}

::: paragraph
The maven plugin **run** goal supports the following options which can
be configured from the command line (use `-D` syntax), or defined in the
`pom.xml` file in the `<configuration>` tag.
:::

+----------------------+----------------------+-----------------------+
| Parameter            | Default Value        | Description           |
+----------------------+----------------------+-----------------------+
| duration             | -1                   | Sets the time         |
|                      |                      | duration (seconds)    |
|                      |                      | that the application  |
|                      |                      | will run for before   |
|                      |                      | terminating. A value  |
|                      |                      | ⇐ 0 will run forever. |
+----------------------+----------------------+-----------------------+
| durationIdle         | -1                   | Sets the idle time    |
|                      |                      | duration (seconds)    |
|                      |                      | duration that the     |
|                      |                      | application can be    |
|                      |                      | idle before           |
|                      |                      | terminating. A value  |
|                      |                      | ⇐ 0 will run forever. |
+----------------------+----------------------+-----------------------+
| durationMaxMessages  | -1                   | Sets the duration of  |
|                      |                      | maximum number of     |
|                      |                      | messages that the     |
|                      |                      | application will      |
|                      |                      | process before        |
|                      |                      | terminating.          |
+----------------------+----------------------+-----------------------+
| logClasspath         | false                | Whether to log the    |
|                      |                      | classpath when        |
|                      |                      | starting              |
+----------------------+----------------------+-----------------------+
| loggingLevel         | OFF                  | Whether to use        |
|                      |                      | built-in console      |
|                      |                      | logging (uses log4j), |
|                      |                      | which does not        |
|                      |                      | require to add any    |
|                      |                      | logging dependency to |
|                      |                      | your project.         |
|                      |                      | However, the logging  |
|                      |                      | is fixed to log to    |
|                      |                      | the console, with a   |
|                      |                      | color style that is   |
|                      |                      | similar to Spring     |
|                      |                      | Boot. You can change  |
|                      |                      | the root logging      |
|                      |                      | level to: FATAL,      |
|                      |                      | ERROR, WARN, INFO,    |
|                      |                      | DEBUG, TRACE, OFF     |
+----------------------+----------------------+-----------------------+
| profile              |                      | To run with a         |
|                      |                      | specific Camel Main   |
|                      |                      | profile               |
|                      |                      | (dev,test,prod)       |
+----------------------+----------------------+-----------------------+
::::

:::::: sect2
### Logging the classpath {#_logging_the_classpath}

::: paragraph
You can configure whether the classpath should be logged when
`camel:run` executes. You can enable this in the configuration using:
:::

:::: listingblock
::: content
``` highlight
<plugin>
  <groupId>org.apache.camel</groupId>
  <artifactId>camel-maven-plugin</artifactId>
  <configuration>
    <logClasspath>true</logClasspath>
  </configuration>
</plugin>
```
:::
::::
::::::

:::::::: sect2
### Using built-in logging {#_using_built_in_logging}

::: paragraph
If you want quickly to have logging to console, you can use the built-in
logging by setting the logging level as shown:
:::

:::: listingblock
::: content
``` highlight
<plugin>
  <groupId>org.apache.camel</groupId>
  <artifactId>camel-maven-plugin</artifactId>
  <configuration>
    <loggingLevel>INFO</loggingLevel>
  </configuration>
</plugin>
```
:::
::::

::: paragraph
This runs the application with console logging, in color that is similar
to Spring Boot logging. This is default turned off, to use the
configured logging system in the project.
:::

::: paragraph
The idea with the built-in logging is that you sometimes want to avoid
messing with setting up logging, and just want a quick and easy log to
console that looks good.
:::
::::::::
:::::::::::::::::::::
::::::::::::::::::::::

::::::: sect1
## camel:dev {#_cameldev}

:::::: sectionbody
::: paragraph
The `camel:dev` is an extension to `camel:run` to run the Camel
application in developer mode. In this mode, among others, Camel will
use hot-reloading of DSL routes (xml, yaml and java) that are located
from the `src/main/resources` directory.
:::

:::: sect2
### Options {#_options_2}

::: paragraph
The maven plugin **dev** goal supports the following options which can
be configured from the command line (use `-D` syntax), or defined in the
`pom.xml` file in the `<configuration>` tag.
:::

+----------------------+----------------------+-----------------------+
| Parameter            | Default Value        | Description           |
+----------------------+----------------------+-----------------------+
| routesDirectory      | src/main/resources   | To watch the          |
|                      |                      | directory for file    |
|                      |                      | changes which         |
|                      |                      | triggers a live       |
|                      |                      | reload of the Camel   |
|                      |                      | routes on-the-fly.    |
+----------------------+----------------------+-----------------------+
| duration             | -1                   | Sets the time         |
|                      |                      | duration (seconds)    |
|                      |                      | that the application  |
|                      |                      | will run for before   |
|                      |                      | terminating. A value  |
|                      |                      | ⇐ 0 will run forever. |
+----------------------+----------------------+-----------------------+
| durationIdle         | -1                   | Sets the idle time    |
|                      |                      | duration (seconds)    |
|                      |                      | duration that the     |
|                      |                      | application can be    |
|                      |                      | idle before           |
|                      |                      | terminating. A value  |
|                      |                      | ⇐ 0 will run forever. |
+----------------------+----------------------+-----------------------+
| durationMaxMessages  | -1                   | Sets the duration of  |
|                      |                      | maximum number of     |
|                      |                      | messages that the     |
|                      |                      | application will      |
|                      |                      | process before        |
|                      |                      | terminating.          |
+----------------------+----------------------+-----------------------+
| logClasspath         | false                | Whether to log the    |
|                      |                      | classpath when        |
|                      |                      | starting              |
+----------------------+----------------------+-----------------------+
| loggingLevel         | OFF                  | Whether to use        |
|                      |                      | built-in console      |
|                      |                      | logging (uses log4j), |
|                      |                      | which does not        |
|                      |                      | require to add any    |
|                      |                      | logging dependency to |
|                      |                      | your project.         |
|                      |                      | However, the logging  |
|                      |                      | is fixed to log to    |
|                      |                      | the console, with a   |
|                      |                      | color style that is   |
|                      |                      | similar to Spring     |
|                      |                      | Boot. You can change  |
|                      |                      | the root logging      |
|                      |                      | level to: FATAL,      |
|                      |                      | ERROR, WARN, INFO,    |
|                      |                      | DEBUG, TRACE, OFF     |
+----------------------+----------------------+-----------------------+
| profile              | dev                  | To run with a         |
|                      |                      | specific Camel Main   |
|                      |                      | profile               |
|                      |                      | (dev,test,prod)       |
+----------------------+----------------------+-----------------------+
::::
::::::
:::::::

::::::: sect1
## camel:debug {#_cameldebug}

:::::: sectionbody
::: paragraph
The `camel:debug` is an extension to `camel:dev` to run the Camel
application in debug mode which allows to debug the Camel routes thanks
to the Camel textual route debugger.
:::

:::: sect2
### Options {#_options_3}

::: paragraph
The maven plugin **debug** goal supports the following options which can
be configured from the command line (use `-D` syntax), or defined in the
`pom.xml` file in the `<configuration>` tag.
:::

+----------------------+----------------------+-----------------------+
| Parameter            | Default Value        | Description           |
+----------------------+----------------------+-----------------------+
| suspend              | true                 | Indicates whether the |
|                      |                      | message processing    |
|                      |                      | done by Camel should  |
|                      |                      | be suspended as long  |
|                      |                      | as a debugger is not  |
|                      |                      | attached.             |
+----------------------+----------------------+-----------------------+
::::
::::::
:::::::

:::::::::::::: sect1
## camel:prepare-fatjar {#_camelprepare_fatjar}

::::::::::::: sectionbody
::: paragraph
The `camel:prepare-fatjar` goal of the Camel Maven Plugin is used to
prepare your Camel application for being packaged as a *fat jar*. The
goal scans the Maven dependencies to discover Camel JARs and extract if
they have type converters, which gets merged together into a single
*uber* file stored in
`target/classes/META-INF/services/org/apache/camel/UberTypeConverterLoader`.
:::

::: paragraph
This *uber* loader file contains all the combined type converters the
Camel application uses at runtime. They are merged together into this
single file.
:::

::: paragraph
This is needed as otherwise the *fat jar* maven plugins (such as
maven-assembly-plugin, or maven-shade-plugin) causes the
`TypeConverterLoader` files to be overwritten in the assembled JAR which
causes not all type converters to be loaded by Camel.
:::

::: paragraph
The `UberTypeConverterLoader` ensures they all type converters gets
loaded as this file contains all the known type converter files.
:::

::: paragraph
To use this goal, you can add the following to your Camel application
`pom.xml` file:
:::

:::: listingblock
::: content
``` highlight
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.camel</groupId>
        <artifactId>camel-maven-plugin</artifactId>
        <version>${camel.version}</version>
        <executions>
          <execution>
            <goals>
              <goal>prepare-fatjar</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```
:::
::::

::: paragraph
For example to use this with the `maven-assembly-plugin` you can do as
below. Remember to specify the class name of **your** main class where
it says `com.foo.NameOfMainClass`:
:::

:::: listingblock
::: content
``` highlight
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.camel</groupId>
        <artifactId>camel-maven-plugin</artifactId>
        <version>${camel.version}</version>
        <executions>
          <execution>
            <goals>
              <goal>prepare-fatjar</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-assembly-plugin</artifactId>
        <configuration>
          <archive>
            <manifest>
              <mainClass>com.foo.NameOfMainClass</mainClass>
            </manifest>
          </archive>
        </configuration>
      </plugin>
    </plugins>
  </build>
```
:::
::::
:::::::::::::
::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
