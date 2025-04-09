::: {#header}
# Camel JBang
:::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
::::: {#preamble}
:::: sectionbody
::: paragraph
A JBang-based Camel app for easily running Camel routes.
:::
::::
:::::

:::::::::::::: sect1
## Installation {#_installation}

::::::::::::: sectionbody
::: paragraph
First, you must install [JBang](https://www.jbang.dev/), which is used
for launching Camel. See instructions on
[JBang](https://www.jbang.dev/download/) how to download and install.
:::

::: paragraph
After JBang is installed, you can verify JBang is working by executing
the following command from a command shell:
:::

:::: listingblock
::: content
``` highlight
jbang version
```
:::
::::

::: paragraph
Which should output the version of JBang.
:::

::: paragraph
To make it easier to use Camel JBang, then install the following:
:::

:::: listingblock
::: content
``` highlight
jbang app install camel@apache/camel
```
:::
::::

::: paragraph
This will install Apache Camel as the `camel` command within JBang,
meaning that you can run Camel from the command line by just executing
`camel` (see more next).
:::

::: paragraph
Note: It requires access to the internet, in case of using a proxy,
please ensure that the proxy is configured for your system. If Camel
JBang is not working with your current configuration, please look to
[Proxy configuration in JBang
documentation](https://www.jbang.dev/documentation/guide/latest/configuration.html#proxy-configuration).
:::
:::::::::::::
::::::::::::::

::::::::::::::::::::::::: sect1
## Container Image {#_container_image}

:::::::::::::::::::::::: sectionbody
::: paragraph
There is also a container image available in
[Dockerhub](https://hub.docker.com/r/apache/camel-jbang/)
:::

:::: listingblock
::: content
``` highlight
docker pull apache/camel-jbang:4.4.0
```
:::
::::

::: paragraph
or
:::

:::: listingblock
::: content
``` highlight
podman pull apache/camel-jbang:4.4.0
```
:::
::::

::: paragraph
Once you have the image in your local registry, you can run all the
commands listed below by simple doing:
:::

:::: listingblock
::: content
``` highlight
docker run apache/camel-jbang:4.4.0 version
```
:::
::::

::: paragraph
or
:::

:::: listingblock
::: content
``` highlight
podman run apache/camel-jbang:4.4.0 version
```
:::
::::

::: paragraph
This will print the following result:
:::

:::: listingblock
::: content
``` highlight
Camel JBang version: 4.4.0
```
:::
::::

::: paragraph
So running a simple route will be as easy as doing the following:
:::

:::: listingblock
::: content
``` highlight
docker run -v .:/integrations apache/camel-jbang:4.4.0 run /integrations/example.yaml
```
:::
::::

::: paragraph
or
:::

:::: listingblock
::: content
``` highlight
podman run -v .:/integrations apache/camel-jbang:4.4.0 run /integrations/example.yaml
```
:::
::::
::::::::::::::::::::::::
:::::::::::::::::::::::::

:::::::::::::::: sect1
## Using Camel JBang {#_using_camel_jbang}

::::::::::::::: sectionbody
::: paragraph
The Camel JBang supports multiple commands. Running the command below
will print all of them:
:::

:::: listingblock
::: content
``` highlight
camel --help
```
:::
::::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | The first time you run this       |
| Tip                               | command, it may cause             |
| :::                               | dependencies to be cached,        |
|                                   | therefore taking a few extra      |
|                                   | seconds to run. If you are        |
|                                   | already using JBang and you get   |
|                                   | first time to run errors such as  |
|                                   | `Exception in threa               |
|                                   | d "main" java.lang.NoClassDefFoun |
|                                   | dError: "org/apache/camel/dsl/jba |
|                                   | ng/core/commands/CamelJBangMain"` |
|                                   | you may try clearing the JBang    |
|                                   | cache and re-install again.       |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
All the commands support the `--help` and will display the appropriate
help if that flag is provided.
:::

::::::::: sect2
### Enable shell completion {#_enable_shell_completion}

::: paragraph
Camel JBang provides shell completion for bash and zsh out of the box.
To enable shell completion for Camel JBang, run:
:::

:::: listingblock
::: content
``` highlight
source <(camel completion)
```
:::
::::

::: paragraph
To make it permanent, run:
:::

:::: listingblock
::: content
``` highlight
echo 'source <(camel completion)' >> ~/.bashrc
```
:::
::::
:::::::::
:::::::::::::::
::::::::::::::::

::::::: sect1
## REPL loop {#_repl_loop}

:::::: sectionbody
::: paragraph
A simple read-eval-print loop is available, you can launch it with:
:::

:::: listingblock
::: content
``` highlight
camel shell
```
:::
::::
::::::
:::::::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Creating and running Camel routes {#_creating_and_running_camel_routes}

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
You can create a new basic routes with the `init` command.
:::

::: paragraph
For example, to create an XML route, you can run:
:::

:::: listingblock
::: content
``` highlight
camel init cheese.xml
```
:::
::::

::: paragraph
Which creates the file `cheese.xml` (in the current directory) with a
sample route.
:::

::: paragraph
To run the file, you do:
:::

:::: listingblock
::: content
``` highlight
camel run cheese.xml
```
:::
::::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | You can create and run any of the |
| Note                              | supported [DSLs](dsl.html) in     |
| :::                               | Camel such as YAML, XML, Java,    |
|                                   | Groovy.                           |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
To create a new .java route, you simply do:
:::

:::: listingblock
::: content
``` highlight
camel init foo.java
```
:::
::::

::: paragraph
When using the init command, then Camel will by default create the file
in the current directory. However, you can use the `--directory` option
to create the file in the specified directory. For example, to create in
a folder named *`foobar`* you can do:
:::

:::: listingblock
::: content
``` highlight
camel init foo.java --directory=foobar
```
:::
::::

::::::::::::::::::: sect2
### Running Routes from multiple files {#_running_routes_from_multiple_files}

::: paragraph
You can run more than one file, for example, to run two YAML files you
can do:
:::

:::: listingblock
::: content
``` highlight
camel run one.yaml two.yaml
```
:::
::::

::: paragraph
You can also mix different [DSLs](dsl.html) such as YAML and Java:
:::

:::: listingblock
::: content
``` highlight
camel run one.yaml hello.java
```
:::
::::

::: paragraph
You can also use wildcards (i.e. `*`) to match multiple files, such as
running all the YAML files:
:::

:::: listingblock
::: content
``` highlight
camel run *.yaml
```
:::
::::

::: paragraph
Or you can run all files starting with foo\*
:::

:::: listingblock
::: content
``` highlight
camel run foo*
```
:::
::::

::: paragraph
And to run everything
:::

:::: listingblock
::: content
``` highlight
camel run *
```
:::
::::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | The run goal can also detect      |
| Tip                               | files that are `properties`, such |
| :::                               | as `application.properties`.      |
+-----------------------------------+-----------------------------------+
:::
:::::::::::::::::::

:::::::::::: sect2
### Running a Maven or Gradle based project {#_running_a_maven_or_gradle_based_project}

::: paragraph
Camel JBang is intended for flat-file-based projects, where you run
small integrations. However, Camel JBang may be used as a tool for
migrating existing Maven or Gradle based projects. To make the migration
easier, then JBang can do *best effort* to run, export, or transform
these projects.
:::

::: paragraph
For example, if you have a Maven-based project, you can execute
:::

:::: listingblock
::: content
``` highlight
camel run pom.xml
```
:::
::::

::: paragraph
or for a Gradle project
:::

:::: listingblock
::: content
``` highlight
camel run build.gradle
```
:::
::::

::: paragraph
Camel JBang will then scan in `src/main/java` and `src/main/resources`
for files to include (recursive).
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | Using `camel run pom.xml` is not  |
| Note                              | intended as a fully compatible    |
| :::                               | way of running an existing        |
|                                   | Maven-based project. It cannot    |
|                                   | start Quarkus or Spring Boot      |
|                                   | applications; instead, use the    |
|                                   | proper plugins/commands. The      |
|                                   | command is mainly used to migrate |
|                                   | from old projects.                |
+-----------------------------------+-----------------------------------+
:::
::::::::::::

::::::::::::::::::::: sect2
### Running Route with user interactive prompt for placeholder values {#_running_route_with_user_interactive_prompt_for_placeholder_values}

::: paragraph
You can create Camel integrations that makes it possible for the user to
quickly enter placeholder values from command prompt.
:::

::: paragraph
For example, given the following route:
:::

:::: listingblock
::: content
``` highlight
import org.apache.camel.builder.RouteBuilder;

public class foo extends RouteBuilder {

    @Override
    public void configure() throws Exception {
        from("timer:java?period={{time:1000}}")
            .setBody()
                .simple("Hello Camel from {{you}}")
            .log("${body}");
    }
}
```
:::
::::

::: paragraph
Then if you run this with:
:::

:::: listingblock
::: content
``` highlight
camel run foo.java
```
:::
::::

::: paragraph
You will have an exception on startup about the missing value
:::

:::: listingblock
::: content
``` highlight
Caused by: java.lang.IllegalArgumentException: Property with key [you] not found in properties from text: Hello Camel from {{you}}`
```
:::
::::

::: paragraph
However, you can then run in prompt mode as follows:
:::

:::: listingblock
::: content
``` highlight
camel run foo.java --prompt
```
:::
::::

::: paragraph
And Camel will now prompt in the terminal for you to enter values for
the placeholders:
:::

:::: listingblock
::: content
``` highlight
2023-12-15 21:46:44.218  INFO 15033 --- [           main] org.apache.camel.main.MainSupport   : Apache Camel (JBang) 4.7.0 is starting
2023-12-15 21:46:44.331  INFO 15033 --- [           main] org.apache.camel.main.MainSupport   : Using Java 17.0.5 with PID 15033. Started by davsclaus in /Users/davsclaus/workspace/deleteme/prompt
2023-12-15 21:46:45.360  INFO 15033 --- [           main] mel.cli.connector.LocalCliConnector : Management from Camel JBang enabled
Enter optional value for time (1000):
Enter required value for you: Jack
2023-12-15 21:46:55.239  INFO 15033 --- [           main] el.impl.engine.AbstractCamelContext : Apache Camel 4.7.0 (foo) is starting
2023-12-15 21:46:55.323  INFO 15033 --- [           main] g.apache.camel.main.BaseMainSupport : Property-placeholders summary
2023-12-15 21:46:55.323  INFO 15033 --- [           main] g.apache.camel.main.BaseMainSupport :     [prompt]                       you=Jack
2023-12-15 21:46:55.341  INFO 15033 --- [           main] el.impl.engine.AbstractCamelContext : Routes startup (started:1)
```
:::
::::

::: paragraph
From the snippet above, Camel JBang had two prompts. First for the
`time` which has a default value of `1000` so you can just press ENTER
to accept the default value. And for `you` a value must be entered, and
we entered `Jack` in this example.
:::

::: paragraph
You may want to use this for Camel prototypes where you want the user to
be able to enter custom values quickly. Those values can of course be
pre-configured in `application.properties` as well.
:::
:::::::::::::::::::::

:::::::::::::::: sect2
### Running Route from input parameter {#_running_route_from_input_parameter}

::: paragraph
For very small Java routes then it is possible to provide the route as
CLI argument, as shown below:
:::

:::: listingblock
::: content
``` highlight
camel run --code='from("kamelet:beer-source").to("log:beer")'
```
:::
::::

::: paragraph
This is very limited as the CLI argument is a bit cumbersome to use than
files.
:::

::: ulist
- Java DSL code is only supported

- Code wrapped in single quote, so you can use double quote in Java DSL

- Code limited to what literal values possible to provide from the
  terminal and JBang.

- All route(s) must be defined in a single `--code` parameter.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | Using `--code` is only usable for |
| Note                              | very quick and small prototypes.  |
| :::                               |                                   |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
From **Camel 4.7** onwards the `--code` parameter can also refer to a
`.java` source file, that are not wrapped in a `public class` which
makes it possible to quickly try a prototype with some Camel java based
routes such as the following stored in a file named `quick.java`:
:::

:::: listingblock
::: content
``` highlight
from("timer:java?period=1000")
    .setBody()
        .simple("Hello Quick Camel from ${routeId}")
    .log("${body}");
```
:::
::::

::: paragraph
Then you can run this route via:
:::

:::: listingblock
::: content
``` highlight
camel run --code=quick.java
```
:::
::::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | You cannot use `--dev` to         |
| Note                              | hot-reload this on code changes.  |
| :::                               |                                   |
+-----------------------------------+-----------------------------------+
:::
::::::::::::::::

::::::: sect2
### Running Routes from source directory {#_running_routes_from_source_directory}

::: paragraph
You can also run dev mode when running Camel with `--source-dir`, such
as:
:::

:::: listingblock
::: content
``` highlight
camel run --source-dir=mycode
```
:::
::::

::: paragraph
This starts Camel where it will load the files from the *source dir*
(also subfolders).
:::
:::::::

::::::::::::::::::::::::::: sect2
### Stub components that should not be active {#_stub_components_that_should_not_be_active}

::: paragraph
Sometimes you need to troubleshoot an existing integration and is given
some Camel code (routes). These routes may use different components, and
those components may be tricky to run as they are configured in a custom
way, or need connection to servers you may not have access to.
:::

::: paragraph
You can run Camel JBang by stubbing those components (or all of them).
:::

::: paragraph
For example, suppose you need access to a JMS broker in the given route
below.
:::

:::: listingblock
::: content
``` highlight
from("jms:inbox")
  .log("Incoming order")
  .to("bean:transform")
  .log("After transformation")
  .to("jms:process");
```
:::
::::

::: paragraph
Then you can run this by stub the `jms` component by:
:::

:::: listingblock
::: content
``` highlight
camel run syncToDatabase.java --stub=jms
```
:::
::::

::: paragraph
Then Camel will not start up the JMS component but replace it with the
`stub` component, but keep the actual endpoint URIs.
:::

::: paragraph
You can then simulate sending messages to Camel with the `cmd send`
command:
:::

:::: listingblock
::: content
``` highlight
camel cmd send --body='Something here'
```
:::
::::

::: paragraph
Which then will send the message to the incoming endpoint in the route,
i.e. `jms:inbox` which has been stubbed.
:::

::: paragraph
You can also stub a specific endpoint by providing the full uri, such
as:
:::

:::: listingblock
::: content
``` highlight
camel run syncToDatabase.java --stub=jms:inbox
```
:::
::::

::: paragraph
Then only the `jms:inbox` endpoint is stubbed.
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | You can stub multiple components  |
| Tip                               | separated by comma, such as       |
| :::                               | `--stub=jms,sql`                  |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
Camel JBang comes with the `camel cmd stub` command that allows to list
all endpoints that has been stubbed, and also browse any messages that
are currently present in their internal queues. A stub endpoint is based
on the `seda` component.
:::

:::: listingblock
::: content
``` highlight
camel cmd stub
```
:::
::::

::: paragraph
And to browse the messages:
:::

:::: listingblock
::: content
``` highlight
camel cmd stub --browse
```
:::
::::
:::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::: sect2
### Dev mode with live reload {#_dev_mode_with_live_reload}

::: paragraph
You can enable dev mode that comes with live reload of the route(s) when
the source file is updated (saved), using the `--dev` options as shown:
:::

:::: listingblock
::: content
``` highlight
camel run foo.yaml --dev
```
:::
::::

::: paragraph
Then, while the Camel integration is running, you can update the YAML
route and update when saving.
:::

::: paragraph
This works for all DSLs, even java, so you can do:
:::

:::: listingblock
::: content
``` highlight
camel run hello.java --dev
```
:::
::::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | The live reload is meant for      |
| Note                              | development purposes, and if you  |
| :::                               | encounter problems with reloading |
|                                   | such as JVM class loading issues, |
|                                   | then you may need to restart the  |
|                                   | integration. Java files cannot be |
|                                   | supported in Spring Boot runtime  |
|                                   | since they have to be recompiled  |
|                                   | to trigger a restart.             |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
You can also run dev mode when running Camel with `--source-dir`, such
as:
:::

:::: listingblock
::: content
``` highlight
camel run --source-dir=mycode --dev
```
:::
::::

::: paragraph
This starts Camel where it will load the files from the *source dir*
(also subfolders). And in *dev mode* then you can add new files, update
existing files, and delete files, and Camel will automatically
hot-reload on the fly.
:::

::: paragraph
Using *source dir* is more flexible than having to specify the files in
the CLI as shown below:
:::

:::: listingblock
::: content
``` highlight
camel run mycode/foo.java mycode/bar.java --dev
```
:::
::::

::: paragraph
In this situation, then Camel will only watch and reload these two files
(foo.java and bar.java). So, for example, if you add a new file
cheese.xml, then this file is not reloaded. On the other hand, if you
use `--source-dir` then any files in this directory (and subfolders) are
automatic detected and reloaded. You can also delete files to remove
routes.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | You cannot use both files and     |
| Note                              | source dir together. The          |
| :::                               | following is not allowed:         |
|                                   | `camel r                          |
|                                   | un abc.java --source-dir=mycode`. |
+-----------------------------------+-----------------------------------+
:::

::::::::::::::::::::::::::: sect3
#### Uploading files to source directory via HTTP {#_uploading_files_to_source_directory_via_http}

::: paragraph
When running Camel JBang with `--source-dir`, `--console` and `--dev`
(reloading) then you can change the source files on-the-fly by copying,
modifying or deleting the files in the source directory.
:::

::: paragraph
This can also be done via HTTP using the `q/upload/:filename` HTTP
endpoint using PUT and DELETE verbs.
:::

::: paragraph
Suppose that you run Camel JBang with:
:::

:::: listingblock
::: content
``` highlight
camel run --source-dir=mycode --console --dev
```
:::
::::

::: paragraph
Then you can upload or modify a source file named `bar.java` you can
send a PUT request via curl:
:::

:::: listingblock
::: content
``` highlight
curl -X PUT http://0.0.0.0:8080/q/upload/bar.java --data-binary "@bar.java"
```
:::
::::

::: paragraph
Or via:
:::

:::: listingblock
::: content
``` highlight
curl -T bar.java http://0.0.0.0:8080/q/upload/bar.java
```
:::
::::

::: paragraph
To send the data via PUT, then the file body can be included when using
`Content-Type: application/x-www-form-urlencoded`:
:::

::: paragraph
For example, from a CURL `--trace-ascii log.txt`:
:::

:::: listingblock
::: content
``` highlight
0000: PUT /q/upload/bar.java HTTP/1.1
0021: Host: 0.0.0.0:8080
0035: User-Agent: curl/7.87.0
004e: Accept: */*
005b: Content-Length: 385
0070: Content-Type: application/x-www-form-urlencoded
00a1:
=> Send data, 385 bytes (0x181)
0000: // camel-k: language=java..import org.apache.camel.builder.Route
0040: Builder;..public class bar extends RouteBuilder {..    @Override
0080: .    public void configure() throws Exception {..        // Writ
00c0: e your routes here, for example:.        from("timer:java?period
0100: ={{time:1000}}").            .setBody().                .simple(
0140: "XXXCamel from ${routeId}").            .log("${body}");.    }.}
0180: .
== Info: Mark bundle as not supporting multiuse
<= Recv header, 17 bytes (0x11)
0000: HTTP/1.1 200 OK
<= Recv header, 19 bytes (0x13)
0000: content-length: 0
<= Recv header, 2 bytes (0x2)
0000:
== Info: Connection #0 to host 0.0.0.0 left intact
```
:::
::::

::: paragraph
To delete one or more files, you use the DELETE verb, such as:
:::

:::: listingblock
::: content
``` highlight
curl -X DELETE http://0.0.0.0:8080/q/upload/bar.java
```
:::
::::

::: paragraph
You can also use wildcards (\'\*\') to delete all .java files:
:::

:::: listingblock
::: content
``` highlight
curl -X DELETE http://0.0.0.0:8080/q/upload/*.java
```
:::
::::

::: paragraph
Or to delete everything
:::

:::: listingblock
::: content
``` highlight
curl -X DELETE http://0.0.0.0:8080/q/upload/*
```
:::
::::
:::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::: sect2
### Developer Console {#_developer_console}

::: paragraph
You can enable the developer console, which presents a variety of
information to the developer.
:::

:::: listingblock
::: content
``` highlight
camel run hello.java --console
```
:::
::::

::: paragraph
The console is then accessible from a web browser at:
[http://localhost:8080/q/dev](http://localhost:8080/q/dev){.bare} (by
default). The link is also shown in the log when Camel is starting up.
:::

::: paragraph
The console can give you insights into your running Camel integration,
such as reporting the top routes that takes the longest time to process
messages. You can then drill down to pinpoint exactly which individual
EIPs in these routes are the slowest.
:::

::: paragraph
The developer console can also output the data in JSON format, which,
for example, can be used by 3rd-party tooling to scrape the information.
:::

::: paragraph
For example, to output the top routes via curl, you can execute:
:::

:::: listingblock
::: content
``` highlight
curl -s -H "Accept: application/json"  http://0.0.0.0:8080/q/dev/top/
```
:::
::::

::: paragraph
And if you have `jq` installed which can format and output the JSON data
in color, then do:
:::

:::: listingblock
::: content
``` highlight
curl -s -H "Accept: application/json"  http://0.0.0.0:8080/q/dev/top/ | jq
```
:::
::::
:::::::::::::::

::::::::::::: sect2
### Using profiles {#_using_profiles}

::: paragraph
**Available from Camel 4.5**
:::

::: paragraph
Camel JBang comes with three sets of profiles
:::

::: ulist
- `dev`:for development (default)

- `test`:for testing (currently same as production)

- `prod`:for production
:::

::: paragraph
The developer profile will pre-configure Camel JBang with a number of
developer assisted features when running Camel. For example, tracing
messages during routing, additional metrics collected, and more. This is
useful during development and also enhanced the Camel JBang CLI tool.
:::

::: paragraph
However, you may want to run Camel JBang in a production-like scenario,
which you can do with:
:::

:::: listingblock
::: content
``` highlight
camel run hello.java --profile=prod
```
:::
::::

::: paragraph
You can have profile-specific configuration in configuration files using
the naming style `application-<profile>.properties`, such as in the
following:
:::

::: ulist
- `application.properties`: common configuration that is always in use
  (default).

- `application-dev.properties`: developer specific configuration for the
  `dev` profile.

- `application-prod.properties`: developer specific configuration for
  the `prod` profile.
:::

::: paragraph
The profile-specific configuration will override values in the common
configuration.
:::
:::::::::::::

:::::::::::: sect2
### Downloading JARs over the internet {#_downloading_jars_over_the_internet}

::: paragraph
By default, Camel JBang will automatically resolve dependencies needed
to run Camel, which is done by JBang and Camel respectively. Camel
itself detects at runtime if a component has a need for JARs that are
not currently available on the classpath, and can then automatically
download the JARs (incl transitive).
:::

::: paragraph
Camel will download these JARs in the following order:
:::

::: {.olist .arabic}
1.  from local disk in `~/.m2/repository`

2.  from the internet in Maven Central

3.  from the internet from custom third-party Maven repositories

4.  from all the repositories found in active profiles of
    `~/.m2/settings.xml` or a settings file specified using
    `--maven-settings` option.
:::

::: paragraph
If you do not want Camel JBang to download over the internet, you can
turn this off with `--download`, as shown below:
:::

:::: listingblock
::: content
``` highlight
camel run foo.java --download=false
```
:::
::::

::: paragraph
If you do not want Camel JBang to use your existing Maven settings file,
you can use:
:::

:::: listingblock
::: content
``` highlight
camel run foo.java --maven-settings=false
```
:::
::::
::::::::::::

::::::::::::: sect2
### Adding custom JARs {#_adding_custom_jars}

::: paragraph
Camel JBang will automatically detect dependencies for Camel components,
languages, data formats, etc. that from its own release. This means you
often do not have to specify which JARs to use.
:::

::: paragraph
However, if you need to add 3rd-party custom JARs, then you can specify
these with `--dep` as CLI argument in Maven GAV syntax
(`groupId:artifactId:version`), such as:
:::

:::: listingblock
::: content
``` highlight
camel run foo.java --dep=com.foo:acme:1.0
```
:::
::::

::: paragraph
In case you need to explicit add a Camel dependency you can use a
shorthand syntax (starting with `camel:` or `camel-`) such as:
:::

:::: listingblock
::: content
``` highlight
camel run foo.java --dep=camel-saxon
```
:::
::::

::: paragraph
You can specify multiple dependencies separated by comma:
:::

:::: listingblock
::: content
``` highlight
camel run foo.java --dep=camel-saxon,com.foo:acme:1.0
```
:::
::::
:::::::::::::

:::::::::::::::::::: sect2
### Using 3rd-party Maven repositories {#_using_3rd_party_maven_repositories}

::: paragraph
Camel JBang will download from local repository first, and then online
from Maven Central. To be able to download from 3rd-party Maven
repositories then you need to specify this as CLI argument, \]or in
`application.properties`
:::

:::: listingblock
::: content
``` highlight
camel run foo.java --repos=https://packages.atlassian.com/maven-external
```
:::
::::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | Multiple repositories can be      |
| Tip                               | separated by comma                |
| :::                               |                                   |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
The configuration for the 3rd-party Maven repositories can also be
configured in `application.properties` with the key `camel.jbang.repos`
as shown:
:::

:::: listingblock
::: content
``` highlight
camel.jbang.repos=https://packages.atlassian.com/maven-external
```
:::
::::

::: paragraph
And when running Camel then `application.properties` is automatically
loaded:
:::

:::: listingblock
::: content
``` highlight
camel run foo.java
```
:::
::::

::: paragraph
However, you can also explicitly specify the properties file to use:
:::

:::: listingblock
::: content
``` highlight
camel run foo.java application.properties
```
:::
::::

::: paragraph
And even better if you specify this as a profile:
:::

:::: listingblock
::: content
``` highlight
camel run foo.java --profile=application
```
:::
::::

::: paragraph
Where the profile id is the name of the properties file.
:::
::::::::::::::::::::

::::::::::::::::::::::: sect2
### Configuration of Maven usage {#_configuration_of_maven_usage}

::: paragraph
By default, existing `~/.m2/settings.xml` file is loaded, so it is
possible to alter the behaviour of Maven resolution process. Maven
settings file can provide information about Maven mirrors, credential
configuration (potentially encrypted) or active profiles, and additional
repositories.
:::

::: paragraph
Maven repositories can use authentication and the Maven-way to configure
credentials is through `<server>` elements, like this:
:::

:::: listingblock
::: content
``` highlight
<server>
    <id>external-repository</id>
    <username>camel</username>
    <password>{SSVqy/PexxQHvubrWhdguYuG7HnTvHlaNr6g3dJn7nk=}</password>
</server>
```
:::
::::

::: paragraph
While the password may be specified using plain text, it's better to
configure maven master password first and then use it to configure
repository password:
:::

:::: listingblock
::: content
``` highlight
$ mvn -emp
Master password: camel
{hqXUuec2RowH8dA8vdqkF6jn4NU9ybOsDjuTmWvYj4U=}
```
:::
::::

::: paragraph
The above password should be added to `~/.m2/settings-security.xml` file
like this:
:::

:::: listingblock
::: content
``` highlight
<settingsSecurity>
  <master>{hqXUuec2RowH8dA8vdqkF6jn4NU9ybOsDjuTmWvYj4U=}</master>
</settingsSecurity>
```
:::
::::

::: paragraph
Then a normal password can be configured like this:
:::

:::: listingblock
::: content
``` highlight
$ mvn -ep
Password: camel
{SSVqy/PexxQHvubrWhdguYuG7HnTvHlaNr6g3dJn7nk=}
```
:::
::::

::: paragraph
Finally, such passwords can be used in `<server>/<password>`
configuration.
:::

::: paragraph
By default, Maven reads the master password from
`~/.m2/settings-security.xml` file, but we can override it. The location
of the `settings.xml` file itself can be specified as well:
:::

:::: listingblock
::: content
``` highlight
camel run foo.java --maven-settings=/path/to/settings.xml --maven-settings-security=/path/to/settings-security.xml
```
:::
::::

::: paragraph
If you want to run Camel application without assuming any location (even
`~/.m2/settings.xml`), use this option:
:::

:::: listingblock
::: content
``` highlight
camel run foo.java --maven-settings=false
```
:::
::::
:::::::::::::::::::::::

:::::::::::::::::::::: sect2
### Running routes hosted on GitHub {#_running_routes_hosted_on_github}

::: paragraph
You can run a route hosted on GitHub using Camels
[GitHub](components:others:resourceresolver-github.html) resource
loader.
:::

::: paragraph
For example, to run one of the Camel Kamelets examples, you can do:
:::

:::: listingblock
::: content
``` highlight
camel run github:apache:camel-kamelets-examples:jbang/hello-java/Hey.java
```
:::
::::

::: paragraph
You can also use the `https` URL for GitHub. For example, you can browse
the examples from a web-browser and then copy the URL from the browser
window and run the example with Camel JBang:
:::

:::: listingblock
::: content
``` highlight
camel run https://github.com/apache/camel-kamelets-examples/tree/main/jbang/hello-java
```
:::
::::

::: paragraph
You can also use wildcards (i.e. `*`) to match multiple files, such as
running all the groovy files:
:::

:::: listingblock
::: content
``` highlight
camel run https://github.com/apache/camel-kamelets-examples/tree/main/jbang/languages/*.groovy
```
:::
::::

::: paragraph
Or you can run all files starting with rou\*
:::

:::: listingblock
::: content
``` highlight
camel run https://github.com/apache/camel-kamelets-examples/tree/main/jbang/languages/rou*
```
:::
::::

:::::::: sect3
#### Running routes from GitHub gists {#_running_routes_from_github_gists}

::: paragraph
Using gists from GitHub is a quick way to share small Camel routes that
you can easily run.
:::

::: paragraph
For example, to run a gist, you can execute:
:::

:::: listingblock
::: content
``` highlight
camel run https://gist.github.com/davsclaus/477ddff5cdeb1ae03619aa544ce47e92
```
:::
::::

::: paragraph
A gist can contain one or more files, and Camel JBang will gather all
relevant files, so a gist can contain multiple routes, properties files,
Java beans, etc.
:::
::::::::
::::::::::::::::::::::

:::::::::::::::::::::::::::::::: sect2
### Downloading routes hosted on GitHub {#_downloading_routes_hosted_on_github}

::: paragraph
We have made it easy for Camel JBang to download existing examples from
GitHub to local disk, which allows for modifying the example and to run
locally.
:::

::: paragraph
All you need to do is to copy the https link from the web browser. For
example, you can download the *dependency injection* example by:
:::

:::: listingblock
::: content
``` highlight
camel init https://github.com/apache/camel-kamelets-examples/tree/main/jbang/dependency-injection
```
:::
::::

::: paragraph
Then the files (not subfolders) are downloaded to the current directory.
The example can then be run locally with:
:::

:::: listingblock
::: content
``` highlight
camel run *
```
:::
::::

::: paragraph
You can also download to a new folder using the `--directory` option,
for example, to download to a folder named *myproject*, you would do:
:::

:::: listingblock
::: content
``` highlight
camel init https://github.com/apache/camel-kamelets-examples/tree/main/jbang/dependency-injection --directory=myproject
```
:::
::::

::: paragraph
You can also run in dev mode, to hot-deploy on source code changes.
:::

:::: listingblock
::: content
``` highlight
camel run * --dev
```
:::
::::

::: paragraph
You can also download a single file, such as one of the Camel Kamelets
examples:
:::

:::: listingblock
::: content
``` highlight
camel init https://github.com/apache/camel-kamelets-examples/blob/main/jbang/hello-yaml/hello.camel.yaml
```
:::
::::

::: paragraph
This is a groovy route, which you can run with (or use `*`):
:::

:::: listingblock
::: content
``` highlight
camel run simple.groovy
```
:::
::::

:::::::::::: sect3
#### Downloading routes form GitHub gists {#_downloading_routes_form_github_gists}

::: paragraph
You can also download files from gists easily as shown:
:::

:::: listingblock
::: content
``` highlight
camel init https://gist.github.com/davsclaus/477ddff5cdeb1ae03619aa544ce47e92
```
:::
::::

::: paragraph
This will then download the files to local disk, which you can run
afterward:
:::

:::: listingblock
::: content
``` highlight
camel run *
```
:::
::::

::: paragraph
You can also download to a new folder using the `--directory` option,
for example, to download to a folder named *foobar*, you would do:
:::

:::: listingblock
::: content
``` highlight
camel init https://gist.github.com/davsclaus/477ddff5cdeb1ae03619aa544ce47e92 --directory=foobar
```
:::
::::
::::::::::::
::::::::::::::::::::::::::::::::

::::::::::::::: sect2
### Using a specific Camel version {#_using_a_specific_camel_version}

::: paragraph
You can specify which Camel version to run as shown:
:::

:::: listingblock
::: content
``` highlight
jbang -Dcamel.jbang.version=3.18.4 camel@apache/camel [command]
```
:::
::::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | Older versions of Camel may not   |
| Note                              | work as well with Camel JBang as  |
| :::                               | the newest versions. Starting     |
|                                   | from Camel 3.20 onwards are the   |
|                                   | versions that are recommended to  |
|                                   | be used onwards.                  |
+-----------------------------------+-----------------------------------+
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | In Camel **3.20.3** onwards there |
| Tip                               | is a `version` command, see the   |
| :::                               | following section for more        |
|                                   | details.                          |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
In **Camel 3.20.2** onwards the `run` command has built-in support,
using `--camel-version`, for specifying the Camel version to use for the
running Camel integration.
:::

:::: listingblock
::: content
``` highlight
camel run * --camel-version=3.18.4
```
:::
::::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | This makes it easy to try         |
| Tip                               | different Camel versions, for     |
| :::                               | example, when you need to         |
|                                   | reproduce an issue, and find out  |
|                                   | how it works in a different Camel |
|                                   | version.                          |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
You can also try bleeding-edge development by using SNAPSHOT such as:
:::

:::: listingblock
::: content
``` highlight
jbang --fresh -Dcamel.jbang.version=3.21.0-SNAPSHOT camel@apache/camel [command]
```
:::
::::
:::::::::::::::

::::::::::::::::::::::::::::::: sect2
### Using the version command {#_using_the_version_command}

::: paragraph
In **Camel 3.20.3** onwards the `version` command makes it possible to
configure a specific version of Camel to use when running or exporting.
This makes it possible to use the latest Camel JBang CLI and run
integrations using an older Camel version.
:::

:::: listingblock
::: content
``` highlight
camel version
Camel JBang version: 3.20.3
```
:::
::::

::: paragraph
Here Camel JBang is using version 3.20.3. Now suppose we want to run
Camel integrations with version 3.18.2.
:::

:::: listingblock
::: content
``` highlight
camel version set 3.18.2
```
:::
::::

::: paragraph
And you can see what Camel version has been set by:
:::

:::: listingblock
::: content
``` highlight
camel version
Camel JBang version: 3.20.3
User configuration:
    camel-version = 3.18.2
```
:::
::::

::: paragraph
And when running an integration, then Camel JBang will show you the
*overridden version* when starting.
:::

:::: listingblock
::: content
``` highlight
camel run foo.java
Running integration with the following configuration:
    --camel-version=3.18.2
2023-03-17 13:35:13.876  INFO 28451 --- [           main] org.apache.camel.main.MainSupport        : Apache Camel (JBang) 3.18.2 is starting
...
```
:::
::::

::: {.admonitionblock .important}
+-----------------------------------+-----------------------------------+
| ::: title                         | You cannot use both a set version |
| Important                         | via `camel version set` and also  |
| :::                               | a version specified via           |
|                                   | `--camel-version` option, i.e.,   |
|                                   | the following is not possible:    |
+-----------------------------------+-----------------------------------+
:::

:::: listingblock
::: content
``` highlight
camel version set 4.0.1
camel run * --camel-version=4.3.0
```
:::
::::

::: paragraph
If you want to unset the version, you can use the `--reset` option:
:::

:::: listingblock
::: content
``` highlight
camel version set --reset
```
:::
::::

::: paragraph
Then the Camel version in use will be the same version as Camel JBang.
:::

::::::::::: sect3
#### Listing available Camel releases {#_listing_available_camel_releases}

::: paragraph
The `version` command can also show available Camel releases by checking
the Maven central repository.
:::

:::: listingblock
::: content
``` highlight
camel version list
 CAMEL VERSION   JDK   KIND     RELEASED     SUPPORTED UNTIL
    3.14.0       8,11  LTS    December 2021    December 2023
    3.14.1       8,11  LTS     January 2022    December 2023
    3.14.2       8,11  LTS       March 2022    December 2023
    3.14.3       8,11  LTS         May 2022    December 2023
    3.14.4       8,11  LTS        June 2022    December 2023
    3.14.5       8,11  LTS      August 2022    December 2023
    3.14.6       8,11  LTS    November 2022    December 2023
    3.14.7       8,11  LTS    December 2022    December 2023
    3.15.0         11         February 2022
    3.16.0         11            March 2022
    3.17.0      11,17              May 2022
    3.18.0      11,17  LTS        July 2022        July 2023
    3.18.1      11,17  LTS      August 2022        July 2023
    3.18.2      11,17  LTS   September 2022        July 2023
    3.18.3      11,17  LTS     October 2022        July 2023
    3.18.4      11,17  LTS    December 2022        July 2023
    3.18.5      11,17  LTS     January 2023        July 2023
    3.19.0      11,17          October 2022
    3.20.0      11,17  LTS    December 2022    December 2023
    3.20.1      11,17  LTS     January 2023    December 2023
    3.20.2      11,17  LTS    February 2023    December 2023
   4.0.0-M1        17   RC    February 2023
   4.0.0-M2        17   RC       March 2023
```
:::
::::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | The `version list` shows the      |
| Note                              | latest releases going back a few  |
| :::                               | versions, at this time of writing |
|                                   | the minimum version is Camel      |
|                                   | 3.14. To show all Camel 3.x       |
|                                   | releases, you can specify         |
|                                   | `--from-version=3.0` and the list |
|                                   | is longer. The list can only go   |
|                                   | back to Camel 2.18, as we do not  |
|                                   | have all release meta-data for    |
|                                   | older releases.                   |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
You can also show Camel releases for either Spring Boot or Quarkus using
the `--runtime` option, such as:
:::

:::: listingblock
::: content
``` highlight
camel version list --runtime=quarkus
 CAMEL VERSION  QUARKUS   JDK  KIND     RELEASED     SUPPORTED UNTIL
    3.14.0       2.6.0     11         December 2021
    3.14.1       2.7.0     11  LTS    February 2022      August 2022
    3.14.2       2.7.1     11  LTS       April 2022      August 2022
    3.14.4       2.7.2     11  LTS        July 2022      August 2022
    3.15.0      2.8.0-M1   11            March 2022
    3.16.0       2.8.0     11            April 2022
    3.16.0       2.9.0     11              May 2022
    3.17.0       2.10.0    11             June 2022
    3.18.0       2.11.0    11             July 2022
    3.18.1       2.12.0    11        September 2022
    3.18.2       2.13.0    11  LTS   September 2022       March 2023
    3.18.3       2.13.1    11  LTS    November 2022       March 2023
    3.18.3       2.13.2    11  LTS    December 2022       March 2023
    3.19.0       2.14.0    11         November 2022
    3.19.0       2.15.0    11         December 2022
    3.20.1       2.16.0    11          January 2023
```
:::
::::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | See more options with             |
| Tip                               | `camel version list --help`.      |
| :::                               |                                   |
+-----------------------------------+-----------------------------------+
:::
:::::::::::
:::::::::::::::::::::::::::::::

:::::::::::::::::::::: sect2
### Manage plugins {#_manage_plugins}

::: paragraph
Camel JBang uses a plugin concept for some of the subcommands so users
can add functionality on demand. Each provided plugin adds a list of
commands to the Camel JBang command line tool.
:::

::: paragraph
You can list the supported plugins with
:::

:::: listingblock
::: content
``` highlight
camel plugin get --all
```
:::
::::

:::: listingblock
::: content
``` highlight
Supported plugins:

 NAME        COMMAND     DEPENDENCY                                      DESCRIPTION
 kubernetes  kubernetes  org.apache.camel:camel-jbang-plugin-kubernetes  Run Camel applications on Kubernetes
 generate    generate    org.apache.camel:camel-jbang-plugin-generate    Generate code such as DTOs
```
:::
::::

::: paragraph
In case you want to enable a plugin and its functionality, you can add
it as follows:
:::

:::: listingblock
::: content
``` highlight
camel plugin add <plugin-name>
```
:::
::::

::: paragraph
For example to install `generate` you execute:
:::

:::: listingblock
::: content
``` highlight
camel plugin add generate
```
:::
::::

::: paragraph
This adds the plugin, and all subcommands are now available for
execution.
:::

::: paragraph
You can list the currently installed plugins with:
:::

:::: listingblock
::: content
``` highlight
camel plugin get
```
:::
::::

::: paragraph
To remove a plugin from the current Camel JBang command line tooling,
you can use the `plugin delete` command.
:::

:::: listingblock
::: content
``` highlight
camel plugin delete <plugin-name>
```
:::
::::
::::::::::::::::::::::

:::::::::::::::::::::::::::: sect2
### Running Pipes {#_running_pipes}

::: paragraph
Camel also supports running pipes, which represent Kubernetes custom
resources following a specific CRD format (Kubernetes Custom Resource
Definitions).
:::

::: paragraph
For example, a pipe file named `joke.yaml`:
:::

:::: listingblock
::: content
``` highlight
#!/usr/bin/env jbang camel@apache/camel run
apiVersion: camel.apache.org/v1
kind: Pipe
metadata:
  name: joke
spec:
  source:
    ref:
      kind: Kamelet
      apiVersion: camel.apache.org/v1
      name: chuck-norris-source
    properties:
      period: 2000
  sink:
    ref:
      kind: Kamelet
      apiVersion: camel.apache.org/v1
      name: log-sink
    properties:
      show-headers: false
```
:::
::::

::: paragraph
Can be run with camel:
:::

:::: listingblock
::: content
``` highlight
camel run joke.yaml
```
:::
::::

:::::::::::: sect3
#### Binding Kamelets in a pipe {#_binding_kamelets_in_a_pipe}

::: paragraph
Camel JBang is able to create the Pipe custom resource for you. You can
use the `bind` command to specify a source and a sink that should be set
in the pipe. As a result, Camel JBang will create a proper Pipe custom
resource for you.
:::

::: paragraph
The command expects a file name as command argument and provides several
options to define the source and the sink that should be used in the
pipe.
:::

:::: listingblock
::: content
``` highlight
camel bind joke.yaml --source chuck-norris-source --sink log-sink
```
:::
::::

::: paragraph
This creates the `joke.yaml` file that represents the Pipe resource.
:::

:::: listingblock
::: content
``` highlight
apiVersion: camel.apache.org/v1
kind: Pipe
metadata:
  name: joke
spec:
  source:
    ref:
      kind: Kamelet
      apiVersion: camel.apache.org/v1
      name: chuck-norris-source
    properties:
      period: 5000
  sink:
    ref:
      kind: Kamelet
      apiVersion: camel.apache.org/v1
      name: log-sink
```
:::
::::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | The bind command is able to       |
| Note                              | inspect the Kamelets being used   |
| :::                               | as a source and sink in order to  |
|                                   | automatically set all required    |
|                                   | properties. In case the Kamelet   |
|                                   | defines a required property, and  |
|                                   | the user has not specified such,  |
|                                   | the command will automatically    |
|                                   | set this property with an example |
|                                   | value. Once the pipe resource     |
|                                   | file is generated, you can review |
|                                   | and set the properties as you     |
|                                   | wish.                             |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
The bind command supports the following options:
:::

+-----------------+-----------------------------------------------------+
| Option          | Description                                         |
+=================+=====================================================+
| `--source`      | Source (from) such as a Kamelet or Camel endpoint   |
|                 | uri that provides data.                             |
+-----------------+-----------------------------------------------------+
| `--sink`        | Sink (to) such as a Kamelet or Camel endpoint uri   |
|                 | where data should be sent to.                       |
+-----------------+-----------------------------------------------------+
| `--step`        | Add optional 1-n steps to the pipe processing. Each |
|                 | step represents a reference to a Kamelet of type    |
|                 | action.                                             |
+-----------------+-----------------------------------------------------+
| `--property`    | Add a pipe property in the form of                  |
|                 | `                                                   |
|                 | [source,sink,error-handler,step-<n>].<key>=<value>` |
|                 | where `<n>` is the step number starting from 1.     |
+-----------------+-----------------------------------------------------+
| `-              | Add error handler (none,log,sink:\<endpoint\>).     |
| -error-handler` | Sink endpoints are expected in the format           |
|                 | \[\[apigroup/\]version:\]kind:\[namespace/\]name,   |
|                 | plain Camel URIs or Kamelet name.                   |
+-----------------+-----------------------------------------------------+
| `--output`      | Output format generated by this command (supports:  |
|                 | file, YAML or JSON). Default is \"file\".           |
+-----------------+-----------------------------------------------------+
::::::::::::

:::::::::: sect3
#### Binding explicit Camel URIs {#_binding_explicit_camel_uris}

::: paragraph
Usually, the source and sink reference a Kamelet by its name as shown in
the previous section. As an alternative, you can also just use an
arbitrary Camel endpoint URI that acts as a source or sink in the pipe.
:::

:::: listingblock
::: content
``` highlight
camel bind joke.yaml --source chuck-norris-source --sink https://mycompany.com/the-service
```
:::
::::

::: paragraph
As a result, the Pipe resource uses the Camel endpoints as source and
sink.
:::

:::: listingblock
::: content
``` highlight
apiVersion: camel.apache.org/v1
kind: Pipe
metadata:
  name: my-pipe
spec:
  source:
# ...
  sink:
    uri: https://mycompany.com/the-service # (1)
```
:::
::::

::: {.colist .arabic}
1.  Pipe with explicit Camel endpoint URI as sink where the data gets
    pushed to
:::
::::::::::
::::::::::::::::::::::::::::

:::::::::::::::::::: sect2
### Creating a new Kamelet {#_creating_a_new_kamelet}

::: paragraph
You can create a new kamelet with the `init` command by using kamelet
naming convention.
:::

::: paragraph
For example, to create a new kamelet source, you can do:
:::

:::: listingblock
::: content
``` highlight
camel init cheese-source.kamelet.yaml
```
:::
::::

::: paragraph
This will create a basic kamelet (based on the timer source).
:::

::: paragraph
And to use the kamelet, you could create the following route:
:::

:::: listingblock
::: content
``` highlight
- from:
    uri: "kamelet:cheese-source"
    parameters:
      period: "2000"
      message: "Hello World"
    steps:
      - log: "${body}"
```
:::
::::

::: paragraph
If you want to create a sink kamelet, then you just name it with sink as
follows (based on log sink):
:::

:::: listingblock
::: content
``` highlight
camel init wine-sink.kamelet.yaml
```
:::
::::

::: paragraph
You can then change the route to use the wine kamelet as follows:
:::

:::: listingblock
::: content
``` highlight
- from:
    uri: "kamelet:cheese-source"
    parameters:
      period: "2000"
      message: "Hello World"
    steps:
      - to: "kamelet:wine-sink"
```
:::
::::

::: paragraph
If you want to create a new Kamelet based on an existing Kamelet, for
example, to create a new sink based on the existing MySQL:
:::

:::: listingblock
::: content
``` highlight
camel init orderdb-sink.kamelet.yaml --from-kamelet=mysql-sink
```
:::
::::
::::::::::::::::::::

::::::::::::::::::: sect2
### Run from clipboard {#_run_from_clipboard}

::: paragraph
You can also run Camel routes directly from the OS clipboard. This
allows copying some code, and then quickly run this.
:::

::: paragraph
The syntax is
:::

:::: listingblock
::: content
``` highlight
camel run clipboard.<extension>
```
:::
::::

::: paragraph
Where `<extension>` is what kind of file the content of the clipboard
is, such as `java`, `xml`, or `yaml` etc.
:::

::: paragraph
For example, you can copy this to your clipboard and then run it
afterward:
:::

:::: listingblock
::: content
``` highlight
<route>
  <from uri="timer:foo"/>
  <log message="Hello World"/>
</route>
```
:::
::::

:::: listingblock
::: content
``` highlight
camel run clipboard.xml
```
:::
::::

:::::::: sect3
#### Run and reload from clipboard {#_run_and_reload_from_clipboard}

::: paragraph
**Available since Camel 4.2**
:::

::: paragraph
It is also possible to run from clipboard in *reload* mode as shown:
:::

:::: listingblock
::: content
``` highlight
camel run clipboard.xml --dev
```
:::
::::

::: paragraph
Then you can quickly make changes and copy to clipboard, and Camel JBang
will update while running.
:::
::::::::
:::::::::::::::::::

:::::::::::::::::::::::::::::::::::: sect2
### Sending messages via Camel {#_sending_messages_via_camel}

::: paragraph
**Available since Camel 4**
:::

::: paragraph
When building integrations with Camel JBang, you may find yourself in
need of being able to send messages into Camel, to test your Camel
routes. This can be challenging when the Camel routes are connected to
external systems using different protocols.
:::

::: paragraph
The best approach is to send messages into these external systems using
standard tools provided by these systems, which often can be done using
CLI tools. However, in some situations, where you may not be familiar
with these tools, you can try to let Camel send the message. Note that
this can only be possible in some scenarios, and should only be used as
a *quick way*.
:::

::: paragraph
Suppose you have a Camel route that consumes messages from an external
MQTT broker:
:::

:::: listingblock
::: content
``` highlight
- route:
    from:
      uri: kamelet:mqtt5-source
      parameters:
        topic: temperature
        brokerUrl: tcp://mybroker:1883
      steps:
        - transform:
            expression:
              jq:
                expression: .value
        - log:
            message: The temperature is ${body}
```
:::
::::

::: paragraph
In the example above the MQTT broker is running on hostname `mybroker`
port 1883.
:::

::: paragraph
The idea with the `camel cmd send` command is to *tap into* an existing
running Camel integration, and reuse an existing endpoint (if possible).
In this example, we want to use the existing configuration to avoid
having to configure this again.
:::

::: paragraph
By executing the following from a shell
:::

:::: listingblock
::: content
``` highlight
$ camel cmd send --body=file:payload.json mqtt
```
:::
::::

::: paragraph
We can send a message where the payload is loaded from a file
(*`payload.json`*). You can also specify the payload in the CLI
argument, but it's cumbersome to specify JSON structure so often it's
better to refer to a local file.
:::

:::: listingblock
::: content
``` highlight
{
  "value": 21
}
```
:::
::::

::: paragraph
The `mqtt` argument is the name of the existing running Camel
integration. You can also specify the PID instead. So what happens is
that Camel will let the existing integration send the message.
:::

::: paragraph
Because the existing integration only has one route, then the `send`
command will automatically pick the *`from`* endpoint, i.e.
`kamelet:mqtt5-source` with all its configuration. If there are multiple
routes, then you can filter which route/endpoint by the `--endpoint`
option:
:::

::: paragraph
For example, to pick the first route by *route id*:
:::

:::: listingblock
::: content
``` highlight
$ camel cmd send --body=file:payload.json --endpoint=route1 mqtt
```
:::
::::

::: paragraph
Or to pick the first route that uses mqtt component:
:::

:::: listingblock
::: content
``` highlight
$ camel cmd send --body=file:payload.json --endpoint=mqtt mqtt
```
:::
::::

::: paragraph
We are fortunate in this situation as the endpoint can be used as both a
*consumer* and *producer* in Camel, and therefore we are able to send
the message to the MQTT broker via `tcp://mybroker:1883` on topic
*temperate*.
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | See more options with             |
| Tip                               | `camel cmd send --help`.          |
| :::                               |                                   |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
The source for this example is provided on GitHub at [camel-jbang MQTT
example](https://github.com/apache/camel-kamelets-examples/tree/main/jbang/mqtt)).
:::

:::::::::: sect3
#### Poll messages via Camel {#_poll_messages_via_camel}

::: paragraph
**Available since Camel 4.8**
:::

::: paragraph
The `camel cmd send` command has been improved to also *poll* messages
from Camel. This is needed if you want to poll the latest messages from
a Kafka topic, JMS queue, or download a file from FTP etc.
:::

::: paragraph
The poll uses Camel consumer to poll the message (timeout if no message
received) instead of producer.
:::

::: paragraph
For example to poll a message from a ActiveMQ queue named cheese you can
do:
:::

:::: listingblock
::: content
``` highlight
$ camel cmd send --poll --endpoint='activemq:cheese'
```
:::
::::

::: paragraph
When you poll then you do not send any payload (body or headers).
:::
::::::::::
::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::: sect2
### Receiving messages via Camel {#_receiving_messages_via_camel}

::: paragraph
**Available since Camel 4.9**
:::

::: paragraph
When building a prototype integration with Camel JBang, you may route
messages to external systems. To know whether messages are being routed
correctly, you may use system consoles to look inside these systems
which messages have arrived, such as SQL prompts, web consoles, CLI
tools etc.
:::

::: paragraph
The Camel JBang now comes with a new command to receive messages from
remote endpoints. This can be used to quickly look or tail in terminal
the messages that an external systems has received. Camel does this by
consuming the messages (if the component has support for consumer) and
then let Camel JBang dump the messages from the CLI.
:::

::: paragraph
For example to start dumping all messages from ActiveMQ in one command,
you can do:
:::

:::: listingblock
::: content
``` highlight
$ camel cmd receive --endpoint='activemq:cheese'
```
:::
::::

::: paragraph
You can also use pattern syntax for the endpoint, so suppose you have
the following route:
:::

:::: listingblock
::: content
``` highlight
from("ftp:myserver:1234/foo")
  .to("log:order")
  .to("activemq:orders");
```
:::
::::

::: paragraph
Then you can tell Camel to automatic start receiving messages with:
:::

:::: listingblock
::: content
``` highlight
$ camel cmd receive --action=start
```
:::
::::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | You can enable and disable this   |
| Tip                               | mode with `--action=start` and    |
| :::                               | `--action-stop`.                  |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
Then Camel will automatically discover from the running integration, all
the *producers* and find the first *producer* that is remote and also
has consumer support. In the example above, that is the `activemq`
component, and thus Camel will start receive from `activemq:orders`.
:::

::: paragraph
You can see the status via:
:::

:::: listingblock
::: content
``` highlight
$ camel cmd receive
 PID   NAME   AGE   STATUS    TOTAL  SINCE  ENDPOINT
  4364  foo   1m33s  Enabled     18     2s  activemq://orders
```
:::
::::

::: paragraph
You can then dump all the received messages with:
:::

:::: listingblock
::: content
``` highlight
$ camel cmd receive --action=dump
```
:::
::::

::: paragraph
This will dump all the messages, and continue to dump new incoming
messages. Use (ctrl + c) to break and exit. You can turn follow off with
`--follow=false`.
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | Use `camel cmd receive --help` to |
| Tip                               | see all the various options for   |
| :::                               | this command.                     |
+-----------------------------------+-----------------------------------+
:::
:::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect2
### Controlling local Camel integrations {#_controlling_local_camel_integrations}

::: paragraph
To list the currently running Camel integrations, you use the `ps`
command:
:::

:::: listingblock
::: content
``` highlight
camel ps
  PID   NAME                             READY  STATUS    AGE
 61818  sample.camel.MyCamelApplica...   1/1   Running  26m38s
 62506  dude                             1/1   Running   4m34s
```
:::
::::

::: paragraph
This lists the PID, the name and age of the integration.
:::

::: paragraph
You can use the `stop` command to stop any of these running Camel
integrations. For example, to stop dude, you can do
:::

:::: listingblock
::: content
``` highlight
camel stop dude
Stopping running Camel integration (pid: 62506)
```
:::
::::

::: paragraph
You can also stop by the PID:
:::

:::: listingblock
::: content
``` highlight
camel stop 62506
Stopping running Camel integration (pid: 62506)
```
:::
::::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | You do not have to type the full  |
| Note                              | name, as the stop command will    |
| :::                               | match using integrations that     |
|                                   | start with the input, for         |
|                                   | example, you can do               |
|                                   | `camel stop d` to stop all        |
|                                   | integrations starting with d. If  |
|                                   | you have multiple integrations    |
|                                   | running with similar name such as |
|                                   | `dude`, `dude2`, then if you type |
|                                   | `camel stop dude` then Camel will |
|                                   | stop both integrations. However,  |
|                                   | if you want to only stop a single |
|                                   | integration then either stop via  |
|                                   | PID or use `camel stop dude!`     |
|                                   | with the `!` at the end.          |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
To stop all integrations, then execute without any pid:
:::

:::: listingblock
::: content
``` highlight
camel stop
Stopping running Camel integration (pid: 61818)
Stopping running Camel integration (pid: 62506)
```
:::
::::

:::::: sect3
#### Watching local Camel integrations {#_watching_local_camel_integrations}

::: paragraph
Most of the management commands can run in *watch* mode, which
repetitively output the status in full-screen mode. This is done using
the `--watch` parameter as follows:
:::

:::: listingblock
::: content
``` highlight
camel ps --watch
  PID   NAME                             READY  STATUS    AGE
 61818  sample.camel.MyCamelApplica...   1/1   Running  26m38s
 62506  dude                             1/1   Running   4m34s
```
:::
::::
::::::

::::::::::: sect3
#### Controlling Spring Boot and Quarkus integrations {#_controlling_spring_boot_and_quarkus_integrations}

::: paragraph
The Camel JBang CLI will by default only control Camel integrations that
are running using the CLI, eg `camel run foo.java`.
:::

::: paragraph
For the CLI to be able to control and manage Spring Boot or Quarkus
applications, then you need to add a dependency to these projects to
integrate with Camel CLI.
:::

::: paragraph
In Spring Boot, you add the following dependency:
:::

:::: listingblock
::: content
``` highlight
<dependency>
    <groupId>org.apache.camel.springboot</groupId>
    <artifactId>camel-cli-connector-starter</artifactId>
</dependency>
```
:::
::::

::: paragraph
In Quarkus, you need to add the following dependency:
:::

:::: listingblock
::: content
``` highlight
<dependency>
    <groupId>org.apache.camel.quarkus</groupId>
    <artifactId>camel-quarkus-cli-connector</artifactId>
</dependency>
```
:::
::::
:::::::::::

:::::::::::::::::::: sect3
#### Getting status of Camel integrations {#_getting_status_of_camel_integrations}

::: paragraph
The `get` command in Camel JBang is used for getting Camel specific
status for one or all of the running Camel integrations.
:::

::: paragraph
To display the status of the running Camel integrations:
:::

:::: listingblock
::: content
``` highlight
camel get
  PID   NAME      CAMEL   PLATFORM            READY  STATUS    AGE    TOTAL  FAILED  INFLIGHT  SINCE-LAST
 61818  MyCamel   3.20.0  Spring Boot v2.7.3   1/1   Running  28m34s    854       0         0     0s/0s/-
 63051  dude      3.20.0  JBang                1/1   Running     18s     14       0         0     0s/0s/-
 63068  mygroovy  3.20.0  JBang                1/1   Running      5s      2       0         0     0s/0s/-
```
:::
::::

::: paragraph
The `camel get` command will default display Camel application
(context), which is equivalent to typing `camel get context`.
:::

::: paragraph
This displays overall information for every Camel integration, where you
can see the total number of messages processed. The column *Since Last*
shows how long time ago the last processed message for 3 stages
(started/completed/failed).
:::

::: paragraph
The value of `0s/0s/-` means that the last started and completed message
just happened (0 seconds ago), and that there has not been any failed
message yet. And this example `9s/9s/1h3m` means that last started and
completed message is 9 seconds ago, and last failed were 1 hour and 3
minutes ago.
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | You can run in *watch* mode       |
| Tip                               | using: `camel get --watch`        |
| :::                               |                                   |
+-----------------------------------+-----------------------------------+
:::

::::::: sect4
##### Total in Camel 4.7 and 4.8 {#_total_in_camel_4_7_and_4_8}

::: paragraph
From **Camel 4.7** onwards, two numbers will be displayed for *TOTAL*,
*FAILED*, and *INFLIGHT* columns. The first number is messages received
from external systems (databases, cloud systems), and the second number
is the total number (as in earlier versions).
:::

:::: listingblock
::: content
``` highlight
camel get
  PID   NAME    CAMEL  PLATFORM        PROFILE  READY  STATUS   RELOAD  AGE   ROUTE  MSG/S  TOTAL  FAIL  INFLIGHT  LAST  DELTA  SINCE-LAST
 42240  cheese  4.7.0  JBang v0.116.0  dev       1/1   Running       0  1m8s    2/2   1.01   1/65   0/0       0/0     0     -1     1s/1s/-
```
:::
::::

::: paragraph
The *TOTAL* column above shows `1/65` which mean that 1 message was from
an external source, and 65 in total; which means that 64 are from
internal sources (such as timer, seda).
:::
:::::::

:::::: sect4
##### Total in Camel 4.9 onwards {#_total_in_camel_4_9_onwards}

::: paragraph
The TOTAL column is total number of messages processed. This is all
messages both started internally (such as from a timer) and received
from external systems (databases, cloud systems). In **Camel 4.9** we
made the TOTAL column a single digit again to not confuse, and we added
a new `REMOTE` column to show total for remote systems only. So the
example above would be:
:::

:::: listingblock
::: content
``` highlight
camel get
  PID   NAME    CAMEL  PLATFORM        PROFILE  READY  STATUS   RELOAD  AGE   ROUTE  MSG/S  TOTAL  REMOTE  FAIL  INFLIGHT  LAST  DELTA  SINCE-LAST
 42240  cheese  4.9.0  JBang v0.118.0  dev       1/1   Running       0  1m8s    2/2   1.01     65       1   0/0       0/0     0     -1     1s/1s/-
```
:::
::::
::::::
::::::::::::::::::::

::::::: sect3
#### Route status {#_route_status}

::: paragraph
You can also see the status of every route, from all the local Camel
integrations with `camel get route`:
:::

:::: listingblock
::: content
``` highlight
camel get route
  PID   NAME      ID      FROM                        STATUS    AGE   TOTAL  FAILED  INFLIGHT  MEAN  MIN  MAX  SINCE-LAST
 61818  MyCamel   hello   timer://hello?period=2000   Running  29m2s    870       0         0     0    0   14     0s/0s/-
 63051  dude      java    timer://java?period=1000    Running    46s     46       0         0     0    0    9     0s/0s/-
 63068  mygroovy  groovy  timer://groovy?period=1000  Running    34s     34       0         0     0    0    5     0s/0s/-
```
:::
::::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | Use `camel get --help` to display |
| Tip                               | all the available commands as     |
| :::                               | additional will be added in       |
|                                   | upcoming releases.                |
+-----------------------------------+-----------------------------------+
:::
:::::::

::::::::::: sect3
#### Top status of Camel integrations {#_top_status_of_camel_integrations}

::: paragraph
The `camel top` command is intended for getting top utilization
statistics (highest to lowest heap used memory) of the running Camel
integrations.
:::

:::: listingblock
::: content
``` highlight
camel top
  PID   NAME     JAVA     CAMEL   PLATFORM            STATUS    AGE         HEAP        NON-HEAP     GC     THREADS   CLASSES
 22104  chuck    11.0.13  3.20.0  JBang               Running   2m10s  131/322/4294 MB  70/73 MB  17ms (6)      7/8  7456/7456
 14242  MyCamel  11.0.13  3.20.0  Spring Boot v2.7.3  Running  33m40s  115/332/4294 MB  62/66 MB  37ms (6)    16/16  8428/8428
 22116  bar      11.0.13  3.20.0  JBang               Running    2m7s   33/268/4294 MB  54/58 MB  20ms (4)      7/8  6104/6104
```
:::
::::

::: paragraph
The *HEAP* column shows the heap memory (used/committed/max) and the
non-heap (used/committed). The *GC* column shows garbage collection
information (time and total runs). The *CLASSES* column shows the number
of classes (loaded/total).
:::

::: paragraph
You can also see the top performing routes (highest to lowest mean
processing time) of every route, from all the local Camel integrations
with `camel top route`:
:::

:::: listingblock
::: content
``` highlight
camel top route
  PID   NAME     ID                     FROM                                 STATUS    AGE    TOTAL  FAILED  INFLIGHT  MEAN  MIN  MAX  SINCE-LAST
 22104  chuck    chuck-norris-source-1  timer://chuck?period=10000           Started     10s      1       0         0   163  163  163          9s
 22116  bar      route1                 timer://yaml2?period=1000            Started      7s      7       0         0     1    0   11          0s
 22104  chuck    chuck                  kamelet://chuck-norris-source        Started     10s      1       0         0     0    0    0          9s
 22104  chuck    log-sink-2             kamelet://source?routeId=log-sink-2  Started     10s      1       0         0     0    0    0          9s
 14242  MyCamel  hello                  timer://hello?period=2000            Started  31m41s    948       0         0     0    0    4          0s
```
:::
::::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | Use `camel top --help` to display |
| Tip                               | all the available commands as     |
| :::                               | additional will be added in       |
|                                   | upcoming releases.                |
+-----------------------------------+-----------------------------------+
:::
:::::::::::

:::::::::::::::::::::: sect3
#### Tailing logs {#_tailing_logs}

::: paragraph
When you run Camel integrations, then they will by default run in the
foreground and output logs.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | The `camel log` command is        |
| Note                              | **not** supported when running in |
| :::                               | Spring Boot or Quarkus runtimes.  |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
You can from another terminal access the logs from any Camel integration
with the `log` command, as follows:
:::

:::: listingblock
::: content
``` highlight
camel log chuck
chuck     | 2023-01-04 17:59:19.288  INFO 44619 --- [           main] org.apache.camel.main.MainSupport   : Apache Camel (JBang) 3.21.0 is starting
chuck     | 2023-01-04 17:59:19.395  INFO 44619 --- [           main] org.apache.camel.main.MainSupport   : Using Java 17.0.5 with PID 44619. Started by davsclaus in /Users/davsclaus/workspace/
...
```
:::
::::

::: paragraph
You can also watch logs for all Camel integrations by `camel log`, or
you can specify by name/pids (separate by comma) `camel log bar,chuck`.
:::

::: paragraph
It is also possible to find and highlight keywords from the log using
`--find`, such as:
:::

:::: listingblock
::: content
``` highlight
camel log chuck --find=invoice
```
:::
::::

::: paragraph
You can find multiple items by repeating the option, i.e.:
:::

:::: listingblock
::: content
``` highlight
camel log chuck --find=invoice --find=order
```
:::
::::

::: paragraph
There is also a *grep* option that will filter the logs to only show
lines that matches text (ignore case).
:::

:::: listingblock
::: content
``` highlight
camel log chuck --grep=error
```
:::
::::

::: paragraph
The log command will by default tail the logs for new lines. If you want
to exit the command immediately, you can turn off the following as
shown:
:::

:::: listingblock
::: content
``` highlight
camel log chuck --grep=error --follow=false
```
:::
::::

::: paragraph
This will grep the logs for log lines with matches text `error` and
output only these logs, and exit.
:::
::::::::::::::::::::::

::::::::::::::::::::::: sect3
#### Tracing messages {#_tracing_messages}

::: paragraph
The `trace` command is used for showing how messages are routed in
Camel. The command has similar output as the `log` command but only
display message tracing information. This allows you to see every *step*
a message is routed in Camel.
:::

::: paragraph
The `trace` command has many options and can be used to *filter*, *grep*
or output on different detail *levels\`. The \_exchange id* is logged
(and grouped by colour), so you can use that to correlate the events
when traces are interleaved.
:::

::: paragraph
The trace command will by default list the status of whether tracing is
enabled or not in the integrations:
:::

:::: listingblock
::: content
``` highlight
camel trace
 PID   NAME   AGE  STATUS   TOTAL  QUEUE  FILTER  PATTERN
 6911  chuck   5s  Standby      0      0
```
:::
::::

::: paragraph
Here we can see that the tracer is in standby mode, and you need to
start the tracer before Camel will capture messages:
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | Camel 4.8 onwards has tracing in  |
| Tip                               | standby mode (when using dev      |
| :::                               | profile). You can enable tracing  |
|                                   | on startup by setting the         |
|                                   | configuration                     |
|                                   | `camel.trace.enabled=true` in     |
|                                   | `application.properties`.         |
+-----------------------------------+-----------------------------------+
:::

:::: listingblock
::: content
``` highlight
camel trace --action=start
```
:::
::::

::: paragraph
And if you run `camel trace` again you can see the tracer is started:
:::

:::: listingblock
::: content
``` highlight
camel trace
PID   NAME   AGE   STATUS   TOTAL  QUEUE  FILTER  PATTERN
6911  chuck  1m5s  Started     16      4
```
:::
::::

::: paragraph
And to show the traces you need to use the `dump` action as follows:
:::

:::: listingblock
::: content
``` highlight
camel trace chuck --action=dump
```
:::
::::

::: paragraph
You can also dump traces from all running integrations:
:::

:::: listingblock
::: content
``` highlight
camel trace --action=dump
```
:::
::::

::: paragraph
To stop tracing use `--action=stop`.
:::

::: paragraph
And you can also clear the already traced messages with
`--action=clear`.
:::
:::::::::::::::::::::::

:::::::::::::: sect3
#### Running Camel integrations in background {#_running_camel_integrations_in_background}

::: paragraph
The `run` command allows running Camel in the background with the
`--background` option. Therefore, to see and understand what happens
then the management commands cane be used, such as `camel ps`,
`camel get`, and `camel log`.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | Only Camel Main is supported to   |
| Note                              | run in background                 |
| :::                               |                                   |
+-----------------------------------+-----------------------------------+
:::

:::: listingblock
::: content
``` highlight
$ camel run chuck.yaml --background --runtime=main
Running Camel integration: chuck in background with PID: 80093

$ camel ps
  PID   NAME    READY  STATUS   AGE
 80093  chuck    1/1   Running  33s
```
:::
::::

::: paragraph
To see the log use `camel log`
:::

:::: listingblock
::: content
``` highlight
$ camel log
chuck     | 2023-01-04 17:59:19.288  INFO 44619 --- [           main] org.apache.camel.main.MainSupport   : Apache Camel (JBang) 3.21.0 is starting
chuck     | 2023-01-04 17:59:19.395  INFO 44619 --- [           main] org.apache.camel.main.MainSupport   : Using Java 17.0.5 with PID 44619. Started by davsclaus in /Users/davsclaus/workspace/
...
```
:::
::::

::: paragraph
To stop the integration you can use `camel stop`
:::

:::: listingblock
::: content
``` highlight
$ camel stop chuck
Shutting down Camel integration (pid: 80093)
```
:::
::::

::: paragraph
When running in background, then Camel JBang (**4.10 onwards**) will now
automatic wait for the integration to startup before returning from the
CLI command. This ensures that if there are any startup errors such as
compilation errors or DSL errors etc. then these are captured and
printed in the shell. You can use the option `--background-wait=false`
to turn this off.
:::
::::::::::::::

:::::::::::::::::::::::::: sect3
#### Starting and Stopping routes {#_starting_and_stopping_routes}

::: paragraph
The `camel cmd` is intended for executing miscellaneous commands in the
running Camel integrations. For example, there are commands to start and
stop routes.
:::

::: paragraph
To stop all the routes in the chuck integration, you execute:
:::

:::: listingblock
::: content
``` highlight
camel cmd stop-route chuck
```
:::
::::

::: paragraph
And the status will then report the status as *Stopped* for the chuck
integration:
:::

:::: listingblock
::: content
``` highlight
camel get route
  PID   NAME     ID                     FROM                                 STATUS    AGE   TOTAL  FAILED  INFLIGHT  MEAN  MIN  MAX  SINCE-LAST
 81663  chuck    chuck                  kamelet://chuck-norris-source        Stopped           600       0         0     0    0    1          4s
 81663  chuck    chuck-norris-source-1  timer://chuck?period=10000           Stopped           600       0         0    65   52  290          4s
 81663  chuck    log-sink-2             kamelet://source?routeId=log-sink-2  Stopped           600       0         0     0    0    1          4s
 83415  bar      route1                 timer://yaml2?period=1000            Started  5m30s    329       0         0     0    0   10          0s
 83695  MyCamel  hello                  timer://hello?period=2000            Started  3m52s    116       0         0     0    0    9          1s
```
:::
::::

::: paragraph
To start the routes, you can do:
:::

:::: listingblock
::: content
``` highlight
camel cmd start-route chuck
```
:::
::::

::: paragraph
To stop *all* routes in every Camel integration:
:::

:::: listingblock
::: content
``` highlight
camel cmd stop-route
```
:::
::::

::: paragraph
And you can start *all* routes using:
:::

:::: listingblock
::: content
``` highlight
camel cmd start-route
```
:::
::::

::: paragraph
To start a specific route, you need to use `--id` parameter such as the
following, which will start routes named `route1` in all running
integrations:
:::

:::: listingblock
::: content
``` highlight
camel cmd start-route --id=route1
```
:::
::::

::: paragraph
If you want to do this in a specific integration, you must include the
name or pid:
:::

:::: listingblock
::: content
``` highlight
camel cmd start-route bar --id=route1
```
:::
::::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | You can stop one or more route by |
| Tip                               | their ids by separating using     |
| :::                               | comma such as                     |
|                                   | `camel cm                         |
|                                   | d start-route --id=route1,hello`. |
|                                   | Use                               |
|                                   | `camel cmd start-route --help`    |
|                                   | for more details.                 |
+-----------------------------------+-----------------------------------+
:::
::::::::::::::::::::::::::

:::::: sect3
#### Running Camel integrations on Kubernetes {#_running_camel_integrations_on_kubernetes}

::: paragraph
After developing the Camel routes locally with JBang, you may want to
run these also on the Kubernetes platform at some point.
:::

::: paragraph
Camel JBang provides a plugin for managing and easily running Camel
applications on Kubernetes. The plugin uses the project export
functionality to build and deploy the application on Kubernetes with
Quarkus or SpringBoot.
:::

::: paragraph
Read about it in the [Camel Kubernetes
plugin](camel-jbang-kubernetes.html) documentation.
:::
::::::

::::::::::::::::::::::: sect3
#### Configuring logging levels {#_configuring_logging_levels}

::: paragraph
You can see the current logging levels of the running Camel integrations
by:
:::

:::: listingblock
::: content
``` highlight
camel cmd logger
  PID   NAME   AGE   LOGGER  LEVEL
 90857  bar   2m48s  root    INFO
 91103  foo     20s  root    INFO
```
:::
::::

::: paragraph
The logging level can be changed at runtime, for example, to change foo
to DEBUG you execute:
:::

:::: listingblock
::: content
``` highlight
camel cmd logger --logging-level=DEBUG foo
```
:::
::::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | You can use `--all` to change     |
| Tip                               | logging levels for all running    |
| :::                               | integrations.                     |
+-----------------------------------+-----------------------------------+
:::

::::::::::::::: sect4
##### Configuring individual logging levels {#_configuring_individual_logging_levels}

::: paragraph
From **Camel 4.6** onwards you can easily configure different logging
levels from CLI and `application.properties`.
:::

::: paragraph
For example from CLI, you can specify using the `--logging-category`
option. For example, to enable DEBUG logging if using Kafka:
:::

:::: listingblock
::: content
``` highlight
$ camel run myapp.yaml --logging-category=org.apache.kafka=DEBUG
```
:::
::::

::: paragraph
You can specify multiple categories by repeating the CLI option as
shown:
:::

:::: listingblock
::: content
``` highlight
$ camel run myapp.yaml --logging-category=org.apache.kafka=DEBUG --logging-category=com.foo.something=TRACE
```
:::
::::

::: paragraph
You can also configure logging levels in `application.properties` using
two styles
:::

::: ulist
- `logging.level.` it is the default style used by Camel and Spring Boot

- `quarkus.log.category.` is used by Quarkus
:::

::: paragraph
For example, you can declare as follows:
:::

:::: listingblock
::: content
``` highlight
# make it easy to configure individual logging levels
logging.level.org.apache.kafka = DEBUG
logging.level.com.foo.something = TRACE

# you can also use quarkus style naming
quarkus.log.category."org.apache.kafka".level = DEBUG
quarkus.log.category."com.foo.something".level = TRACE
```
:::
::::
:::::::::::::::
:::::::::::::::::::::::

::::::::: sect3
#### Listing services {#_listing_services}

::: paragraph
Some Camel integrations may host a service which clients can call, such
as REST, or SOAP-WS, or socket-level services using TCP protocols.
:::

::: paragraph
You can list the available services as shown in the example below:
:::

:::: listingblock
::: content
``` highlight
camel get service
 PID   NAME       COMPONENT      PROTOCOL  SERVICE
 1912  netty      netty          tcp       tcp:localhost:4444
 2023  greetings  platform-http  rest      http://0.0.0.0:7777/camel/greetings/{name} (GET)
 2023  greetings  platform-http  http      http://0.0.0.0:7777/q/dev
```
:::
::::

::: paragraph
Here you can see 2 Camel integrations. The netty integration hosts a TCP
service that is available on port 4444. The other Camel integration
hosts a REST service that can be called via GET only. And finally the
integration comes with embedded web console (started with the
`--console` option).
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | For a service to be listed then   |
| Note                              | Camel components must be able to  |
| :::                               | advertise the services using      |
|                                   | [camel-c                          |
|                                   | onsole.html](camel-console.html). |
+-----------------------------------+-----------------------------------+
:::
:::::::::

::::::::::: sect3
#### Observability with metrics {#_observability_with_metrics}

::: paragraph
Camel JBang comes with support for using Micrometer for metrics that
easily can be made available.
:::

::: paragraph
You simply either run with `--metrics` option, or enable and have more
control of the configuration in the `application.properties` file as
shown below:
:::

:::: listingblock
::: content
``` highlight
# enable HTTP server with metrics
camel.server.enabled=true
camel.server.metricsEnabled=true

# turn on micrometer metrics
camel.metrics.enabled=true
# include more camel details
camel.metrics.enableMessageHistory=true
# include additional out-of-the-box micrometer metrics for cpu, jvm and used file descriptors
camel.metrics.binders=processor,jvm-info,file-descriptor
```
:::
::::

::: paragraph
Then you can access metrics from the web browser at:
<http://localhost:8080/q/metrics>
:::

::: paragraph
You can also see metrics from the CLI as shown below:
:::

:::: listingblock
::: content
``` highlight
$ camel get metric
  PID   NAME         TYPE   METRIC                         ROUTE                  VALUE  MEAN  MAX  TOTAL
 11562  MyCoolCamel  timer  camel.exchange.event.notifier                             3    12   27     35
 11562  MyCoolCamel  timer  camel.exchange.event.notifier                             3     1    2      3
 11562  MyCoolCamel  gauge  camel.routes.added                                        3
 11562  MyCoolCamel  gauge  camel.routes.running                                      3
 11562  MyCoolCamel  gauge  jvm.info                                                  1
 11562  MyCoolCamel  gauge  process.cpu.usage                     0.0045185067010171795
 11562  MyCoolCamel  gauge  process.files.max                                     10240
 11562  MyCoolCamel  gauge  process.files.open                                      288
 11562  MyCoolCamel  gauge  system.cpu.count                                          8
 11562  MyCoolCamel  gauge  system.cpu.usage                        0.15222772277227722
 11562  MyCoolCamel  gauge  system.load.average.1m                        3.58935546875
```
:::
::::
:::::::::::

:::::::: sect3
#### Listing state of Circuit Breakers {#_listing_state_of_circuit_breakers}

::: paragraph
If your Camel integration uses [Circuit
Breaker](components:eips:circuitBreaker-eip.html), then you can output
the status of the breakers with Camel JBang as follows:
:::

:::: listingblock
::: content
``` highlight
camel get circuit-breaker
  PID   NAME  COMPONENT     ROUTE   ID               STATE      PENDING  SUCCESS  FAIL  REJECT
 56033  mycb  resilience4j  route1  circuitBreaker1  HALF_OPEN        5        2     3       0
```
:::
::::

::: paragraph
Here we can see the circuit breaker is in *half-open* state, i.e., a
state where the breaker is attempting to transition back to closed if
the failures start to drop.
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | You can run the command with      |
| Tip                               | watch to keep showing the latest  |
| :::                               | state                             |
|                                   | `                                 |
|                                   | watch camel get circuit-breaker`. |
+-----------------------------------+-----------------------------------+
:::
::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::: sect2
### Using Jolokia and Hawtio {#_using_jolokia_and_hawtio}

::: paragraph
The [Hawtio](https://hawt.io/) web console allows inspecting running
Camel integrations, such as all the JMX management information, and not
but least to visualize the Camel routes with live performance metrics.
Hawtio is a handy tool for many years, and we have made it easy to use
Hawtio with Camel JBang.
:::

::: paragraph
To let Hawtio able to inspect the Camel integrations, then the Jolokia
JVM Agent must be installed in the running integration, this can be
done, either explicit as follows:
:::

:::: listingblock
::: content
``` highlight
camel ps
  PID   NAME                             READY  STATUS    AGE
 61818  sample.camel.MyCamelApplica...   1/1   Running  26m38s
 62506  dude.java                        1/1   Running   4m34s
```
:::
::::

::: paragraph
With the PID you can then attach Jolokia:
:::

:::: listingblock
::: content
``` highlight
camel jolokia 62506
Started Jolokia for PID 62506
http://127.0.0.1:8778/jolokia/
```
:::
::::

::: paragraph
Instead of using PID, you can also attach by name pattern. In this
example, because the two Camel integrations have unique names (foo and
dude), then you can also attach Jolokia without knowing the PID as
follows:
:::

:::: listingblock
::: content
``` highlight
camel jolokia du
Started Jolokia for PID 62506
http://127.0.0.1:8778/jolokia/
```
:::
::::

::: paragraph
Then you can launch [Hawtio](https://hawt.io/) using Camel JBang:
:::

:::: listingblock
::: content
``` highlight
camel hawtio
```
:::
::::

::: paragraph
This will automatically download and start Hawtio, and open in web
browser.
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | See `camel hawtio --help` for     |
| Tip                               | options.                          |
| :::                               |                                   |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
And when Hawtio launches in the web browser, click the *Discover* tab
which should list all the local available Jolokia Agents (yes you can
use `camel jolokia PID` to connect to multiple different Camel
integrations and from this list select which to load).
:::

::: paragraph
Click the green *lightning* icon to connect to running Camel integration
(of choice).
:::

::: paragraph
You can uninstall the Jolokia JVM Agent in a running Camel integration
when no longer needed:
:::

:::: listingblock
::: content
``` highlight
camel jolokia 62506 --stop
Stopped Jolokia for PID 62506
```
:::
::::

::: paragraph
It is also possible to do this with only one command, as follows:
:::

:::: listingblock
::: content
``` highlight
camel hawtio dude
```
:::
::::

::: paragraph
Where *dude* is the name of the running Camel integration. When you stop
Hawtio (using `ctrl` + `c`) then Camel will attempt to uninstall the
Jolokia JVM Agent. However, this may not be able to do this always,
because the JVM is being terminated which can prevent camel-jbang from
doing JVM process communication to the running Camel integration.
:::
:::::::::::::::::::::::::::

:::::::::::::::::::::::: sect2
### Scripting from terminal using pipes {#_scripting_from_terminal_using_pipes}

::: paragraph
You can also execute a Camel JBang file as a script that can be used for
terminal scripting with pipes and filters.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | Every time the script is          |
| Note                              | executed, a JVM is started with   |
| :::                               | Camel. This is not very fast or   |
|                                   | low on memory usage, so use Camel |
|                                   | JBang terminal scripting where    |
|                                   | using Camel makes sense. For      |
|                                   | example, to use many Camel        |
|                                   | components or Kamelets, to more   |
|                                   | easily send or receive data from  |
|                                   | disparate IT systems.             |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
This requires adding the following line in top of the file, for example,
as in the `UpperCase.java` file below:
:::

:::: listingblock
::: content
``` highlight
///usr/bin/env jbang --quiet camel@apache/camel script "$0" "$@" ; exit $?

import org.apache.camel.builder.RouteBuilder;

//Will upper-case the input
public class UpperCase extends RouteBuilder {

    @Override
    public void configure() throws Exception {
        from("stream:in")
                .setBody()
                .simple("${body.toUpperCase()}")
                .to("stream:out");
    }
}
```
:::
::::

::: paragraph
To be able to execute this as a script, you need to set execute file
permission:
:::

:::: listingblock
::: content
``` highlight
chmod +x UpperCase.java
```
:::
::::

::: paragraph
Then you can then execute this as a script:
:::

:::: listingblock
::: content
``` highlight
echo "Hello\nWorld" | ./UpperCase.java
```
:::
::::

::: paragraph
Which should output:
:::

:::: listingblock
::: content
``` highlight
HELLO
WORLD
```
:::
::::

::: paragraph
Logging can be turned on using `--logging=true` which then logs to
`.camel-jbang/camel-pipe.log` file. The name of the logging file cannot
be configured.
:::

:::: listingblock
::: content
``` highlight
echo "Hello\nWorld" | ./UpperCase.java --logging=true
```
:::
::::

:::::: sect3
#### Using stream:in with line vs. raw mode {#_using_streamin_with_line_vs_raw_mode}

::: paragraph
When using `stream:in` to read data from *System in* then the [Stream
component](components::stream-component.html) works in two modes:
:::

::: ulist
- line mode (default): reads input as single lines (separated by line
  breaks). Message body is a `String`.

- raw mode: reads the entire stream until the *end of stream*. Message
  body is a `byte[]`.
:::

::: {.admonitionblock .important}
+-----------------------------------+-----------------------------------+
| ::: title                         | The default mode is due to        |
| Important                         | historically how the stream       |
| :::                               | component was created. Therefore, |
|                                   | you may want to set               |
|                                   | `stream:in?readLine=false` to use |
|                                   | raw mode.                         |
+-----------------------------------+-----------------------------------+
:::
::::::
::::::::::::::::::::::::

::::::::::: sect2
### Running local Kamelets {#_running_local_kamelets}

::: paragraph
You can also use Camel JBang to try local Kamelets, without the need to
publish them on GitHub or package them in a jar.
:::

:::: listingblock
::: content
``` highlight
camel run --local-kamelet-dir=/path/to/local/kamelets earthquake.yaml
```
:::
::::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | When the kamelets are from local  |
| Tip                               | file system, then they can be     |
| :::                               | live reloaded if they are         |
|                                   | updated, when you run Camel JBang |
|                                   | in `--dev` mode.                  |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
You can also point to a folder in a GitHub repository. For example, we
have provided some custom Kamelets at
[https://github.com/apache/camel-kamelets-examples/tree/main/custom-kamelets](https://github.com/apache/camel-kamelets-examples/tree/main/custom-kamelets){.bare},
which can be used easily:
:::

:::: listingblock
::: content
``` highlight
camel run --local-kamelet-dir=https://github.com/apache/camel-kamelets-examples/tree/main/custom-kamelets user.java
```
:::
::::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | If a kamelet is loaded from       |
| Note                              | GitHub, then they cannot be live  |
| :::                               | reloaded.                         |
+-----------------------------------+-----------------------------------+
:::
:::::::::::

::::::::::::: sect2
### Using the platform-http component {#_using_the_platform_http_component}

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | Camel JBang is only intended for  |
| Note                              | working with `platform-http` as   |
| :::                               | HTTP server component for         |
|                                   | rest-dsl, and for HTTP serer in   |
|                                   | general. It is not intended to    |
|                                   | work with `camel-servlet` or      |
|                                   | `camel-jetty`. If you find a need |
|                                   | for using Jetty, then Camel JBang |
|                                   | will not support seamless         |
|                                   | rest-dsl support and exporting.   |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
When a route is started from `platform-http` then Camel JBang will
automatically include a VertX HTTP server running on port 8080. For
example, the following route in a file named `server.yaml`:
:::

:::: listingblock
::: content
``` highlight
- from:
    uri: "platform-http:/hello"
    steps:
      - set-body:
          constant: "Hello World"
```
:::
::::

::: paragraph
Can be run with
:::

:::: listingblock
::: content
``` highlight
camel run server.yaml
```
:::
::::

::: paragraph
And you can call the HTTP service with:
:::

:::: listingblock
::: content
``` highlight
$ curl http://localhost:8080/hello
Hello World%
```
:::
::::
:::::::::::::

:::: sect2
### Using Java beans and processors {#_using_java_beans_and_processors}

::: paragraph
There is basic support for including regular Java source files together
with Camel routes, and let Camel JBang runtime compile the Java source.
This means you can include smaller utility classes, POJOs, Camel
Processors and whatnot that the application needs.
:::
::::

::::::::::::::::::::::::::::::::::::::::::: sect2
### Dependency Injection in Java classes {#_dependency_injection_in_java_classes}

::: paragraph
When running Camel integrations with camel-jbang, then the runtime is
`camel-main` based. This means there is no Spring Boot or Quarkus
available. However, we have added support for using annotation-based
dependency injection in Java classes.
:::

:::::::: sect3
#### Using Camel dependency injection {#_using_camel_dependency_injection}

::: paragraph
You can use the following Camel annotations on Camel standalone:
:::

::: ulist
- `@org.apache.camel.BindToRegistry` on class level (for custom beans,
  processors, etc.) to create an instance of the class and register in
  the [Registry](registry.html).

- `@org.apache.camel.Configuration` on class level for
  `CamelConfiguation` classes, which is used during Camel startup (only
  Camel Main runtime) to allow custom setup that requires some coding.
:::

::: paragraph
And these annotations should work on all runtimes (if target runtime is
either Quarkus or Spring Boot then favour using their annotations):
:::

::: ulist
- `@org.apache.camel.BeanInject` to dependency inject a bean on a class
  field.

- `@org.apache.camel.PropertyInject` to inject a [property
  placeholder](using-propertyplaceholder.html). Such as a property
  defined in `application.properties`.

- `@org.apache.camel.BindToRegistry` on a method to create a bean by
  invoking the method.

- `@org.apache.camel.Converter` on class level to auto-register the
  [type converters](type-converter.html) from the class.
:::

::: {.admonitionblock .important}
+-----------------------------------+-----------------------------------+
| ::: title                         | You can use `@BeanInject`         |
| Important                         | annotation to refer to existing   |
| :::                               | bean annotated with               |
|                                   | `@BindToRegistry`, however, this  |
|                                   | is possible if the dependency is  |
|                                   | registered before the dependant.  |
+-----------------------------------+-----------------------------------+
:::
::::::::

::::: sect3
#### Using Spring Boot dependency injection {#_using_spring_boot_dependency_injection}

::: paragraph
You can use the following Spring Boot annotations:
:::

::: ulist
- `@org.springframework.stereotype.Component` or
  `@org.springframework.stereotype.Service` on class level to create an
  instance of the class and register in the [Registry](registry.html).

- `@org.springframework.beans.factory.annotation.Autowired` to
  dependency inject a bean on a class field.
  `@org.springframework.beans.factory.annotation.Qualifier` can be used
  to specify the bean id.

- `@org.springframework.beans.factory.annotation.Value` to inject a
  [property placeholder](using-propertyplaceholder.html). Such as a
  property defined in `application.properties`.

- `@org.springframework.context.annotation.Bean` on a method to create a
  bean by invoking the method.
:::
:::::

::::: sect3
#### Using Quarkus injection {#_using_quarkus_injection}

::: paragraph
You can use the following Quarkus annotations:
:::

::: ulist
- `@javax.enterprise.context.ApplicationScoped` or
  `@javax.inject.Singleton` on class level to create an instance of the
  class and register in the [Registry](registry.html).
  `@javax.inject.Named` can be used to specify the bean id.

- `@javax.inject.Inject` to dependency inject a bean on a class field.
  `@javax.inject.Named` can be used to specify the bean id.

- `@org.eclipse.microprofile.config.inject.ConfigProperty` to inject a
  [property placeholder](using-propertyplaceholder.html). Such as a
  property defined in `application.properties`.

- `@javax.enterprise.inject.Produces` on a method to create a bean by
  invoking the method. `@javax.inject.Named` can be used to specify the
  bean id.
:::
:::::

::::::::: sect3
#### Using beans in Camel XML DSL {#_using_beans_in_camel_xml_dsl}

::: paragraph
Since Camel **4.0.0**, when using [XML
DSL](components:others:java-xml-io-dsl.html), we can declare additional
beans in a similar way as in [YAML
DSL](components:others:yaml-dsl.html). Such beans will be added to the
[Registry](registry.html) and can be referred to, for example, from
routes.
:::

:::: listingblock
::: content
``` highlight
<camel>

    <bean name="beanFromMap" type="com.acme.MyBean">
        <properties>
            <property key="foo" value="bar" />
        </properties>
    </bean>

</camel>
```
:::
::::

::: paragraph
The properties of the bean can be defined using either nested
`<property>` and `<properties>` elements or using dotted properties
style, as shown in the example below:
:::

:::: listingblock
::: content
``` highlight
<camel>

    <!-- nested properties style -->
    <bean name="beanFromMap" type="com.acme.MyBean">
        <properties>
            <property key="field1" value="f1_p" />
            <property key="field2" value="f2_p" />
            <property key="nested">
                <properties>
                    <property key="field1" value="nf1_p" />
                    <property key="field2" value="nf2_p" />
                </properties>
            </property>
        </properties>
    </bean>

    <!-- dotted properties style -->
    <bean name="beanFromProps" type="com.acme.MyBean">
        <properties>
            <property key="field1" value="f1_p" />
            <property key="field2" value="f2_p" />
            <property key="nested.field1" value="nf1_p" />
            <property key="nested.field2" value="nf2_p" />
        </properties>
    </bean>

</camel>
```
:::
::::
:::::::::

:::::::::::::::::::::: sect3
#### Using Spring beans in Camel XML DSL {#_using_spring_beans_in_camel_xml_dsl}

::: paragraph
Since Camel **4.0.0**, when using [XML
DSL](components:others:java-xml-io-dsl.html), we can also declare
*beans* using Spring Beans XML namespace. All these beans will be added
to the [Registry](registry.html).
:::

::: paragraph
This will not make the application managed by Spring Framework / Spring
Boot. Simply Camel will leverage existing support for generic bean
definition including:
:::

::: ulist
- dependency injection

- constructor injection

- dependency cycles

- wiring existing Camel objects (like `org.apache.camel.CamelContext`)
:::

::: paragraph
[XML DSL](components:others:java-xml-io-dsl.html) allows using XML
documents that define routes, rests and route templates. Since Camel
**4.0.0** these documents may use a new root XML element (either
`<camel>` or `<beans>` to resemble Spring XML DSL), where other Camel
elements (like `<routes>`) are contained.
:::

::: paragraph
Here's an example `camel.xml` file, which defines both the routes and
beans used (referred to) by the route definition:
:::

::::: listingblock
::: title
camel.xml
:::

::: content
``` highlight
<camel>

    <beans xmlns="http://www.springframework.org/schema/beans">
        <bean id="messageString" class="java.lang.String">
            <constructor-arg index="0" value="Hello"/>
        </bean>

        <bean id="greeter" class="org.apache.camel.main.app.Greeter">
            <description>Spring Bean</description>
            <property name="message">
                <bean class="org.apache.camel.main.app.GreeterMessage">
                    <property name="msg" ref="messageString"/>
                </bean>
            </property>
        </bean>
    </beans>

    <route id="my-route">
        <from uri="direct:start"/>
        <bean ref="greeter"/>
        <to uri="mock:finish"/>
    </route>

</camel>
```
:::
:::::

::: paragraph
This document contains embedded `<beans>` element using Spring Beans XML
namespace
([`http://www.springframework.org/schema/beans`](http://www.springframework.org/schema/beans){.bare}) -
Camel passes this element directly to Spring
`org.springframework.beans.factory.xml.XmlBeanDefinitionReader` and all
read beans are used to populate [Camel Registry](registry.html).
:::

::: paragraph
The beans declared this way may use references to some predefined Camel
beans. Currently, these are handled:
:::

::: ulist
- *`CamelContext`*: an instance of current
  `org.apache.camel.CamelContext`

- *`MainConfiguration`*: an instance of
  `org.apache.camel.main.MainConfigurationProperties` used for
  `org.apache.camel.main.KameletMain`
:::

::: paragraph
So we can use this XML fragment without actually defining what
`CamelContext` is.
:::

:::: listingblock
::: content
``` highlight
<camel>

    <beans xmlns="http://www.springframework.org/schema/beans">
        <bean id="greeter" class="org.apache.camel.main.app.Greeter">
            <property name="camelContext" ref="CamelContext"/>
            <!-- ... -->
        </bean>
    </beans>

    <route id="my-route">
        <from uri="direct:start"/>
        <bean ref="greeter"/>
        <to uri="mock:finish"/>
    </route>

</camel>
```
:::
::::

::: paragraph
What's more, we can declare some additional beans that can affect
internal mechanics of `CamelContext` being run.
`org.apache.camel.main.DefaultConfigurationConfigurer` is used by [Camel
Main](components:others:main.html) to configure `CamelContext` using
beans found in [Camel Registry](registry.html).
:::

::: paragraph
For example, we can customize used `org.apache.camel.spi.UuidGenerator`
with this XML fragment to replace UUID generator used by Camel (which
defaults to `org.apache.camel.support.DefaultUuidGenerator`):
:::

:::: listingblock
::: content
``` highlight
<camel>

    <beans xmlns="http://www.springframework.org/schema/beans">
        <bean id="customUUIDGenerator" class="org.apache.camel.support.ShortUuidGenerator" />
    </beans>

</camel>
```
:::
::::

::: paragraph
That's it - Camel context will then look up for the instances of
`org.apache.camel.spi.UuidGenerator` and if one is found, it'll be used
by Camel.
:::
::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::: sect2
### Configuring JDBC DataSources {#_configuring_jdbc_datasources}

::: paragraph
When using SQL databases, then you would often need to configure a JDBC
connection pool. You can do this:
:::

::: ulist
- Manually by adding 3rd party JAR dependency for the data source (and
  connection pool), and configure this from Java or XML/YAML DSL.

- **Camel 4.6** Spring Boot style with `spring.datasource.`
  configuration in `application.properties` (uses Hikari
  connection-pool)
:::

::::::: sect3
#### Defining datasource in YAML DSL as a bean {#_defining_datasource_in_yaml_dsl_as_a_bean}

::: paragraph
Here is a snippet how you can declare a bean as the `DataSource` for a
Postgres database in YAML DSL:
:::

:::: listingblock
::: content
``` highlight
- beans:
    - name: PostgresqlDataSource
      properties:
        databaseName: "postgres"
        password: "postgres"
        portNumber: "5432"
        serverName: localhost
        user: postgres
      type: org.postgresql.ds.PGSimpleDataSource
```
:::
::::

::: paragraph
You would then also need to add the JAR dependency with Maven
coordinates: `org.postgresql:postgresql:42.7.3`.
:::
:::::::

:::::::::::::::: sect3
#### Using a Spring Boot JDBC data source {#_using_a_spring_boot_jdbc_data_source}

::: paragraph
In `application.properties` you can set up the datasource such as:
:::

:::: listingblock
::: content
``` highlight
spring.datasource.url= jdbc:sqlserver://db.example.net:1433;databaseName=test_db
spring.datasource.username=user
spring.datasource.password=password
spring.datasource.driverClassName=com.microsoft.sqlserver.jdbc.SQLServerDriver
```
:::
::::

::: paragraph
The name of the `DataSource` will be registered with `springDataSource`,
however you can configure the name via:
:::

:::: listingblock
::: content
``` highlight
spring.datasource.name=myDataSourceNameHere
```
:::
::::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | Some JDBC drivers are automatic   |
| Tip                               | detected by camel-jbang. If not,  |
| :::                               | then you need to add the JAR      |
|                                   | dependency manually.              |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
And you can configure the Hikari connection-pool (if needed), such as:
:::

:::: listingblock
::: content
``` highlight
spring.datasource.hikari.connection-timeout=30000
spring.datasource.hikari.maximum-pool-size=10
spring.datasource.hikari.minimum-idle=5
spring.datasource.hikari.idle-timeout=600000
spring.datasource.hikari.max-lifetime=1800000
spring.datasource.hikari.pool-name=collection-pool
```
:::
::::

::: paragraph
And you can set logging to DEBUG on hikari to see the actual
configuration:
:::

:::: listingblock
::: content
``` highlight
logging.level.com.zaxxer.hikari = DEBUG
```
:::
::::
::::::::::::::::
::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect2
### Debugging {#_debugging}

::: paragraph
There are two kinds of debugging:
:::

::: ulist
- *Java debugging*: Java code debugging (Standard Java)

- *Camel route debugging*: Debugging Camel routes (requires Camel
  tooling plugins)
:::

::::::::::::: sect3
#### Java debugging {#_java_debugging}

::: paragraph
Camel JBang makes it easy to do *Java debugging* when you run your
integration with the `--jvm-debug` option as shown below:
:::

:::: listingblock
::: content
``` highlight
camel run hello.yaml --jvm-debug
Listening for transport dt_socket at address: 4004
```
:::
::::

::: paragraph
As you can see the default listening port is 4004 but can be configured
as described in [JBang
Debugging](https://www.jbang.dev/documentation/guide/latest/debugging.html).
:::

::: paragraph
This is a standard Java debug socket. You can then use the IDE of your
choice. For instance, see the generic documentation for
[IntelliJ](https://www.jetbrains.com/help/idea/attaching-to-local-process.html#create-rc),
[VS
Code](https://code.visualstudio.com/docs/java/java-debugging#_attach)
and [Eclipse
Desktop](https://www.vogella.com/tutorials/EclipseDebugging/article.html#remote-debugging).
You will surely want to add `Processor` to be able to put breakpoints
hit during route execution (as opposed to route definition creation).
:::

::::::: sect4
##### Standard JBang debugging {#_standard_jbang_debugging}

::: paragraph
You can debug your integration scripts by making use of the `--debug`
flag provided by JBang.
:::

:::: listingblock
::: content
``` highlight
jbang --debug  camel@apache/camel run hello.yaml
Listening for transport dt_socket at address: 4004
```
:::
::::

::: paragraph
This makes it possible to debug any of the Camel JBang commands, not
only the `run` command as shown above.
:::
:::::::
:::::::::::::

:::::::::::::::::::::::: sect3
#### Camel route debugging {#_camel_route_debugging}

::: paragraph
With **Camel 4.2** onwards you can now easily debug Camel routes from
the command shell with the `debug` command as shown below:
:::

:::: listingblock
::: content
``` highlight
camel debug hello.yaml
```
:::
::::

::: paragraph
This will run the integration (in background), and the current shell
running in foreground will act as a CLI based debugger. You can see
snippets from the log (in the top), the middle section shown the current
breakpoint. And in the bottom you can see content of the message
(similar to the `camel trace` command)
:::

:::: listingblock
::: content
``` highlight
2023-11-03 18:15:06.715  INFO 33384 --- [           main] g.apache.camel.main.BaseMainSupport :     [Command Line]                 camel.debug.loggingLevel=DEBUG
2023-11-03 18:15:06.715  INFO 33384 --- [           main] g.apache.camel.main.BaseMainSupport :     [Command Line]                 camel.debug.singleStepIncludeStartEnd=true
2023-11-03 18:15:06.787  INFO 33384 --- [           main] mel.cli.connector.LocalCliConnector : Camel CLI enabled (local)
2023-11-03 18:15:06.833  INFO 33384 --- [           main] el.impl.engine.AbstractCamelContext : Apache Camel 4.7.0 (foo) is starting
2023-11-03 18:15:06.906  INFO 33384 --- [           main] g.apache.camel.main.BaseMainSupport : Property-placeholders summary
2023-11-03 18:15:06.906  INFO 33384 --- [           main] g.apache.camel.main.BaseMainSupport :     [application.properties]       my-name=Donald Duck
2023-11-03 18:15:06.924  INFO 33384 --- [           main] el.impl.engine.AbstractCamelContext : Routes startup (started:1)
2023-11-03 18:15:06.924  INFO 33384 --- [           main] el.impl.engine.AbstractCamelContext :     Started route-07a6 (timer://yaml)
2023-11-03 18:15:06.924  INFO 33384 --- [           main] el.impl.engine.AbstractCamelContext : Apache Camel 4.7.0 (foo) started in 91ms (build:0ms init:0ms start:91ms)
2023-11-03 18:15:11.951  INFO 33384 --- [ - timer://yaml] foo.camel.yaml:10                   : Before:

Source: foo.camel.yaml:13                                                         History
--------------------------------------------------------------------------------  ------------------------------------------------------------------------------------------
  10:              - log:                                                         route-07a6/from-c041           (0ms)    4:       uri: timer
  11:                  message: 'Before: ${body}'                                 route-07a6/log1                (1ms)   10:         - log:
  12:                  id: log1
  13: --->         - setProperty:
  14:                  name: myProp
  15:                  expression:
  16:                    constant:
  17:                      expression: '123'
  18:                      id: constant-4004


2023-11-03 18:15:11.951  33384 --- [ thread #4 - timer://yaml]              route-07a6/setProperty-321c :     3 - Breakpoint (4ms)
 Exchange  (DefaultExchange)  InOnly  D48642E54410AF9-0000000000000000
 Message   (DefaultMessage)
 Body      (null)


    Breakpoint suspended. Press ENTER to continue.
```
:::
::::

::: paragraph
This is *basic* but yet powerful, as you have this debugger always
readily available, without having to start up VSCode or IDEA tooling.
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | The `camel debug` can debug       |
| Tip                               | almost all the DSLs (there is a   |
| :::                               | problem with groovy).             |
+-----------------------------------+-----------------------------------+
:::

::::::::::::::: sect4
##### Configuring breakpoints {#_configuring_breakpoints}

::: paragraph
When using `camel debug` then breakpoints are by default added to every
route. However, you can specify which breakpoints to use instead with
the `--breakpoint` option. This parameter is able to match the Camel
routes using a pattern style:
:::

::: {.olist .arabic}
1.  Exact match by node ids

2.  Match node ids by pattern (wildcard and regular expression)

3.  Match by EIP kind (setHeader, setBody, choice, split, etc.)

4.  Match by source and line number

5.  Match by line number
:::

::: paragraph
Multiple breakpoints can be separated by comma.
:::

::: paragraph
For example, to set a breakpoint at the setHeader EIP, you do:
:::

:::: listingblock
::: content
``` highlight
camel debug hello.yaml --breakpoint=setHeader
```
:::
::::

::: paragraph
To set a breakpoint at line 18:
:::

:::: listingblock
::: content
``` highlight
camel debug hello.yaml --breakpoint=18
```
:::
::::

::: paragraph
To set a breakpoint at line 18 and 34
:::

:::: listingblock
::: content
``` highlight
camel debug hello.yaml --breakpoint=18,34
```
:::
::::
:::::::::::::::
::::::::::::::::::::::::

:::::::::::::: sect3
#### Editing code using VSCode or IDEA editors {#_editing_code_using_vscode_or_idea_editors}

::: paragraph
You can use JBang to edit the source code by using the `jbang` CLI to
download dependencies, and set up a project for being ready to load into
an IDE of choice, such as IDEA, VSCode, or Eclipse.
:::

::: paragraph
This is typically in use when you use Java DSL and have Camel routes in
.java source code. To let JBang understand which dependencies are in
use, then you need to use JBang style for specifying dependencies by
using `//DEPS` code comments in Java code.
:::

::: paragraph
Given the following source file in `foo.java` file:
:::

:::: listingblock
::: content
``` highlight
//DEPS org.apache.camel:camel-bom:4.3.0@pom
//DEPS org.apache.camel:camel-endpointdsl
//DEPS org.apache.camel:camel-netty-http
//DEPS org.apache.camel:camel-stream

// add more dependencies here

import org.apache.camel.builder.endpoint.EndpointRouteBuilder;
import org.apache.camel.component.netty.http.NettyHttpMessage;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class foo extends EndpointRouteBuilder {

    private static final Logger LOG = LoggerFactory.getLogger(foo.class);

    @Override
    public void configure() {
        from(timer("trigger").period(5000).repeatCount(3))
                .to(nettyHttp("https://random-data-api.com/api/v2/banks").keepAlive(true))
                .process(e -> {
                    // use classes from camel-netty-http dependency in the source code
                    // and have jbang able to generate project with the dependencies ready
                    // to use in your IDE of choice
                    NettyHttpMessage msg = e.getMessage(NettyHttpMessage.class);
                    LOG.info("Netty HTTP response:\n\n\n{}\n\n\n", msg.getHttpResponse());
                })
                .log("Found bank:")
          .to(stream("out"));
    }
}
```
:::
::::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | You can use                       |
| Tip                               | `                                 |
| :::                               | camel dependency update foo.java` |
|                                   | to update the dependencies.       |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
Notice how we specify [JBang
dependencies](https://www.jbang.dev/documentation/guide/latest/dependencies.html)
at the top of the file. We want JBang to know and prepare for the IDE of
choice. The first `//DEPS` is the `@pom` which set up the Camel version
to use. The following `//DEPS` declares the Camel component we use.
:::

::: paragraph
You can now open this source file for editing in your IDE of choice by
using jbang CLI as follows:
:::

:::: listingblock
::: content
``` highlight
$ jbang edit -b foo.java
```
:::
::::

::: paragraph
You can find this example at:
[https://github.com/apache/camel-kamelets-examples/tree/main/jbang/jbang-edit](https://github.com/apache/camel-kamelets-examples/tree/main/jbang/jbang-edit){.bare}
:::
::::::::::::::

:::::::::::::::: sect3
#### Updating dependencies in source code {#_updating_dependencies_in_source_code}

::: paragraph
When working with Java source code, then you can keep the JBang
dependencies up to date using the following command:
:::

:::: listingblock
::: content
``` highlight
$ camel dependency update foo.java
```
:::
::::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | You can use `--clean` to not keep |
| Tip                               | any existing dependencies and     |
| :::                               | generate a clean fresh list.      |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
This will then automatic insert or update the JBang dependencies
(`//DEPS`) in the top of the source file.
:::

::: paragraph
You may want to use this for making it easier to load the source into an
IDE editor to do coding. See the previous section for more details.
:::

::::::::: sect4
##### Updating dependencies in Maven projects {#_updating_dependencies_in_maven_projects}

::: paragraph
Camel JBang can also help with keeping Camel Maven dependencies
up-to-date for Maven based projects.
:::

::: paragraph
For example if start using new Camel components, in your Camel routes.
Then you would have to add the corresponding Camel JAR dependencies to
the pom.xml file.
:::

::: paragraph
You can use the `camel dependency update` command to automate this, by
executing from the project folder:
:::

:::: listingblock
::: content
``` highlight
$ camel dependency update pom.xml
```
:::
::::

::: paragraph
Notice, that only adding new Camel dependencies is supported. If you
remove a component, then you need to remove the dependency from the
pom.xml manually. Also, this is only intended for production code from
`src/main` folder.
:::
:::::::::
::::::::::::::::

::::::: sect3
#### Camel route debugging using VSCode or IDEA editors {#_camel_route_debugging_using_vscode_or_idea_editors}

::: paragraph
The Camel route debugger is available by default (the `camel-debug`
component is automatically added to the classpath). By default, it can
be reached through JMX at the URL
`service:jmx:rmi:///jndi/rmi://localhost:1099/jmxrmi/camel`.
:::

::: paragraph
You can then use the Integrated Development Environment (IDE) of your
choice. For instance,
[IntelliJ](https://plugins.jetbrains.com/plugin/9371-apache-camel), [VS
Code](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-debug-adapter-apache-camel)
or [Eclipse
Desktop](https://marketplace.eclipse.org/content/textual-debugging-apache-camel).
:::

::: paragraph
A specific how-to is available for VS Code, see this
[video](https://youtu.be/owNhWxf42qk).
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | When exporting to Maven projects, |
| Tip                               | then you can use the maven        |
| :::                               | profile `camel.debug` that will   |
|                                   | include the `camel-debug` JAR to  |
|                                   | make it possible for Camel IDE    |
|                                   | tools to do Camel route           |
|                                   | debugging.                        |
+-----------------------------------+-----------------------------------+
:::
:::::::

::::::::::::::::::: sect3
#### Health Checks {#_health_checks}

::: paragraph
The status of health checks can be accessed via Camel JBang from the CLI
as follows:
:::

:::: listingblock
::: content
``` highlight
camel get health
  PID   NAME    AGE  ID             RL  STATE  RATE    SINCE   MESSAGE
 61005  mybind   8s  camel/context   R   UP    2/2/-  1s/3s/-
```
:::
::::

::: paragraph
Here we can see the Camel is *UP*. The application has just been running
for 8 seconds, and there have been two health checks invoked.
:::

::: paragraph
The output is showing the *default* level of checks as:
:::

::: ulist
- `CamelContext` health check

- Component-specific health checks (such as from `camel-kafka` or
  `camel-aws`, ...​)

- Custom health checks

- Any check that are not UP
:::

::: paragraph
The *RATE* column shows 3 numbers separated by `/`. So `2/2/-` means two
checks in total, two successes, and no failures. The two last columns
will reset when a health check changes state as this number is the
number of consecutive checks that was successful or failure. So if the
health check starts to fail, then the numbers could be:
:::

:::: listingblock
::: content
``` highlight
camel get health
  PID   NAME     AGE   ID             RL  STATE   RATE    SINCE    MESSAGE
 61005  mybind   3m2s  camel/context   R   UP    77/-/3  1s/-/17s  some kind of error
```
:::
::::

::: paragraph
Here we can see the numbers are changed to `77/-/3`. This means the
total is 77. There is no success, but the check has been failing three
times in a row. The *SINCE* column corresponds to the *RATE*. So in this
case, we can see the last check was 1 second ago, and that the check has
been failing for 17 seconds in a row.
:::

::: paragraph
You can use `--level=full` to output every health check, that will
include consumer and route level checks as well.
:::

::: paragraph
A health check may often be failed due to an exception was thrown which
can be shown via `--trace` flag:
:::

:::: listingblock
::: content
``` highlight
camel get health --trace
  PID   NAME      AGE   ID                                        RL  STATE    RATE       SINCE     MESSAGE
 61038  mykafka  6m19s  camel/context                             R   UP    187/187/-  1s/6m16s/-
 61038  mykafka  6m19s  camel/kafka-consumer-kafka-not-secure...  R  DOWN   187/-/187  1s/-/6m16s  KafkaConsumer is not ready - Error: Invalid url in bootstrap.servers: value


------------------------------------------------------------------------------------------------------------------------
                                                       STACK-TRACE
------------------------------------------------------------------------------------------------------------------------
    PID: 61038
    NAME: mykafka
    AGE: 6m19s
    CHECK-ID: camel/kafka-consumer-kafka-not-secured-source-1
    STATE: DOWN
    RATE: 187
    SINCE: 6m16s
    METADATA:
        bootstrap.servers = value
        group.id = 7d8117be-41b4-4c81-b4df-cf26b928d38a
        route.id = kafka-not-secured-source-1
        topic = value
    MESSAGE: KafkaConsumer is not ready - Error: Invalid url in bootstrap.servers: value
    org.apache.kafka.common.KafkaException: Failed to construct kafka consumer
        at org.apache.kafka.clients.consumer.KafkaConsumer.<init>(KafkaConsumer.java:823)
        at org.apache.kafka.clients.consumer.KafkaConsumer.<init>(KafkaConsumer.java:664)
        at org.apache.kafka.clients.consumer.KafkaConsumer.<init>(KafkaConsumer.java:645)
        at org.apache.kafka.clients.consumer.KafkaConsumer.<init>(KafkaConsumer.java:625)
        at org.apache.camel.component.kafka.DefaultKafkaClientFactory.getConsumer(DefaultKafkaClientFactory.java:34)
        at org.apache.camel.component.kafka.KafkaFetchRecords.createConsumer(KafkaFetchRecords.java:241)
        at org.apache.camel.component.kafka.KafkaFetchRecords.createConsumerTask(KafkaFetchRecords.java:201)
        at org.apache.camel.support.task.ForegroundTask.run(ForegroundTask.java:123)
        at org.apache.camel.component.kafka.KafkaFetchRecords.run(KafkaFetchRecords.java:125)
        at java.base/java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:515)
        at java.base/java.util.concurrent.FutureTask.run(FutureTask.java:264)
        at java.base/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1128)
        at java.base/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:628)
        at java.base/java.lang.Thread.run(Thread.java:829)
    Caused by: org.apache.kafka.common.config.ConfigException: Invalid url in bootstrap.servers: value
        at org.apache.kafka.clients.ClientUtils.parseAndValidateAddresses(ClientUtils.java:59)
        at org.apache.kafka.clients.ClientUtils.parseAndValidateAddresses(ClientUtils.java:48)
        at org.apache.kafka.clients.consumer.KafkaConsumer.<init>(KafkaConsumer.java:730)
        ... 13 more
```
:::
::::

::: paragraph
Here we can easily see that the health check is failing because of the
`org.apache.kafka.common.config.ConfigException` which is due to invalid
configuration: `Invalid url in bootstrap.servers: value`.
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | Use `camel get health --help` to  |
| Tip                               | see all the various options.      |
| :::                               |                                   |
+-----------------------------------+-----------------------------------+
:::
:::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::: sect1
## Running with Spring Boot or Quarkus {#_running_with_spring_boot_or_quarkus}

:::::::::::::::::::: sectionbody
::: paragraph
Camel JBang is *primary* intended to be Camel standalone only. In
**Camel 4.6** onwards, we added limited support for running with Spring
Boot or Quarkus, but there are some limitations.
:::

::: paragraph
You use the `--runtime` option to specify which platform to use, as
shown below:
:::

:::: listingblock
::: content
``` highlight
camel run foo.camel.yaml --runtime=spring-boot
```
:::
::::

::: paragraph
And for Quarkus:
:::

:::: listingblock
::: content
``` highlight
camel run foo.camel.yaml --runtime=quarkus
```
:::
::::

::: paragraph
When running this way, then Camel JBang is *essentially* doing an
*export* to a temporary folder, and then running Spring Boot or Quarkus
using Maven.
:::

::: paragraph
You can do changes to the source file and have Quarkus and Spring Boot
reload the routes, just as `camel run --dev` can do, but uses the
natural Spring Boot *dev-tools* and Quarkus *dev mode* functionality.
:::

::: paragraph
There are several limitations, one would be that Spring Boot and Quarkus
cannot automatically detect new components and download JARs. (you can
stop and run again to update dependencies).
:::

::: paragraph
When using Quarkus, then you can only select the Quarkus version to run.
That is locked to a specific Camel version. You can see the versions by
`camel version list --runtime=quarkus`. On the other hand Spring Boot is
more flexible where you can choose different Spring Boot and Camel
versions (within reasonable range).
:::

::: paragraph
For example:
:::

:::: listingblock
::: content
``` highlight
camel run foo.camel.yaml --runtime=spring-boot --spring-boot-version=3.2.3 --camel-version=4.4.1
```
:::
::::

::: paragraph
And for Quarkus:
:::

:::: listingblock
::: content
``` highlight
camel run foo.camel.yaml --runtime=quarkus --quarkus-version=3.9.4
```
:::
::::
::::::::::::::::::::
:::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Transforming message (data mapping) {#_transforming_message_data_mapping}

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
When integrating a system, you often need to transform messages from one
system to another. Camel has a rich set of capabilities for this such as
various data formats, templating languages, and much more. However, for
basic data mapping such as taking an existing incoming JSON document and
transforming this to a smaller JSON document, you may want to do this
quickly with Camel.
:::

::: paragraph
The `camel transform message` command can be used for such tasks, where
it can take an existing source file as input, and then a template that
defines how to transform the data, and then show the output (in real
time).
:::

::: paragraph
For example, given this JSon document (in a file named `random.json`),
we want to combine the name, and select a few fields:
:::

:::: listingblock
::: content
``` highlight
{
  "id": 9914,
  "uid": "eb5fa603-1db6-45f9-912a-431a6ed59b18",
  "password": "ei7gvYKdnN",
  "first_name": "Khalilah",
  "last_name": "Monahan",
  "username": "khalilah.monahan",
  "email": "khalilah.monahan@email.com",
  "avatar": "https://robohash.org/utnumquamexcepturi.png?size=300x300&set=set1",
  "gender": "Agender",
  "phone_number": "+54 (421) 591-5640 x333",
  "social_insurance_number": "268418308",
  "date_of_birth": "1975-03-11",
  "employment": {
    "title": "Product Design Director",
    "key_skill": "Work under pressure"
  },
  "address": {
    "city": "New Fritzchester",
    "street_name": "Patrick Common",
    "street_address": "4538 Reggie Inlet",
    "zip_code": "16282-7045",
    "state": "New York",
    "country": "United States",
    "coordinates": {
      "lat": -1.9868753435474673,
      "lng": 39.09763956726292
    }
  },
  "credit_card": {
    "cc_number": "4493983042212"
  },
  "subscription": {
    "plan": "Student",
    "status": "Active",
    "payment_method": "Debit card",
    "term": "Monthly"
  }
}
```
:::
::::

::: paragraph
Then we can have a `transform.json` file as the beginning of the
template, with the structure of the desired output:
:::

:::: listingblock
::: content
``` highlight
{
    "sid": 123,
    "name": "TODO",
    "country": "TODO",
    "phone": "TODO",
    "student": false
}
```
:::
::::

::: paragraph
We can then run `camel transform message` and have it update (in real
time) the output every time we change the template.
:::

:::: listingblock
::: content
``` highlight
$ camel transform message --body=file:random.json --language=simple --template=file:transform.json --pretty --watch
```
:::
::::

::: paragraph
What happens is then Camel will output on the console as you go:
:::

:::: listingblock
::: content
``` highlight
 Exchange  (DefaultExchange)  InOut   23F5DD4CE6C260B-0000000000000002
 Message   (DefaultMessage)
 Body      (String) (bytes: 118)
 {
   "sid": 123,
   "name": "TODO",
   "country": "TODO",
   "phone": "TODO",
   "student": false
 }
```
:::
::::

::: paragraph
Then you can update the `transform.json` file and save it and see the
generated output:
:::

:::: listingblock
::: content
``` highlight
{
   "sid": ${jq(.id)},
   "name": "${jq(.first_name)} ${jq(.last_name)}",
   "country": "TODO",
   "phone": "TODO",
   "student": false
}
```
:::
::::

::: paragraph
And the output:
:::

:::: listingblock
::: content
``` highlight
 Exchange  (DefaultExchange)  InOut   23F5DD4CE6C260B-0000000000000018
 Message   (DefaultMessage)
 Body      (String) (bytes: 158)
 {
   "sid": 9914,
   "name": "Khalilah Monahan",
   "country": "TODO",
   "phone": "TODO",
   "student": false
 }
```
:::
::::

::: paragraph
Then you can continue to update the `transform.json` until you have the
desired result. And if you make a mistake, then you see an error (in
red) with stacktrace that hopefully can help you out how to fix this.
:::

::::::::::::::::::::::::::::::::::::::::::::: sect2
### Transforming directly from the Camel DSL source {#_transforming_directly_from_the_camel_dsl_source}

::: paragraph
In the example above we externalize the transformation into a template
file named `transform.json`. However, it's possible to transform
directly in the Camel DSL route.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | Currently Java DSL is not working |
| Note                              | in \--watch mode (reload on       |
| :::                               | change)                           |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
In the following, we cover the json-transform example from:
[https://github.com/apache/camel-kamelets-examples/tree/main/jbang/json-transform](https://github.com/apache/camel-kamelets-examples/tree/main/jbang/json-transform){.bare}
:::

::: paragraph
In this example, a random beer is fetched via the beer-source kamelet.
The beer data is as follows:
:::

:::: listingblock
::: content
``` highlight
 {
   "id": 2104,
   "uid": "cefb36e1-e42f-4083-8f97-fc4ff85e56fb",
   "brand": "Pabst Blue Ribbon",
   "name": "St. Bernardus Abt 12",
   "style": "Belgian Strong Ale",
   "hop": "Amarillo",
   "yeast": "1388 - Belgian Strong Ale",
   "malts": "Caramel",
   "ibu": "43 IBU",
   "alcohol": "3.8%",
   "blg": "16.0°Blg"
 }
```
:::
::::

::: paragraph
We save this beer into a file named `sample.json` as we want to use this
as source for message transformation.
:::

::: paragraph
Now suppose we want to transform this to a smaller JSON document with
only a few elements, then we can do this directly in the route:
:::

:::: listingblock
::: content
``` highlight
- route:
    nodePrefixId: route-c38
    id: route-66b0
    from:
      uri: kamelet:beer-source
      id: from-3996
      steps:
        - setBody:
            expression:
              simple:
                expression: >-
                  TODO
                id: simple-b320
            id: setBody-fa01
        - log:
            message: ${body}
            id: log-0c79
```
:::
::::

::: paragraph
As we can see the route has a `setBody` where we want to transform the
message, which we can do with the simple language combined with JQ (or
JSonPath). At first, we just write TODO to get started:
:::

:::: listingblock
::: content
``` highlight
camel transform message --body=file:sample.json --source=beer-jq.yaml  --watch --pretty
```
:::
::::

::: paragraph
And the output from the transformation is:
:::

:::: listingblock
::: content
``` highlight
 Exchange  (DefaultExchange)  InOut   D9F909701338607-0000000000000004
 Message   (DefaultMessage)
 Body      (String) (bytes: 4)
 TODO
```
:::
::::

::: paragraph
We can now do live updates in the Camel route, and when we save the
file, the transform command will output the changes in the terminal. So
we modify the DSL to grab the details we want, such as:
:::

:::: listingblock
::: content
``` highlight
    expression: >-
      {
        "kind": "${jq(.brand)}",
        "beer": "${jq(.name)}"
      }
    id: simple-b320
```
:::
::::

::: paragraph
Notice how we use inlined JQ expressions to grab the desired data from
the sample, and when saving the file, we have the result shown in the
terminal:
:::

:::: listingblock
::: content
``` highlight
 Exchange  (DefaultExchange)  InOut   D9F909701338607-000000000000003E
 Message   (DefaultMessage)
 Body      (String) (bytes: 78)
 {
   "kind": "Pabst Blue Ribbon",
   "beer": "St. Bernardus Abt 12"
 }
```
:::
::::

::: paragraph
And when we are done with the transformation we can stop by `ctrl + c`
to exit the command.
:::

::: paragraph
When transforming messages directly from the DSL (using the `--source`
option), then by default Camel will pick the last expression in the
route. If you only have one expression like in this example, then that
is easy. But when you have many, then you need to tell which one to use.
:::

::: paragraph
You can do this either by referring to the line number in the source
code of the EIP / expression, or by its id (if specified).
:::

::: paragraph
So suppose the example had many expressions, and the one we want to use
is on line 11, then we can do as follows:
:::

:::: listingblock
::: content
``` highlight
camel transform message --body=file:sample.json --source=beer-jq.yaml:11  --watch --pretty
```
:::
::::

::: paragraph
Notice how we specify the line number in the source file name, by
prefixing with `:line-number`. The line number does not have to be 100%
accurate, as long the number is within range of the EIP, until the next
EIP in the route. In the example, this means you can pick number in
range 8-17.
:::

::: paragraph
If we want to use the id of the expression/EIP, we can do the same as
shown below:
:::

:::: listingblock
::: content
``` highlight
camel transform message --body=file:sample.json --source=beer-jq.yaml:setBody-fa01  --watch --pretty
```
:::
::::

::: paragraph
You can also transform the message from JSON to XML as shown (or any
other kind of fixed structure):
:::

:::: listingblock
::: content
``` highlight
- setBody:
    expression:
      simple:
        expression: >-
          <beer id="${jq(.id)}">
            <name>${jq(.name)}</name>
            <kind>${jq(.brand)}</kind>
          </beer>
        id: simple-b320
    id: setBody-fa01
```
:::
::::

::: paragraph
You can configure options that the language format should use, such as
shown below:
:::

:::: listingblock
::: content
``` highlight
$ camel transform message --body=file:sample.json --language=jsonpath --template="beer.unknown" --option=suppressExceptions=true
```
:::
::::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | You can specify multiple options  |
| Tip                               | by repeating the `--option`       |
| :::                               | argument.                         |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
Pay attention that we now use JSONPath as the language, and the template
is a tiny JSON path expression. Because the sample does not have a
`beer.unknown` path, then JSonPath would normally throw an exception.
This can be suppressed by setting the option `suppressExceptions` to
`true` as shown above. Then the output is as follows:
:::

:::: listingblock
::: content
``` highlight
2023-12-04 13:02:50.923  66291 --- Message transformed (success) (239ms)
 Exchange  (DefaultExchange)  InOut   6118686CA3995FF-0000000000000000
 Message   (DefaultMessage)
 Body      (null) (bytes: 14)
 [Body is null]
```
:::
::::
:::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::: sect2
### Transforming the message using Components {#_transforming_the_message_using_components}

::: paragraph
Some components can also be used for message transformation such as
FlatPack, Velocity, FreeMarker, Thymeleaf, and good old XSLT.
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | You can use                       |
| Tip                               | `camel cata                       |
| :::                               | log component --filter=transform` |
|                                   | to see which components can be    |
|                                   | transformation.                   |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
Given the below XML in the `sample.xml` file:
:::

:::: listingblock
::: content
``` highlight
<hash>
  <id type="integer">1369</id>
  <uid>8c946e1a-fdc5-40d3-9098-44271bdfad65</uid>
  <account-number>8673088731</account-number>
  <iban>GB38EFUA27474531363797</iban>
  <bank-name>ABN AMRO MEZZANINE (UK) LIMITED</bank-name>
  <routing-number>053228004</routing-number>
  <swift-bic>AACCGB21</swift-bic>
</hash>
```
:::
::::

::: paragraph
Then you can transform this using an XSLT named `mystyle.xsl`:
:::

:::: listingblock
::: content
``` highlight
<?xml version = "1.0"?>
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">

  <xsl:template match="/">
    <bank>
        <name><xsl:value-of select="/hash/bank-name/text()"/></name>
        <bic><xsl:value-of select="/hash/swift-bic/text()"/></bic>
    </bank>
  </xsl:template>

</xsl:stylesheet>
```
:::
::::

::: paragraph
Then you can do live changes to the stylesheet and see the output in
real-time with Camel JBang by running:
:::

:::: listingblock
::: content
``` highlight
$ camel transform message --body=file:sample.xml --component=xslt --template=file:mystyle.xsl --pretty --watch
```
:::
::::

::: paragraph
You can then edit the `mystyle.xsl` file, and save the file, and watch
the terminal for the updated result.
:::

::: paragraph
You can find this example at:
[https://github.com/apache/camel-kamelets-examples/tree/main/jbang/xslt-transform](https://github.com/apache/camel-kamelets-examples/tree/main/jbang/xslt-transform){.bare}
:::

::: paragraph
You can configure options that the XSLT component format should use,
such as shown below:
:::

:::: listingblock
::: content
``` highlight
$ camel transform message --body=file:sample.xml --component=xslt --template=file:mystyle.xsl --option=output=bytes --pretty --watch
```
:::
::::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | You can specify multiple options  |
| Tip                               | by repeating the `--option`       |
| :::                               | argument.                         |
+-----------------------------------+-----------------------------------+
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | The transform message with the    |
| Note                              | component is limited as some      |
| :::                               | components require configuring    |
|                                   | complex options that cannot be    |
|                                   | set from command line.            |
+-----------------------------------+-----------------------------------+
:::
:::::::::::::::::::::

:::::::::::::::::::: sect2
### Transforming a message using Data Formats {#_transforming_a_message_using_data_formats}

::: paragraph
Some data formats can also be used for message transformation such as
Base64, Csv, FlatPack.
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | You can use                       |
| Tip                               | `camel catal                      |
| :::                               | og dataformat --filter=transform` |
|                                   | to see which components can be    |
|                                   | transformation.                   |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
Given the below CSV file in the `daltons.csv`:
:::

:::: listingblock
::: content
``` highlight
Jack Dalton, 115, mad at Averell
Joe Dalton, 105, calming Joe
William Dalton, 105, keeping Joe from killing Averell
Averell Dalton, 80, playing with Rantanplan
Lucky Luke, 120, capturing the Daltons
```
:::
::::

::: paragraph
Then you can transform this via Camel CSV dataformat from its form into
Java objects (unmarshal). This will NOT transform the CSV into another
format such as XML, but allows you to quickly try out the dataformat,
and be able to inspect the Java object inside Camel.
:::

:::: listingblock
::: content
``` highlight
$ camel transform message --body=file:daltons.csv --dataformat=csv
```
:::
::::

::: paragraph
This will then output:
:::

:::: listingblock
::: content
``` highlight
2023-12-04 10:53:45.578  55793 --- Message transformed (success) (176ms)
 Exchange  (DefaultExchange)  InOut   6673987D34F3B54-0000000000000000
 Message   (DefaultMessage)
 Body      (ArrayList) (size: 5 bytes: 224)
 [[Jack Dalton,  115,  mad at Averell], [Joe Dalton,  105,  calming Joe], [William Dalton,  105,  keeping Joe from killing Averell], [Averell Dalton,  80,
 playing with Rantanplan], [Lucky Luke,  120,  capturing the Daltons]]
```
:::
::::

::: paragraph
As you can see Camel CSV dataformat will then unmarshal the input (from
CSV file) to a `java.util.ArrayList` object with 5 elements (one per
row), and each row is another List of the columns.
:::

::: paragraph
You can configure options that the CSV data format should use, such as
shown below:
:::

:::: listingblock
::: content
``` highlight
$ camel transform message --body=file:daltons.csv --dataformat=csv --option=captureHeaderRecord=true
```
:::
::::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | You can specify multiple options  |
| Tip                               | by repeating the `--option`       |
| :::                               | argument.                         |
+-----------------------------------+-----------------------------------+
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | The transform message with        |
| Note                              | dataformat is limited as some     |
| :::                               | dataformat requires configuring   |
|                                   | complex options that cannot be    |
|                                   | set from the command line.        |
+-----------------------------------+-----------------------------------+
:::
::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::: sect1
## Transforming routes DSL {#_transforming_routes_dsl}

:::::::::::::: sectionbody
::: paragraph
Camel offers different DSLs, such as Java, XML, YAML, and more. Many
graphical editors and code generators use XML and YAML Camel DSL,
therefore, for some use cases it may be required to use one DSL or the
other. The `camel transform route` command can be used to transform a
YAML route into an XML and vice versa, the command takes a list of files
as input and transforms the DSL.
:::

::: paragraph
For example, given the following YAML content, in a file named
route.yaml
:::

:::: listingblock
::: content
    - route:
        id: route-b785
        nodePrefixId: route-14b
        from:
          id: from-959e
          uri: direct
          parameters:
            name: start
          steps:
            - choice:
                id: choice-52fe
                when:
                  - id: when-b126
                    expression:
                      simple:
                        id: simple-576d
                        expression: ${header.foo} == 'bar'
                    steps:
                      - to:
                          id: to-65f9
                          uri: jms
                          parameters:
                            destinationName: queue
                otherwise:
                  id: otherwise-c07f
                  steps:
                    - to:
                        id: to-dc76
                        uri: log
                        parameters:
                          loggerName: default
:::
::::

::: paragraph
The following command
:::

:::: listingblock
::: content
    camel transform route --format=xml /path/to/route.yaml
:::
::::

::: paragraph
Automatically transform the route in XML DSL (Camel XML IO DSL)
:::

:::: listingblock
::: content
    <camel>
        <route id="route-b785" nodePrefixId="route-14b">
            <from id="from-959e" uri="direct:start"/>
            <choice id="choice-52fe">
                <when id="when-b126">
                    <simple>${header.foo} == 'bar'</simple>
                    <to id="to-65f9" uri="jms:queue"/>
                </when>
                <otherwise id="otherwise-c07f">
                    <to id="to-dc76" uri="log:default"/>
                </otherwise>
            </choice>
        </route>
    </camel>
:::
::::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | If all the input files have the   |
| Tip                               | same extension and are written    |
| :::                               | with the same DSL, for example,   |
|                                   | `.yaml`, the transformation will  |
|                                   | default to the XML DSL and vice   |
|                                   | versa.                            |
+-----------------------------------+-----------------------------------+
:::
::::::::::::::
:::::::::::::::

::::::::::::::::::::::::::::::::::::::::::: sect1
## Listing what Camel components are available {#_listing_what_camel_components_are_available}

:::::::::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
Camel comes with a lot of artifacts out of the box which comes as:
:::

::: ulist
- components

- data formats

- expression languages

- miscellaneous components

- kamelets
:::

::: paragraph
You can use the Camel CLI to list what Camel provides via the
`camel catalog` command. For example, to list all the components
:::

:::: listingblock
::: content
``` highlight
camel catalog component
```
:::
::::

::: paragraph
And to see which Kamelets are available:
:::

:::: listingblock
::: content
``` highlight
camel catalog kamelet
```
:::
::::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | Use `camel catalog --help` to see |
| Tip                               | all possible commands.            |
| :::                               |                                   |
+-----------------------------------+-----------------------------------+
:::

:::::::::::::::::::::::::::::::: sect2
### Displaying component documentation {#_displaying_component_documentation}

::: paragraph
The `doc` goal can show quick documentation for every component,
dataformat, kamelets etc. For example, to see the kafka component you
run:
:::

:::: listingblock
::: content
``` highlight
camel doc kafka
```
:::
::::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | The documentation is not the full |
| Note                              | documentation as shown on the     |
| :::                               | website, as the Camel CLI does    |
|                                   | not have direct access to this    |
|                                   | information and can only show a   |
|                                   | basic description of the          |
|                                   | component, but include tables for |
|                                   | every configuration option.       |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
To see the documentation for jackson dataformat:
:::

:::: listingblock
::: content
``` highlight
camel doc jackson
```
:::
::::

::: paragraph
In some rare cases then there may be a component and dataformat with the
same name, and the `doc` goal prioritizes components. In such a
situation you can prefix the name with dataformat, i.e.:
:::

:::: listingblock
::: content
``` highlight
camel doc dataformat:thrift
```
:::
::::

::: paragraph
You can also see the kamelet documentation such as shown:
:::

:::: listingblock
::: content
``` highlight
camel doc aws-kinesis-sink
```
:::
::::

:::::::::: sect3
#### Browsing online documentation from the Camel website {#_browsing_online_documentation_from_the_camel_website}

::: paragraph
You can use the `doc` command to quickly open the url in the web browser
for the online documentation. For example, to browse the kafka
component, you use `--open-url`:
:::

:::: listingblock
::: content
``` highlight
camel doc kafka --open-url
```
:::
::::

::: paragraph
This also works for data formats, languages, kamelets etc.
:::

:::: listingblock
::: content
``` highlight
camel doc aws-kinesis-sink --open-url
```
:::
::::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | To just get the link to the       |
| Tip                               | online documentation, then use    |
| :::                               | `camel doc kafka --url`.          |
+-----------------------------------+-----------------------------------+
:::
::::::::::

:::::::::: sect3
#### Filtering options listed in the tables {#_filtering_options_listed_in_the_tables}

::: paragraph
Some components may have many options, and in such cases you may use
`--filter` to only list options that match the filter in either name,
description, or the group (producer, security, advanced, etc).
:::

::: paragraph
For example, to list only security related options:
:::

:::: listingblock
::: content
``` highlight
camel doc kafka --filter=security
```
:::
::::

::: paragraph
And to list only something about *timeout*:
:::

:::: listingblock
::: content
``` highlight
camel doc kafka --filter=timeout
```
:::
::::
::::::::::
::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::

:::::::: sect1
## Open API {#_open_api}

::::::: sectionbody
::: paragraph
Camel JBang allows quickly exposing an Open API service using
*contract-first* approach, where you have an existing OpenAPI
specification file.
:::

::: paragraph
Then Camel JBang is able to bridge each API endpoint from the OpenAPI
specification to a Camel route with the naming convention
`direct:<operationId>`.
:::

::: paragraph
This make it quick to implement a Camel route for a given operation.
:::

::: paragraph
See the [open-api
example](https://github.com/apache/camel-kamelets-examples/tree/main/jbang/open-api)
for more details.
:::
:::::::
::::::::

:::::::::::::::: sect1
## Gathering list of dependencies {#_gathering_list_of_dependencies}

::::::::::::::: sectionbody
::: paragraph
When working with Camel JBang, then dependencies are automatically
resolved. This means that you do not have to use a build system like
Maven or Gradle to add every Camel component as a dependency.
:::

::: paragraph
However, you may want to know what dependencies are required to run the
Camel integration. To see this, you can use the `dependency` command.
The command output does not output a detailed tree, such as
`mvn dependencies:tree`, as the output is intended to list which Camel
components, and other JARs needed (when using Kamelets).
:::

::: paragraph
The dependency output by default is *vanilla* Apache Camel with the
camel-main as runtime, as shown below:
:::

:::: listingblock
::: content
``` highlight
camel dependency list
org.apache.camel:camel-dsl-modeline:3.20.0
org.apache.camel:camel-health:3.20.0
org.apache.camel:camel-kamelet:3.20.0
org.apache.camel:camel-log:3.20.0
org.apache.camel:camel-rest:3.20.0
org.apache.camel:camel-stream:3.20.0
org.apache.camel:camel-timer:3.20.0
org.apache.camel:camel-yaml-dsl:3.20.0
org.apache.camel.kamelets:camel-kamelets:0.9.3
```
:::
::::

::: paragraph
The output is by default a line per maven dependency in GAV format
(*groupId:artifactId:version*).
:::

::: paragraph
You can also specify the output should be in *Maven format* as shown:
:::

:::: listingblock
::: content
``` highlight
camel dependency list --output=maven
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-main</artifactId>
    <version>3.20.0</version>
</dependency>
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-dsl-modeline</artifactId>
    <version>3.20.0</version>
</dependency>
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-health</artifactId>
    <version>3.20.0</version>
</dependency>
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-kamelet</artifactId>
    <version>3.20.0</version>
</dependency>
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-log</artifactId>
    <version>3.20.0</version>
</dependency>
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-rest</artifactId>
    <version>3.20.0</version>
</dependency>
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-stream</artifactId>
    <version>3.20.0</version>
</dependency>
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-timer</artifactId>
    <version>3.20.0</version>
</dependency>
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-yaml-dsl</artifactId>
    <version>3.20.0</version>
</dependency>
<dependency>
    <groupId>org.apache.camel.kamelets</groupId>
    <artifactId>camel-kamelets</artifactId>
    <version>0.9.3</version>
</dependency>
```
:::
::::

::: paragraph
You can also choose the target runtime as either *quarkus* or
*spring-boot* as shown:
:::

:::: listingblock
::: content
``` highlight
camel dependency --runtime=spring-boot
org.springframework.boot:spring-boot-starter-actuator:2.7.5
org.springframework.boot:spring-boot-starter-web:2.7.5
org.apache.camel.springboot:camel-spring-boot-engine-starter:3.20.0
org.apache.camel.springboot:camel-dsl-modeline-starter:3.20.0
org.apache.camel.springboot:camel-kamelet-starter:3.20.0
org.apache.camel.springboot:camel-log-starter:3.20.0
org.apache.camel.springboot:camel-rest-starter:3.20.0
org.apache.camel.springboot:camel-stream-starter:3.20.0
org.apache.camel.springboot:camel-timer-starter:3.20.0
org.apache.camel.springboot:camel-yaml-dsl-starter:3.20
org.apache.camel.kamelets:camel-kamelets:0.9.3
```
:::
::::
:::::::::::::::
::::::::::::::::

::::::::::::::::::::::::: sect1
## Generating SBOM report {#_generating_sbom_report}

:::::::::::::::::::::::: sectionbody
:::: listingblock
::: content
``` highlight
You can generate a SBOM report of your integration by running the following command
```
:::
::::

:::: listingblock
::: content
    camel sbom
:::
::::

::: paragraph
By default, it will generate a file named `sbom.json` in `cyclonedx`
format.
:::

::: paragraph
You can also choose to use an `spdx` format with the following command
:::

:::: listingblock
::: content
    camel sbom --sbom-format=spdx
:::
::::

::: paragraph
You can also choose the target runtime as either *quarkus* or
*spring-boot* as shown:
:::

:::: listingblock
::: content
    camel sbom --runtime=quarkus
:::
::::

::: paragraph
or
:::

:::: listingblock
::: content
    camel sbom --runtime=spring-boot
:::
::::

::: paragraph
by default `camel-main` will be used
:::

:::::::: sect2
### Copying dependency JARs to a specific directory {#_copying_dependency_jars_to_a_specific_directory}

::: paragraph
You can use the `camel dependency copy` command to copy the required
JARs to a specific folder.
:::

::: {.admonitionblock .important}
+-----------------------------------+-----------------------------------+
| ::: title                         | The `camel dependency copy` and   |
| Important                         | `camel dependency list` uses      |
| :::                               | Apache Maven, This requires       |
|                                   | having Apache Maven installed,    |
|                                   | and `mvn` command in PATH         |
|                                   | environment, so Camel JBang can   |
|                                   | invoke `mvn` command.             |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
By default, the JARs are copied to `lib` folder:
:::

:::: listingblock
::: content
``` highlight
camel dependency copy
ls lib
camel-api-3.21.0.jar                    camel-health-3.21.0.jar                 camel-yaml-dsl-3.21.0.jar
camel-base-3.21.0.jar                   camel-main-3.21.0.jar                   camel-yaml-dsl-common-3.21.0.jar
camel-base-engine-3.21.0.jar            camel-management-api-3.21.0.jar         camel-yaml-dsl-deserializers-3.21.0.jar
camel-core-engine-3.21.0.jar            camel-rest-3.21.0.jar                   jakarta.activation-api-1.2.2.jar
camel-core-languages-3.21.0.jar         camel-support-3.21.0.jar                jakarta.xml.bind-api-2.3.3.jar
camel-core-model-3.21.0.jar             camel-timer-3.21.0.jar                  slf4j-api-1.7.36.jar
camel-core-processor-3.21.0.jar         camel-tooling-model-3.21.0.jar          snakeyaml-engine-2.3.jar
camel-core-reifier-3.21.0.jar           camel-util-3.21.0.jar
camel-dsl-support-3.21.0.jar            camel-util-json-3.21.0.jar
```
:::
::::
::::::::
::::::::::::::::::::::::
:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Creating Projects {#_creating_projects}

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
You can *export* your Camel JBang integration to a traditional
Java-based project such as Spring Boot or Quarkus.
:::

::: paragraph
You may want to do this after you have built a prototype using Camel
JBang, and are in need of a traditional Java-based project with more
need for Java coding, or wanting to use the powerful runtimes of Spring
Boot, Quarkus or vanilla Camel Main.
:::

::::::::::::::: sect2
### Exporting to Camel Spring Boot {#_exporting_to_camel_spring_boot}

::: paragraph
The command `export --runtime=spring-boot` will export your current
Camel JBang file(s) to a Maven-based Spring Boot project with files
organized in `src/main/` folder structure.
:::

::: paragraph
For example, to export to Spring Boot using the Maven groupId
*`com.foo`* and the artifactId *acme* and with version *1.0-SNAPSHOT*
you execute:
:::

:::: listingblock
::: content
``` highlight
camel export --runtime=spring-boot --gav=com.foo:acme:1.0-SNAPSHOT
```
:::
::::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | This will export to the *current* |
| Note                              | directory, meaning that files are |
| :::                               | moved into the needed folder      |
|                                   | structure.                        |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
To export to another directory (copies the files), you execute:
:::

:::: listingblock
::: content
``` highlight
camel export --runtime=spring-boot --gav=com.foo:acme:1.0-SNAPSHOT --directory=../myproject
```
:::
::::

::: paragraph
When exporting to Spring Boot then the Camel version defined in the
`pom.xml` or `build.gradle` is the same version as Camel JBang uses.
However, you can specify the Camel version as shown below:
:::

:::: listingblock
::: content
``` highlight
camel export --runtime=spring-boot --gav=com.foo:acme:1.0-SNAPSHOT --directory=../myproject --camel-spring-boot-version=3.18.3
```
:::
::::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | See the possible options by       |
| Tip                               | running: `camel export --help`    |
| :::                               | for more details.                 |
+-----------------------------------+-----------------------------------+
:::
:::::::::::::::

::::::::::::: sect2
### Exporting to Camel Quarkus {#_exporting_to_camel_quarkus}

::: paragraph
The command `export --runtime=quarkus` will export your current Camel
JBang file(s) to a Maven-based Quarkus project with files organized in
`src/main/` folder structure.
:::

::: paragraph
For example, to export to Quarkus using the Maven groupId *`com.foo`*
and the artifactId *acme* and with version *1.0-SNAPSHOT* you simply
execute:
:::

:::: listingblock
::: content
``` highlight
camel export --runtime=quarkus --gav=com.foo:acme:1.0-SNAPSHOT
```
:::
::::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | This will export to the *current* |
| Note                              | directory, meaning that files are |
| :::                               | moved into the necessary folder   |
|                                   | structure.                        |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
To export to another directory (copies the files) you execute:
:::

:::: listingblock
::: content
``` highlight
camel export --runtime=quarkus --gav=com.foo:acme:1.0-SNAPSHOT --directory=../myproject
```
:::
::::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | See the possible options by       |
| Tip                               | running: `camel export --help`    |
| :::                               | for more details.                 |
+-----------------------------------+-----------------------------------+
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | You cannot use `--profile` option |
| Note                              | when exporting to Camel Quarkus.  |
| :::                               |                                   |
+-----------------------------------+-----------------------------------+
:::
:::::::::::::

:::::::::::::::::::::::: sect2
### Exporting to Camel Main {#_exporting_to_camel_main}

::: paragraph
The command `export --runtime=camel-main` will export your current Camel
JBang file(s) to a Maven-based vanilla Camel Main project with files
organized in `src/main/` folder structure.
:::

::: paragraph
For example, to export to Camel Main using the Maven groupId *com.foo*
and the artifactId *acme* and with version *1.0-SNAPSHOT* you simply
execute:
:::

:::: listingblock
::: content
``` highlight
camel export --runtime=camel-main --gav=com.foo:acme:1.0-SNAPSHOT
```
:::
::::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | This will export to the *current* |
| Note                              | directory, meaning that files are |
| :::                               | moved into the needed folder      |
|                                   | structure.                        |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
To export to another directory (copies the files), you execute:
:::

:::: listingblock
::: content
``` highlight
camel export --runtime=camel-main --gav=com.foo:acme:1.0-SNAPSHOT --directory=../myproject
```
:::
::::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | See the possible options by       |
| Tip                               | running: `camel export --help`    |
| :::                               | for more details.                 |
+-----------------------------------+-----------------------------------+
:::

:::::::::::::: sect3
#### Exporting with Java Agent included {#_exporting_with_java_agent_included}

::: paragraph
The Camel Main runtime allows exporting with Java Agents that makes it
easier to run your Camel integration with Java Agents such as
OpenTelemetry Agent.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | Only `camel-main` as runtime      |
| Note                              | supports exporting with Java      |
| :::                               | Agents included.                  |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
This requires specifying the agent as a dependency with `agent:` as
prefix as shown below in `application.properties`:
:::

:::: listingblock
::: content
``` highlight
camel.jbang.dependencies=camel:opentelemetry,agent:io.opentelemetry.javaagent:opentelemetry-javaagent:1.31.0
camel.opentelemetry.enabled=true
```
:::
::::

::: paragraph
Then you can export to `--runtime=camel-main` as follows:
:::

:::: listingblock
::: content
``` highlight
camel export --runtime=camel-main --gav=com.foo:acme:1.0-SNAPSHOT --directory=../myproject
```
:::
::::

::: paragraph
Then Camel JBang will detect the `agent:` dependency and download this
from Maven and save to a `../myproject/agent` folder. You can then start
the Camel integration from Java via:
:::

:::: listingblock
::: content
``` highlight
cd ../myproject
mvn clean package
java -javaagent:agent/opentelemetry-javaagent-1.31.0.jar -jar target/acme-1.0-SNAPSHOT.jar
```
:::
::::
::::::::::::::
::::::::::::::::::::::::

::::::::::: sect2
### Exporting with selected files {#_exporting_with_selected_files}

::: paragraph
By default, Camel will export what was last run, or all files from the
current directory. You can also explicitly specify which files should be
exported.
:::

::: paragraph
For example, you may have 3 files in a folder:
:::

::: ulist
- Foo.java

- bar.xml

- cheese.yaml
:::

::: paragraph
These are 3 Camel routes, and you want to export them into 2 different
applications:
:::

:::: listingblock
::: content
``` highlight
camel export Foo.java --runtime=quarkus --gav=com.foo:acme:1.0-SNAPSHOT --directory=../export1
camel export bar.xml cheese.yaml --runtime=spring-boot --gav=com.foo:cheese:1.0-SNAPSHOT --directory=../export2
```
:::
::::

::: paragraph
As you can see the first export command will only include `Foo.java`,
and then 2nd export includes both `bar.xml` and `cheese.yaml`.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | If you have                       |
| Note                              | `application.properties` in the   |
| :::                               | folder as well, then this will be |
|                                   | included in both exports.         |
+-----------------------------------+-----------------------------------+
:::
:::::::::::

:::::: sect2
### Exporting as Gradle Project {#_exporting_as_gradle_project}

::: paragraph
Camel JBang exports by default as a Maven-based project. To use Gradle
instead, you can specify the `--build-tool=gradle` when exporting, such
as:
:::

:::: listingblock
::: content
``` highlight
camel export --build-tool=gradle --runtime=spring-boot --gav=com.foo:acme:1.0-SNAPSHOT --directory=../myproject
```
:::
::::
::::::

:::::: sect2
### Exporting with JMX management included {#_exporting_with_jmx_management_included}

::: paragraph
Usually when exporting to Spring Boot, Quarkus or Camel Main, then JMX
management is not included out of the box. To include JMX, you need to
add `camel:management` in the `--dep` option, as shown below:
:::

:::: listingblock
::: content
``` highlight
camel export --runtime=quarkus --gav=com.foo:acme:1.0-SNAPSHOT --dep=camel:management --directory=../myproject
```
:::
::::
::::::

:::::: sect2
### Exporting with Camel CLI included {#_exporting_with_camel_cli_included}

::: paragraph
Usually when exporting to Spring Boot, Quarkus or Camel Main, then Camel
JBang CLI is not included out of the box. To be able to continue to use
Camel CLI (i.e. `camel`), you need to add `camel:cli-connector` in the
`--dep` option, as shown below:
:::

:::: listingblock
::: content
``` highlight
camel export --runtime=quarkus --gav=com.foo:acme:1.0-SNAPSHOT --dep=camel:cli-connector --directory=../myproject
```
:::
::::
::::::

:::::: sect2
### Troubleshooting exporting {#_troubleshooting_exporting}

::: paragraph
When exporting then Camel CLI performs a set of tasks which can go
wrong. To see more activity during exporting you can turn on both
`--verbose` and `--logging` that prints more information to the console.
You can also set `--logging-level=DEBUG` to output even more details.
:::

::: paragraph
If your Camel application cannot be exported, for example due to some
custom Java code, you can try to export with `--ignore-loading-error`.
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | Camel will by default store       |
| Tip                               | export logs to                    |
| :::                               | `<us                              |
|                                   | er home>/.camel/camel-export.log` |
|                                   | file of the last export run       |
|                                   | (unless you turn on `--logging`   |
|                                   | that logs only to console).       |
+-----------------------------------+-----------------------------------+
:::
::::::

:::::: sect2
### Configuring exporting {#_configuring_exporting}

::: paragraph
The export command will by default load configuration from
`application.properties` which also can be used to specific parameters
for export such as selecting the runtime and java version.
:::

::: paragraph
The follow options related to *exporting*, can be configured in
`application.properties`:
:::

+-----------------------------------+-----------------------------------+
| Option                            | Description                       |
+===================================+===================================+
| `camel.jbang.runtime`             | Runtime (spring-boot, quarkus, or |
|                                   | camel-main)                       |
+-----------------------------------+-----------------------------------+
| `camel.jbang.gav`                 | The Maven group:artifact:version  |
+-----------------------------------+-----------------------------------+
| `camel.jbang.dependencies`        | Additional dependencies (Use      |
|                                   | commas to separate multiple       |
|                                   | dependencies). See more details   |
|                                   | at [Adding custom                 |
|                                   | JARs](#_adding_custom_jars).      |
+-----------------------------------+-----------------------------------+
| `camel.jbang.excludes`            | Exclude files by name or pattern. |
|                                   | Multiple names can be separated   |
|                                   | by comma.                         |
+-----------------------------------+-----------------------------------+
| `camel.jbang.classpathFiles`      | Additional files to add to        |
|                                   | classpath (Use commas to separate |
|                                   | multiple files). See more details |
|                                   | at [Adding custom                 |
|                                   | JARs](#_adding_custom_jars).      |
+-----------------------------------+-----------------------------------+
| `camel.jbang.jkubeFiles`          | Resource fragments for Kubernetes |
|                                   | (Use commas to separate multiple  |
|                                   | files).                           |
+-----------------------------------+-----------------------------------+
| `camel.jbang.javaVersion`         | Java version (17 or 21). Java 17  |
|                                   | is default.                       |
+-----------------------------------+-----------------------------------+
| `camel.jbang.kameletsVersion`     | Apache Camel Kamelets version     |
+-----------------------------------+-----------------------------------+
| `camel.jbang.localKameletDir`     | Local directory for loading       |
|                                   | Kamelets                          |
+-----------------------------------+-----------------------------------+
| `ca                               | Camel version to use with Spring  |
| mel.jbang.camelSpringBootVersion` | Boot                              |
+-----------------------------------+-----------------------------------+
| `camel.jbang.springBootVersion`   | Spring Boot version               |
+-----------------------------------+-----------------------------------+
| `camel.jbang.quarkusGroupId`      | Quarkus Platform Maven groupId    |
+-----------------------------------+-----------------------------------+
| `camel.jbang.quarkusArtifactId`   | Quarkus Platform Maven artifactId |
+-----------------------------------+-----------------------------------+
| `camel.jbang.quarkusVersion`      | Quarkus Platform version          |
+-----------------------------------+-----------------------------------+
| `camel.jbang.mavenWrapper`        | Include Maven Wrapper files in    |
|                                   | the exported project              |
+-----------------------------------+-----------------------------------+
| `camel.jbang.gradleWrapper`       | Include Gradle Wrapper files in   |
|                                   | the exported project              |
+-----------------------------------+-----------------------------------+
| `camel.jbang.buildTool`           | Build tool to use (Maven or       |
|                                   | Gradle)                           |
+-----------------------------------+-----------------------------------+
| `camel.jbang.repos`               | Additional maven repositories for |
|                                   | download on-demand (Use commas to |
|                                   | separate multiple repositories)   |
+-----------------------------------+-----------------------------------+
| `camel.jbang.mavenSettings`       | Optional location of Maven        |
|                                   | settings.xml file to configure    |
|                                   | servers, repositories, mirrors,   |
|                                   | and proxies. If set to false, not |
|                                   | even the default                  |
|                                   | \~/.m2/settings.xml will be used. |
+-----------------------------------+-----------------------------------+
| `c                                | Optional location of Maven        |
| amel.jbang.mavenSettingsSecurity` | settings-security.xml file to     |
|                                   | decrypt settings.xml              |
+-----------------------------------+-----------------------------------+
| `camel.jbang.mavenCentralEnabled` | Whether downloading JARs from     |
|                                   | Maven Central repository is       |
|                                   | enabled                           |
+-----------------------------------+-----------------------------------+
| `camel.                           | Whether downloading JARs from ASF |
| jbang.mavenApacheSnapshotEnabled` | Maven Snapshot repository is      |
|                                   | enabled                           |
+-----------------------------------+-----------------------------------+
| `camel.jbang.exportDir`           | Directory where the project will  |
|                                   | be exported                       |
+-----------------------------------+-----------------------------------+
| `camel.jbang.platform-http.port`  | HTTP server port to use when      |
|                                   | running standalone Camel, such as |
|                                   | when `--console` is enabled (port |
|                                   | 8080 by default).                 |
+-----------------------------------+-----------------------------------+
| `camel.jbang.console`             | Developer console at /q/dev on    |
|                                   | local HTTP server (port 8080 by   |
|                                   | default) when running standalone  |
|                                   | Camel                             |
+-----------------------------------+-----------------------------------+
| `camel.jbang.info`                | Info console at /q/info on local  |
|                                   | HTTP server (port 8080 by         |
|                                   | default) when running standalone  |
|                                   | Camel                             |
+-----------------------------------+-----------------------------------+
| `camel.jbang.health`              | Health check at /q/health on      |
|                                   | local HTTP server (port 8080 by   |
|                                   | default) when running standalone  |
|                                   | Camel                             |
+-----------------------------------+-----------------------------------+
| `camel.jbang.metrics`             | Metrics (Micrometer and           |
|                                   | Prometheus) at /q/metrics on      |
|                                   | local HTTP server (port 8080 by   |
|                                   | default) when running standalone  |
|                                   | Camel                             |
+-----------------------------------+-----------------------------------+
| `camel.jbang.open-api`            | File name of open-api spec file   |
|                                   | (JSON or YAML) that are used when |
|                                   | using `--open-api` to generate    |
|                                   | routes from the swagger/openapi   |
|                                   | API spec file.                    |
+-----------------------------------+-----------------------------------+
| `camel.jbang.ignoreLoadingError`  | Whether to ignore route loading   |
|                                   | and compilation errors (use this  |
|                                   | with care!)                       |
+-----------------------------------+-----------------------------------+
| `came                             | Version to use for                |
| l.jbang.jib-maven-plugin-version` | jib-maven-plugin if exporting to  |
|                                   | camel-main and have Kubernetes    |
|                                   | enabled (jkube.xxx options).      |
+-----------------------------------+-----------------------------------+

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | These are options from the export |
| Note                              | command, so you can see more      |
| :::                               | details and default values using  |
|                                   | `camel export --help`.            |
+-----------------------------------+-----------------------------------+
:::
::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Configuration {#_configuration}

:::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
Camel JBang `config` command is used to store and use the user
configuration. This eliminates the need to specify CLI options each
time. For example, to run a different Camel version, instead of
executing
:::

:::: listingblock
::: content
``` highlight
camel run * --camel-version=3.18.4
```
:::
::::

::: paragraph
the camel-version can be added to the user configuration such as:
:::

:::: listingblock
::: content
``` highlight
camel config set camel-version=3.18.4
```
:::
::::

::: paragraph
Now, the run command picks the user configuration:
:::

:::: listingblock
::: content
``` highlight
camel run *
```
:::
::::

::::: sect2
### Configuration Locations {#_configuration_locations}

::: paragraph
Camel JBang uses two possible configuration files:
:::

::: ulist
- Global configuration: \~/.camel-jbang-user.properties

- Local configuration: ./camel-jbang-user.properties
:::
:::::

:::: sect2
### Configuration Precedence {#_configuration_precedence}

::: paragraph
When both files exist, the local configuration takes precedence over the
global configuration.
:::
::::

:::::::::: sect2
### Using Configuration Commands {#_using_configuration_commands}

::: paragraph
All camel config commands target the global configuration by default. To
work with the local configuration instead, add the `--global=false`
flag:
:::

:::: listingblock
::: content
``` highlight
camel config <command> --global=false
```
:::
::::

::: paragraph
This directs all configuration changes to the local
./camel-jbang-user.properties file.
:::

::: {.admonitionblock .important}
+-----------------------------------+-----------------------------------+
| ::: title                         | You cannot use both a set version |
| Important                         | via `camel config set` and also a |
| :::                               | version specified via             |
|                                   | `--camel-version` option, i.e.,   |
|                                   | the following is not possible:    |
+-----------------------------------+-----------------------------------+
:::

:::: listingblock
::: content
``` highlight
camel config set camel-version=4.0.1
camel run * --camel-version=4.3.0
```
:::
::::
::::::::::

::::::::: sect2
### Set and unset configuration {#_set_and_unset_configuration}

::: paragraph
Every Camel JBang option is added to the user configuration. For
example, to export a simple project such as
:::

:::: listingblock
::: content
``` highlight
camel init foo.yaml
camel config set gav=com.foo:acme:1.0-SNAPSHOT
camel config set runtime=spring-boot
camel config set deps=org.apache.camel.springboot:camel-timer-starter
camel config set camel-spring-boot-version=3.20.1

camel export
```
:::
::::

::: paragraph
User configuration keys are unset using the following:
:::

:::: listingblock
::: content
``` highlight
camel config unset camel-spring-boot-version
```
:::
::::
:::::::::

:::::::::::: sect2
### List and get configurations {#_list_and_get_configurations}

::: paragraph
User configuration keys are listed using the following:
:::

:::: listingblock
::: content
``` highlight
camel config list
```
:::
::::

::: paragraph
Following is the output for the above-mentioned configuration.
:::

:::: listingblock
::: content
``` highlight
runtime = spring-boot
deps = org.apache.camel.springboot:camel-timer-starter
gav = com.foo:acme:1.0-SNAPSHOT
```
:::
::::

::: paragraph
To obtain a value for the given key, use the `get` command.
:::

:::: listingblock
::: content
``` highlight
camel config get gav

com.foo:acme:1.0-SNAPSHOT
```
:::
::::
::::::::::::

:::::::: sect2
### Placeholders\' substitutes {#_placeholders_substitutes}

::: paragraph
User configuration values can be used as placeholder substitutes with
command line properties, for example:
:::

:::: listingblock
::: content
``` highlight
camel config set repos=https://maven.repository.my.repo.com/ga

camel run 'Test.java' --logging-level=info --repos=#repos,https://packages.atlassian.com/maven-external
```
:::
::::

::: paragraph
In this example, since `repos` is set in the user configuration
(`config set`) and the `camel run` command declares the placeholder
`#repos`, `camel run` will replace the placeholder so that both
repositories will be used during the execution. Notice, that to refer to
the configuration value the syntax is `#optionName` eg `#repos`.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | The placeholder substitution only |
| Note                              | works for every option that a     |
| :::                               | given Camel command has. You can  |
|                                   | see all the options a command has |
|                                   | via the help, eg                  |
|                                   | `camel run --help`.               |
+-----------------------------------+-----------------------------------+
:::
::::::::
::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::

::::::: sect1
## Troubleshooting {#_troubleshooting}

:::::: sectionbody
::: paragraph
When using [JBang](https://www.jbang.dev/) then JBang stores state in
`~/.jbang` directory. This is also the location where JBang stores
downloaded JARs.
:::

::: paragraph
Camel JBang also downloads needed dependencies while running. However,
these dependencies are downloaded to your local Maven repository
`~/.m2`.
:::

::: paragraph
So if you find problems with running Camel JBang using what is seems
like an outdated JAR, then you can try to delete these directories or
parts of it.
:::
::::::
:::::::

:::::::::::::::::::::::::::::::::::::: sect1
## Infrastructure {#_infrastructure}

::::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
Camel JBang `infra` command can be used to `list`, `run` and `stop`
external services that can be used for fast prototyping and testing.
Apache Camel test-infra Services are exposed via the `infra` command, in
this way, the same infrastructure that is used to test Apache Camel
itself, is exposed via Camel JBang, and services that are already proved
to work with Apache Camel can be easily used.
:::

::::::::: sect2
### List Services {#_list_services}

::: paragraph
Supported external services can be listed with the following command:
:::

:::: listingblock
::: content
``` highlight
camel infra list
```
:::
::::

::: paragraph
Following is the output for the above-mentioned configuration.
:::

:::: listingblock
::: content
``` highlight
ALIAS                   IMPLEMENTATION
 ignite
 azure                   storage-queue
 openldap
 event-bridge
 xmpp
 kinesis
 redis
 ...
```
:::
::::
:::::::::

::::::::::::: sect2
### Run Service {#_run_service}

::: paragraph
Supported external services can be run with the following command
(Docker or Podman is required for most of the service):
:::

:::: listingblock
::: content
``` highlight
camel infra run $SERVICE ($OPTIONAL_SERVICE_IMPLEMENTATION)
```
:::
::::

::: paragraph
Once the service is up and running, information about the service are
printed as json that can be easily used in the Camel Routes, for
example:
:::

:::: listingblock
::: content
``` highlight
$ camel infra run ftp

Starting service ftp
{
  "getPort" : 52472,
  "getFtpRootDir" : "file://path/to/current/directory/target/ftp/camel-test-infra-test-directory/camel-test-infra-configuration-test-directory"
}
```
:::
::::

:::: listingblock
::: content
``` highlight
$ camel infra run kafka redpanda

Starting service kafka with implementation redpanda
{
  "getBootstrapServers" : "localhost:32771"
}
```
:::
::::

:::: listingblock
::: content
``` highlight
$ camel infra run openldap

Starting service openldap
{
  "getPort" : 32774,
  "getSslPort" : 32775,
  "getHost" : "localhost"
}
```
:::
::::
:::::::::::::

:::::: sect2
### Stop Service {#_stop_service}

::: paragraph
Stop a running infrastructure service:
:::

:::: listingblock
::: content
``` highlight
$ camel infra run arangodb
...

$ camel infra ps
 ALIAS             IMPLEMENTATION  DESCRIPTION
 arangodb                          ArangoDB is a multi-model database for high-performance applications.

$ camel infra stop arangodb
Shutting down service arangodb (PID: {pidNumber})
```
:::
::::
::::::

:::::::: sect2
### List running services {#_list_running_services}

::: paragraph
Display running services, useful to retrieve the service ALIAS
:::

:::: listingblock
::: content
``` highlight
$ camel infra run arangodb
...

$ camel infra ps
 ALIAS             IMPLEMENTATION  DESCRIPTION
 arangodb                          ArangoDB is a multi-model database for high-performance applications.
---

=== Get running service

Display running service information, the command will print the json containing information about the running service (ex. host, port, username)

[source,bash]
```
:::
::::

::: paragraph
\$ camel infra run openldap ...​
:::

::: paragraph
\$ camel infra get openldap { \"getPort\" : 32774, \"getSslPort\" :
32775, \"getHost\" : \"localhost\" } \-\--
:::
::::::::

::::::: sect2
### Get running services logs {#_get_running_services_logs}

::: paragraph
The command `camel infra log` gathers and displays the log of all
running services (similar to tail command)
:::

:::: listingblock
::: content
``` highlight
$ camel infra log
[service1] ...
[service2] ...
[service1] ...
[service2] ...
[service2] ...
[service2] ...
[service1] ...
[service2] ...
[service1] ...
[service1] ...
[service1] ...
---

In order to retrieve the log of a specific service the service alias can be provided

[source,bash]
```
:::
::::

::: paragraph
\$ camel infra log service \[service\] ...​ \[service\] ...​ \[service\]
...​ \-\--
:::
:::::::
:::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::: sect1
## Update {#_update}

::::::::::::::::::::::::::::::: sectionbody
::: paragraph
Apache Camel applications can be automatically updated using Camel
JBang. The update command provides two main operations:
:::

::: ulist
- `list`: Shows available Apache Camel versions for updating

- `run`: Executes the actual update process
:::

::: paragraph
The update process leverages the [Apache Camel Open Rewrite
recipes](https://github.com/apache/camel-upgrade-recipes) and supports
three application types:
:::

::: ulist
- Plain Camel (camel-main)

- Camel Quarkus

- Camel Spring Boot
:::

::: paragraph
While Camel and Camel Spring Boot updates primarily use
camel-upgrade-recipes, Camel Quarkus updates involve both the Quarkus
runtime (via [Rewrite
Quarkus](https://github.com/openrewrite/rewrite-quarkus)) and Apache
Camel recipes.
:::

:::::: sect2
### Listing Available Updates {#_listing_available_updates}

::: paragraph
To see which versions are available for updating, use:
:::

:::: listingblock
::: content
``` highlight
$ camel update list

 VERSION                       RUNTIME            RUNTIME VERSION  DESCRIPTION
 4.4.4                         Camel Quarkus      3.8.x            Migrates `camel 4.0` quarkus application to `camel 4.4`.
 4.8.0                         Camel                               Migrates Apache Camel 4 application to Apache Camel 4.8.0
 4.8.3                         Camel Quarkus      3.15.x           Migrates `camel 4.4` quarkus application to `camel 4.8`.
 4.9.0                         Camel                               Migrates Apache Camel 4 application to Apache Camel 4.9.0
 4.9.0                         Camel Spring Boot  3.4.0            Migrates Apache Camel Spring Boot 4 application to Apache Camel Spring Boot 4.9.0
```
:::
::::
::::::

::::::::::::::::::::: sect2
### Running Updates {#_running_updates}

::: paragraph
To perform an update to a specific version:
:::

:::: listingblock
::: content
``` highlight
$ camel update run $VERSION
```
:::
::::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | The update commands must be       |
| Note                              | executed in the project directory |
| :::                               | containing the pom.xml file.      |
+-----------------------------------+-----------------------------------+
:::

:::::: sect3
#### Configuration Options {#_configuration_options}

::: paragraph
The update process can be customized with several options: `--runtime`:
Specifies the application type:
:::

::: ulist
- `camel-main` - Plain Camel applications

- `spring-boot` - Camel Spring Boot applications

- `quarkus` - Camel Quarkus applications
:::

::: paragraph
`--repos`: Additional Maven repositories to use during the update
`--dry-run`: Preview the changes without applying them
`--extraActiveRecipes`: Comma-separated list of additional recipe names
to apply `--extraRecipeArtifactCoordinates`: Comma-separated list of
Maven coordinates for extra recipes (format: groupId:artifactId:version)
Use `--help` to see all available options.
:::
::::::

:::::::::::: sect3
#### Examples {#_examples}

::: paragraph
Update a Camel Quarkus application
:::

:::: listingblock
::: content
``` highlight
$ camel update run 4.8.3 --runtime=quarkus --dryRun
```
:::
::::

::: paragraph
Update a plain Camel application
:::

:::: listingblock
::: content
``` highlight
$ camel update run 4.9.0 --runtime=camel-main --repos=https://myMaven/repo --extraActiveRecipes=my.first.Recipe,my.second.Recipe --extraRecipeArtifactCoordinates=ex.my.org:recipes:1.0.0
```
:::
::::

::: paragraph
Update a Spring Boot application with and extra Spring Boot 3.3 upgrade
recipe
:::

:::: listingblock
::: content
``` highlight
$ camel update run 4.9.0 --runtime=spring-boot --extraActiveRecipes=org.openrewrite.java.spring.boot3.UpgradeSpringBoot_3_3 --extraRecipeArtifactCoordinates=org.openrewrite.recipe:rewrite-spring:6.0.2
```
:::
::::
::::::::::::
:::::::::::::::::::::
:::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
