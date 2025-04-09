::: {#header}
# Route Reload
:::

:::::::::::::::::::::::::::::: {#content}
::::::: {#preamble}
:::::: sectionbody
::: paragraph
The route reload functionality in Camel is capable of watching a
directory folder for file changes, and then automatic trigger reload of
the running routes in the Camel application.
:::

::: paragraph
This functionality is intended for development purposes and not for
production use.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | This feature is not supported     |
| Note                              | when using Camel Quarkus, as you  |
| :::                               | should be using the hot-reloading |
|                                   | functionality that comes with     |
|                                   | Quarkus instead. This             |
|                                   | functionality can also reload     |
|                                   | Java source code changes and much |
|                                   | more.                             |
+-----------------------------------+-----------------------------------+
:::
::::::
:::::::

::::::::::::::::::: sect1
## Using route reloading {#_using_route_reloading}

:::::::::::::::::: sectionbody
::: paragraph
You cannot watch for file changes in the Java classpath. It is only
possible to watch for file changes in the file system (i.e., using
`file` and not `classpath`).
:::

::: paragraph
The route reloading can be configured in Java or with Spring Boot,
Quarkus in the following way:
:::

:::: listingblock
::: content
``` highlight
CamelContext context = ...

RouteWatcherReloadStrategy reload = new RouteWatcherReloadStrategy();
reload.setFolder("myfolder/routes");
reload.setPattern("*.xml");

context.addService(reload);
```
:::
::::

::: paragraph
And with Camel Quarkus / Camel Main you can configure this in
`application.properties:`
:::

:::: listingblock
::: content
``` highlight
# turn on route reloading on file changes
camel.main.routes-reload-enabled = true
# the base directory to watch
camel.main.routes-reload-directory = myfolder/routes
# pattern(s) for files to watch
camel.main.routes-reload-pattern = *.xml
```
:::
::::

::: paragraph
And in Spring Boot:
:::

:::: listingblock
::: content
``` highlight
# turn on route reloading on file changes
camel.springboot.routes-reload-enabled = true
# the base directory to watch
camel.springboot.routes-reload-directory = myfolder/routes
# pattern(s) for files to watch
camel.springboot.routes-reload-pattern = *.xml
```
:::
::::

:::: sect2
### Route Reload Options {#_route_reload_options}

::: paragraph
You can configure the `RouteWatcherReloadStrategy` using the following
options:
:::

+-------------+-----------------------------------+------+-------------+
| Name        | Description                       | Def  | Type        |
|             |                                   | ault |             |
+=============+===================================+======+=============+
| `           | Directory to scan for route       | `s   | String      |
| routesReloa | changes. Camel cannot scan the    | rc/m |             |
| dDirectory` | classpath, so this must be        | ain/ |             |
|             | configured to a file directory.   | reso |             |
|             | Development with Maven as build   | urce |             |
|             | tool, you can configure the       | s/ca |             |
|             | directory to be                   | mel` |             |
|             | `src/main/resources` to scan for  |      |             |
|             | Camel routes in XML or YAML       |      |             |
|             | files.                            |      |             |
+-------------+-----------------------------------+------+-------------+
| `routesRel  | Whether the directory to scan     | `fa  | boolean     |
| oadDirector | should include subdirectories.    | lse` |             |
| yRecursive` | Depending on the number of        |      |             |
|             | sub-directories, then this can    |      |             |
|             | cause the JVM to start up slower  |      |             |
|             | as Camel uses the JDK file-watch  |      |             |
|             | service to scan for file changes. |      |             |
+-------------+-----------------------------------+------+-------------+
| `routesRel  | Used for enabling automatic       | `fa  | boolean     |
| oadEnabled` | routes reloading. If enabled,     | lse` |             |
|             | then Camel will watch for file    |      |             |
|             | changes in the given reload       |      |             |
|             | directory, and trigger reloading  |      |             |
|             | routes if files are changed.      |      |             |
+-------------+-----------------------------------+------+-------------+
| `routesRel  | Used for inclusive filtering of   | `*   | String      |
| oadPattern` | routes from directories. Typical  | .yam |             |
|             | used for specifying to accept     | l,*. |             |
|             | routes in XML or YAML files. The  | xml` |             |
|             | default pattern is \*.yaml,\*.xml |      |             |
|             | Multiple patterns can be          |      |             |
|             | specified separated by comma.     |      |             |
+-------------+-----------------------------------+------+-------------+
| `routes     | When reloading routes should all  | `t   | boolean     |
| ReloadRemov | existing routes be stopped and    | rue` |             |
| eAllRoutes` | removed. By default, Camel will   |      |             |
|             | stop and remove all existing      |      |             |
|             | routes before reloading routes.   |      |             |
|             | This ensures that only the        |      |             |
|             | reloaded routes will be active.   |      |             |
|             | If disabled, then only routes     |      |             |
|             | with the same route id are        |      |             |
|             | updated, and any existing routes  |      |             |
|             | are continued to run.             |      |             |
+-------------+-----------------------------------+------+-------------+
::::

::::: sect2
### Must use route id's {#_must_use_route_ids}

::: paragraph
When using route reload, then it is recommended to assign id's to your
routes, so Camel knows exactly which routes have been updated. This is
necessary because Apache Camel must stop the existing routes from
running before they can be updated.
:::

::: paragraph
And adding new routes is therefore possible as they would have a new
unique route id specified.
:::
:::::
::::::::::::::::::
:::::::::::::::::::

::::::: sect1
## See Also {#_see_also}

:::::: sectionbody
::: paragraph
See related [context-reload.html](context-reload.html).
:::

::: paragraph
See the following examples that come with live reloading enabled:
:::

::: ulist
- [camel-examples/examples/main-xml](https://github.com/apache/camel-examples/tree/main/main-xml)

- [camel-examples/examples/main-yaml](https://github.com/apache/camel-examples/tree/main/main-yaml)
:::
::::::
:::::::
::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
