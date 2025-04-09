::: {#header}
# Camel Console
:::

:::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
::::::: {#preamble}
:::::: sectionbody
::: paragraph
The `camel-console` is available from **Camel 3.15** and newer versions.
:::

::: {.admonitionblock .important}
+-----------------------------------+-----------------------------------+
| ::: title                         | The Camel Developer Console is    |
| Important                         | intended assisting developers and |
| :::                               | can display various information   |
|                                   | about a running Camel             |
|                                   | application. This is very handy   |
|                                   | during development and testing.   |
|                                   | However, the Camel Developer      |
|                                   | Console is not recommended for    |
|                                   | production use.                   |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
Camel comes with a set of consoles out of the box from `camel-console`
and `camel-catalog-console` JARs. These consoles can display general
information about the running JVM and the OS Environment, and of course
Camel related information such as runtime metrics of the Camel routes,
and a lot more.
:::
::::::
:::::::

::::::::::::::::::::::::::::::: sect1
## Using Camel Console {#_using_camel_console}

:::::::::::::::::::::::::::::: sectionbody
::: paragraph
The `camel-console` must be added to the classpath, and enabled either
via
:::

:::: listingblock
::: content
``` highlight
CamelContext context = ...
context.setDevConsole(true);
```
:::
::::

::: paragraph
If using Camel Main / Spring Boot / Quarkus etc then the console can be
enabled via configuration:
:::

:::: listingblock
::: content
``` highlight
camel.main.dev-console-enabled = true
```
:::
::::

:::::::::::::::::: sect2
### Dev Console and Camel Spring Boot {#_dev_console_and_camel_spring_boot}

::: paragraph
The Camel developer console is available in Spring Boot as an
*actuator*. To enable the console you need to add dependency:
:::

:::: listingblock
::: content
``` highlight
<dependency>
    <groupId>org.apache.camel.springboot</groupId>
    <artifactId>camel-console-starter</artifactId>
</dependency>
```
:::
::::

::: paragraph
To include more details such as route metrics you need to include JMX
management:
:::

:::: listingblock
::: content
``` highlight
<dependency>
    <groupId>org.apache.camel.springboot</groupId>
    <artifactId>camel-management-starter</artifactId>
</dependency>
```
:::
::::

::: paragraph
And finally you **must** enable `camel` in the exposed list of HTTP
actuator endpoints in `application.properties` as shown:
:::

:::: listingblock
::: content
``` highlight
management.endpoints.web.exposure.include=info,health,camel
```
:::
::::

::: paragraph
The console is then available on HTTP (using default port):
:::

:::: listingblock
::: content
``` highlight
http://localhost:8080/actuator/camel
```
:::
::::

::: paragraph
This will list the available consoles, and you can then call a console
by its id, such as `routes`:
:::

:::: listingblock
::: content
``` highlight
http://localhost:8080/actuator/camel/routes
```
:::
::::
::::::::::::::::::

::::::: sect2
### Dev Console and Camel JBang {#_dev_console_and_camel_jbang}

::: paragraph
The Developer Console is easily available when using [Camel
JBang](camel-jbang.html), by the `--console` argument when running Camel
JBang.
:::

::: paragraph
For example to run a Camel route from `foo.yaml` and additional
configurations from `myapp.properties` you can run as follows and have
the console started and accessible from
[`http://localhost:8080/q/dev`](http://localhost:8080/q/dev){.bare}
:::

:::: listingblock
::: content
``` highlight
$ camel run foo.yaml myapp.properties --console
```
:::
::::
:::::::
::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::

::::::::::::::::::: sect1
## Writing Custom Dev Consoles {#_writing_custom_dev_consoles}

:::::::::::::::::: sectionbody
::: paragraph
To write a custom console, you need to add `camel-console` as
dependency, as it comes with the base class `AbstractDevConsole` which
we extend for our console.
:::

:::: listingblock
::: content
``` highlight
@DevConsole("foo")
public class FooConsole extends AbstractDevConsole {

    public FooConsole() {
        super("acme", "foo", "Foolish", "A foolish console that outputs something");
    }

    @Override
    protected String doCallText(Map<String, Object> options) {
        StringBuilder sb = new StringBuilder();
        sb.append("Hello from my custom console");

        // more stuff here

        return sb.toString();
    }

    @Override
    protected JsonObject doCallJson(Map<String, Object> options) {
        JsonObject root = new JsonObject();
        root.put("message", "Hello from my custom console");

        // more stuff here

        return root;
    }

}
```
:::
::::

::: paragraph
The class must be annotated with `DevConsole` and the unique id of the
console (must be unique across all consoles). In the constructor the
console specifies which group, id, display title, and description to
use.
:::

::: paragraph
The `doCallText` and `doCallJson` methods is responsible for gathering
the information the console should output.
:::

::: paragraph
If the console does not support either text or json output, then the
methods can return `null`, and override the `supportMediaType` method
and return `true` for the media-type that are supported.
:::

::::::: sect2
### Supported Media Types {#_supported_media_types}

::: paragraph
A console can support any of, or all of the following types:
:::

::: ulist
- TEXT

- JSON
:::

::: paragraph
The intention for `TEXT` is to be plain/text based that can be outputted
in CLI and other low-level tools.
:::

::: paragraph
For `JSON` then the intention is the console outputs a json dataset with
key/value pairs that holds the information, which can be displayed in a
custom fashion such as in a web browser, or IDE tool such as VSCode.
:::
:::::::

:::::: sect2
### Maven Configuration {#_maven_configuration}

::: paragraph
To make Camel able to discover custom dev consoles, then the
[came-component-maven-plugin](camel-component-maven-plugin.html) must be
used, such as:
:::

:::: listingblock
::: content
``` highlight
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.camel</groupId>
            <artifactId>camel-component-maven-plugin</artifactId>
            <version>${camel-version}</version>
            <executions>
                <execution>
                    <id>generate</id>
                    <goals>
                        <goal>generate</goal>
                    </goals>
                    <phase>process-classes</phase>
                </execution>
                <execution>
                    <id>generate-postcompile</id>
                    <goals>
                        <goal>generate-postcompile</goal>
                    </goals>
                    <phase>prepare-package</phase>
                </execution>
            </executions>
        </plugin>
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>build-helper-maven-plugin</artifactId>
            <executions>
                <execution>
                    <phase>generate-sources</phase>
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
    </plugins>
</build>
```
:::
::::
::::::
::::::::::::::::::
:::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
