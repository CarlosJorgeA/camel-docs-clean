::: {#header}
# Camel Maven Archetypes
:::

:::::::::::::::::: {#content}
::::: {#preamble}
:::: sectionbody
::: paragraph
Camel is distributed with the following archetypes for Maven end users.
:::
::::
:::::

:::::::::::::: sect1
## Archetype Supported {#_archetype_supported}

::::::::::::: sectionbody
+-----------------------------------+-----------------------------------+
| Archetype                         | Description                       |
+===================================+===================================+
| camel-archetype-api-component     | This archetype is used for        |
|                                   | creating a new Maven project for  |
|                                   | Camel                             |
|                                   | [Com                              |
|                                   | ponents](components::index.html). |
|                                   | Use this if there is an API       |
|                                   | component missing in Camel that   |
|                                   | you want to create yourself.      |
+-----------------------------------+-----------------------------------+
| camel-archetype-component         | This archetype is used for        |
|                                   | creating a new Maven project for  |
|                                   | Camel                             |
|                                   | [Com                              |
|                                   | ponents](components::index.html). |
|                                   | Use this if there is a component  |
|                                   | missing in Camel that you want to |
|                                   | create yourself.                  |
+-----------------------------------+-----------------------------------+
| camel-archetype-cdi               | This archetype is used to create  |
|                                   | a new Maven project for Camel     |
|                                   | routes in the Java Container      |
|                                   | using CDI to configure            |
|                                   | components, endpoints and beans.  |
+-----------------------------------+-----------------------------------+
| camel-archetype-dataformat        | This archetype is used for        |
|                                   | creating a new Maven project for  |
|                                   | Camel [Data                       |
|                                   | Format](data-format.html)s. Use   |
|                                   | this if there is a data format    |
|                                   | missing in Camel that you want to |
|                                   | create yourself.                  |
+-----------------------------------+-----------------------------------+
| camel-archetype-java              | This archetype is used to create  |
|                                   | a new Maven project for Camel     |
|                                   | routes using Java DSL.            |
+-----------------------------------+-----------------------------------+
| camel-archetype-main              | This archetype is used to create  |
|                                   | a new Maven project for Camel     |
|                                   | routes running Camel standalone   |
|                                   | (camel-main).                     |
+-----------------------------------+-----------------------------------+
| camel-archetype-spring            | This archetype is used to create  |
|                                   | a new Maven project for XML DSL   |
|                                   | routes using Spring.              |
+-----------------------------------+-----------------------------------+
| camel-archetype-spring-boot       | This archetype is used to create  |
|                                   | a new Maven project for Camel     |
|                                   | routes using Spring Boot.         |
+-----------------------------------+-----------------------------------+
| camel-archetype-endpointdsl       | This archetype is used to create  |
|                                   | a new Maven project for Camel     |
|                                   | routes using Endpoint DSL.        |
+-----------------------------------+-----------------------------------+

::: paragraph
The maven coordinates for these archetypes is the following:
:::

:::: listingblock
::: content
``` highlight
<dependency>
  <groupId>org.apache.camel.archetypes</groupId>
  <artifactId>${archetype-name}</artifactId>
  <version>${camel-version}</version>
</dependency>
```
:::
::::

::::::::: sect2
### Snapshot Archetypes {#_snapshot_archetypes}

::: paragraph
If you would like to use an archetype from an unreleased version of
Camel, you just need to let the maven-archetype-plugin know where to
look for it. For example, say someone wanted to create a Java based
project based on Camel 3.21.0-SNAPSHOT. He'd need to use the following
command
:::

:::: listingblock
::: content
``` highlight
mvn archetype:generate \
  -DarchetypeGroupId=org.apache.camel.archetypes \
  -DarchetypeArtifactId=camel-archetype-java \
  -DarchetypeVersion=3.21.0-SNAPSHOT
```
:::
::::

::: paragraph
When the project is created, you may need to add another repository to
the pom.xml file. This is to load a SNAPSHOT version of the
camel-maven-plugin
:::

:::: listingblock
::: content
``` highlight
<pluginRepositories>
  <pluginRepository>
    <releases>
      <enabled>false</enabled>
    </releases>
    <snapshots/>
    <id>Apache Snapshot Repository</id>
    <url>http://repository.apache.org/snapshots</url>
  </pluginRepository>
</pluginRepositories>
```
:::
::::
:::::::::
:::::::::::::
::::::::::::::
::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
