::: {#header}
# Camel Component Maven Plugin
:::

:::::::::::::::::::::::::::::::::: {#content}
::::: {#preamble}
:::: sectionbody
::: paragraph
The [Camel Component Maven
Plugin](https://github.com/apache/camel/tree/main/tooling/maven/camel-component-maven-plugin)
allows third party component developers to generate all the necessary
metadata and Java classes for configurations to be used since Camel 3.x.
These metadata files and Java files allows for more efficiency and
faster runs for your component.
:::
::::
:::::

::::::::: sect1
## Goals Supported {#_goals_supported}

:::::::: sectionbody
::: paragraph
The following goal is supported:
:::

:::::: sect2
### generate {#_generate}

::: paragraph
This goal will generate the following metadata files and Java files:
:::

::: ulist
- Jandex index: it will generate a Jandex index for used Java
  annotations within the project which allows to more efficiency to
  search and load the annotations to generate other metadata as
  mentioned below.

- Type Converter Loader: it will generate a loader for [type
  converter](type-converter.html) annotated with
  `@Converter(loader = true)` to allow faster way for Camel to load
  these converters.

- SPIs: it will generate all Service Provider Interfaces (SPI) for the
  component. This allows Camel to auto-discover your component without
  adding it manually to the Camel context.

- Configurers: it will generate all configurer Java classes from
  `@Configurer` annotated classes.

- Endpoint Schema: it will generate the property configurers as well as
  schema JSONs extracted from the component's Endpoint and Component
  classes. This allows Camel to avoid reflections while configuring the
  properties, thus allows for better efficiency.

- Endpoint URI Factory: it will generate endpoint factory to build URIs
  from a map of properties.

- Invoke on Header: it will generate source code for components using
  `@InvokeOnHeader`

- Prepare Component: it analyzes if the maven module contains Camel
  modules such as `components`, `dataformats`, `languages` and others.
  And for each of those generates extra descriptors and schema files for
  easier auto-discovery in Camel and tooling.

- Validate Component: it validates the Camel component if the meta-files
  for `components`, `dataformats`, `languages` and others, all contains
  the needed meta-data such as assigned labels, documentation for each
  option.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | ::: paragraph                     |
| Note                              | In order for the plugin to work   |
| :::                               | probably, you will need to have   |
|                                   | the proper Camel annotations in   |
|                                   | your component. Example,          |
|                                   | `@Component` for component class, |
|                                   | `@UriEndpoint` for endpoint       |
|                                   | class, `@UriParams` ..etc. You    |
|                                   | can take a look at these          |
|                                   | annotation how being used in any  |
|                                   | of the existing Camel components  |
|                                   | in github. Or even better, you    |
|                                   | can use [Camel Maven              |
|                                   | Archety                           |
|                                   | pes](camel-maven-archetypes.html) |
|                                   | to bootstrap the initial          |
|                                   | component, this already include   |
|                                   | all the necessary batteries for   |
|                                   | your component such as necessary  |
|                                   | initial Java classes, annotation  |
|                                   | and maven `pom.xml`.              |
|                                   | :::                               |
+-----------------------------------+-----------------------------------+
:::
::::::
::::::::
:::::::::

::::::::::::::::::::::: sect1
## Adding the plugin to your pom.xml {#_adding_the_plugin_to_your_pom_xml}

:::::::::::::::::::::: sectionbody
::: paragraph
In case you **did not** use the Camel Maven Archetypes to bootstrap the
initial component project, you will need to add the following to your
`pom.xml` build section:
:::

:::: listingblock
::: content
``` highlight
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
   </executions>
</plugin>
```
:::
::::

::: paragraph
This will attach the plugin `generate` goal to Maven's
`process-classes`, in order to generate all the necessary files being
described above upon compilation.
:::

::: paragraph
Thus, `mvn test`, `mvn package`, `mvn verify` and `mvn install` phases
should run this plugin.
:::

::::::: sect2
### Re-compile to include latest generated code {#_re_compile_to_include_latest_generated_code}

::: paragraph
The `camel-component-maven-plugin` is executed after the compiler, and
because it outputs java source code, then the compiler must execute
again (will only compile if source code has actually changed).
:::

::: paragraph
To ensure this the following should be added to the `pom.xml` file:
:::

:::: listingblock
::: content
``` highlight
<plugin>
    <artifactId>maven-compiler-plugin</artifactId>
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
:::::::

::::::::::: sect2
### Configuring output directory {#_configuring_output_directory}

::: paragraph
The plugin will by default generate outputs to
:::

::: ulist
- `src/generated/java` - for generated java source code

- `src/generated/resources` - for generated resource files
:::

::: paragraph
To include these folders with the Java compiler, then you can configure
Maven to include those directories:
:::

:::: listingblock
::: content
``` highlight
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
```
:::
::::

::: paragraph
However, if you want, you can also configure the
`camel-component-maven-plugin` to output directly to `src/main` as shown
(then you do not need to use the `build-helper-maven-plugin` as we do
above):
:::

:::: listingblock
::: content
``` highlight
<plugin>
  <groupId>org.apache.camel</groupId>
  <artifactId>camel-component-maven-plugin</artifactId>
  <version>${camel-version}</version>
  <configuration>
    <sourcesOutputDir>src/main/java</sourcesOutputDir>
    <resourcesOutputDir>src/main/resources</resourcesOutputDir>
  </configuration>
  <executions>
    <execution>
      <id>generate</id>
      <goals>
        <goal>generate</goal>
      </goals>
      <phase>process-classes</phase>
    </execution>
   </executions>
</plugin>
```
:::
::::
:::::::::::
::::::::::::::::::::::
:::::::::::::::::::::::
::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
