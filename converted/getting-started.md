::: {#header}
# Getting Started
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
:::::::: {#preamble}
::::::: sectionbody
::: paragraph
You can get started with Apache Camel in a variety of ways, such as:
:::

::: ulist
- Using online Project generators

- Using the Camel CLI (command line)

- Using Camel Karavan (visual UI Camel designer)
:::

::: paragraph
And some more alternative methods:
:::

::: ulist
- Adding Camel to an existing project

- Using IDE tooling wizards

- Using Maven Archetypes

- Cloning an existing example to modify
:::
:::::::
::::::::

::::::: sect1
## Using online Project generators {#_using_online_project_generators}

:::::: sectionbody
::: paragraph
You can use [Camel Kameleon](https://kameleon.dev/#/standalone) which is
Camel's own online project generator. This generator is Camel focused
only, which is recommended for most Camel users.
:::

::: paragraph
You can also use [Spring Boot Initializer](https://start.spring.io/)
which is the Spring Boot generator that also has Camel support. However,
this generator does not allow users to have fine-grained control over
which components, data formats, kamelets etc. they can use.
:::

::: paragraph
And there is [Code with Quarkus](https://code.quarkus.io/), the Quarkus
generator, which has great support with Camel.
:::
::::::
:::::::

:::::::::::::::::::::::::::::: sect1
## Getting Started from command line (CLI) {#_getting_started_from_command_line_cli}

::::::::::::::::::::::::::::: sectionbody
::: paragraph
Camel uses [JBang](https://www.jbang.dev/) for the Camel CLI. You can
easily get up and running in a few steps.
:::

::: paragraph
**Step 1**
:::

::: paragraph
Open your favorite terminal and use JBang to install the Camel CLI. You
do not need to have Java installed first.
:::

::: paragraph
For Linux, macOS, and Windows (using WSL or bash compatible shell like
Cygwin or MinGW)
:::

:::: listingblock
::: content
``` highlight
curl -Ls https://sh.jbang.dev | bash -s - trust add https://github.com/apache/
curl -Ls https://sh.jbang.dev | bash -s - app install --fresh --force camel@apache/camel
```
:::
::::

::: paragraph
For Windows using Powershell
:::

:::: listingblock
::: content
``` highlight
iex "& { $(iwr https://ps.jbang.dev) } trust add https://github.com/apache/"
iex "& { $(iwr https://ps.jbang.dev) } app install --fresh --force camel@apache/camel"
```
:::
::::

::: paragraph
If it's your first time to install, you'll need to restart your shell.
:::

::: paragraph
**Step 2**
:::

::: paragraph
Create your first Camel integration
:::

:::: listingblock
::: content
``` highlight
camel init hello.java
```
:::
::::

::: paragraph
**Step 3**
:::

::: paragraph
Run the Camel integration
:::

:::: listingblock
::: content
``` highlight
camel run hello.java
```
:::
::::

::: paragraph
Bang the Camel integration is now running. You can use `ctrl` + `c` to
stop the integration.
:::

::: paragraph
**Step 4**
:::

::: paragraph
Camel makes it easy to change your code on the fly. You can run in live
coding mode, as shown:
:::

:::: listingblock
::: content
``` highlight
camel run hello.java --dev
```
:::
::::

::: paragraph
While in live coding mode, whenever you save changes to `hello.java`,
Camel will automatically load the updated version.
:::

::: paragraph
**Step 5**
:::

::: paragraph
Make sure to check out the [Camel JBang](camel-jbang.html)
documentation, for more details on the powers of the Camel CLI. You will
also find information how you can *export* what you have built with the
Camel CLI into a vanilla Camel Spring Boot or Camel Quarkus project.
:::
:::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::

::::: sect1
## Getting started with Camel using Karavan {#_getting_started_with_camel_using_karavan}

:::: sectionbody
::: paragraph
The [Camel Karavan](https://github.com/apache/camel-karavan) is a
toolkit for visually designing Camel integrations, it is fully
integrated with [Camel JBang](camel-jbang.html) which allows users to
easily try Camel while using the designer.
:::
::::
:::::

:::::::::::: sect1
## Alternative ways of getting started with Camel {#_alternative_ways_of_getting_started_with_camel}

::::::::::: sectionbody
:::: sect2
### Adding Camel to an existing project {#_adding_camel_to_an_existing_project}

::: paragraph
You can add Camel to any Java project, such as adding the necessary
Camel dependencies to the project build files (Maven or Gradle).
:::
::::

:::: sect2
### Using IDE tooling wizards {#_using_ide_tooling_wizards}

::: paragraph
Some IDEs have wizards for creating new projects, of which, some have
support for Apache Camel via Spring Boot Initializer or Code with
Quarkus.
:::
::::

:::: sect2
### Using Maven Archetypes {#_using_maven_archetypes}

::: paragraph
Apache Camel comes with a set of [Camel Maven
Archetypes](camel-maven-archetypes.html), you can use to create a new
Camel project.
:::
::::

:::: sect2
### Cloning an existing example to modify {#_cloning_an_existing_example_to_modify}

::: paragraph
There are tons of Camel examples hosted on Github that you can clone and
modify, such as [Camel Spring Boot
examples](https://github.com/apache/camel-spring-boot-examples).
:::
::::
:::::::::::
::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
