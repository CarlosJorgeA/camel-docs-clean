::: {#header}
# Improving the documentation
:::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
:::::: {#preamble}
::::: sectionbody
::: paragraph
The Apache Camel project loves your help with improving the
documentation, whether its a tiny typo fix, or adding more details to an
existing component, etc.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | This page only describes working  |
| Note                              | with the \'documentation\'        |
| :::                               | portion of the website. Other     |
|                                   | portions are written in markdown  |
|                                   | and built using Hugo.             |
+-----------------------------------+-----------------------------------+
:::
:::::
::::::

::::: sect1
## Simple changes {#_simple_changes}

:::: sectionbody
::: paragraph
If there's an `edit this page` button at the top right of the page and
you wish to propose a simple change such as fixing a typo or rewording
something, use this [very simple
process](faq:how-do-i-edit-the-website.html). For more complicated
changes, including changing xrefs, adding, removing or renaming pages,
and significant organizational changes, please use the process described
on this page.
:::
::::
:::::

::::::: sect1
## Where to find the documentation {#_where_to_find_the_documentation}

:::::: sectionbody
::: paragraph
All of the documentation accessible in the left-hand navigation panel in
the documentation portion of the website is managed in the [AsciiDoc
format](https://asciidoc.org/) and built with the
[Antora](https://antora.org) static site generator. As of November 2021,
by far the most capable Asciidoc editor is the Intellij Asciidoc plugin,
which works with all Intellij editor products including the free IDEA
Community Edition. The plugin preview is more capable than viewing a
local Asciidoc file with a browser plugin as it has some understanding
of Antora structure. Note that the only reliable way to preview your
changes is with a full build of the Antora portion of the website.
:::

::: paragraph
The files have the extension `.adoc` and are managed in the Camel
repositories. General documentation is usually directly editable.
Component specific documentation is partially or entirely generated from
other metadata sources which are in turn generated from the code, often
from javadoc. Altering generated documentation requires finding the
original source, which varies by project. Editable pages are found in
several different places in the repositories:
:::

::: dlist

Main camel repository

:   ::: dlist

    Camel components

    :   In the `src/main/docs` folder for the component or camel module.
        These are symlinked to under `docs/components`.

    EIPs

    :   In the `core/camel-core-engine/src/main/docs` folder.

    Core languages

    :   In the `core/camel-core-languages/src/main/docs` folder. Note
        that many languages are under `components`.

    User manual and FAQS

    :   In the `/docs/user-manual` folder.
    :::

Camel Karaf

:   In the `docs` folder.

Camel Spring Boot

:   Most documentation is generated and appended to the component
    documentation it applies to. Editable pages are under
    `docs/spring-boot` and `core/camel-spring-boot/src/main/docs` and
    `core/camel-spring-boot-xml/src/main/docs`.

Other subprojects

:   ::: dlist

    camel-k, camel-k-runtime

    :   Under `docs`. There is no generated `camel-k` documentation.

    camel-kafka-connector

    :   Editable pages are under `docs`. Most documentation is generated
        directly from the generated json files for each connector under
        `connectors/<connector-name>/src/generated/resources`.

    camel-kamelets

    :   Only `docs/modules/ROOT/pages/index.adoc` is editable. All other
        documentation is generated from the kamelet yaml descriptors.

    camel-quarkus

    :   Editable pages are under `docs`. Pages under
        `docs/modules/ROOT/pages/reference/components` and
        `docs/modules/ROOT/pages/reference/extensions` are generated,
        including optional snippets from e.g.
        `extensions/activemq/src/main/doc`.

    camel-quarkus-examples

    :   Editable pages are under `docs`.
    :::
:::
::::::
:::::::

::::::::::::::::::::::::::::::: sect1
## How to build the website locally, with your changes {#_local_build_instructions}

:::::::::::::::::::::::::::::: sectionbody
::: paragraph
First, make sure you have yarn, version \>= 3.1.0, installed globally.
:::

::: paragraph
All three builds rely on a \'site-manifest\' that lists the contents of
the site with enough detail so that Antora knows about all the pages.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | The following procedure is not    |
| Note                              | available in all subprojects yet. |
| :::                               | If there is no                    |
|                                   | `docs/local-build.sh` in the one  |
|                                   | you are working on, please ping   |
|                                   | djencks on zulip and I'll try to  |
|                                   | get it in soon.                   |
+-----------------------------------+-----------------------------------+
:::

:::::: sect2
### Directory layout and initial setup {#_directory_layout_and_initial_setup}

::: paragraph
You need a single directory, such as `camel`, that contains all the
camel subprojects you are working with, and the `camel-website` project.
:::

:::: listingblock
::: content
``` highlight
cd camel
git clone https://github.com/apache/camel-website.git
```
:::
::::
::::::

:::::::: sect2
### Quick partial build {#_quick_partial_build}

::: paragraph
In your project, run
:::

:::: listingblock
::: content
``` highlight
./local-build.sh quick
```
:::
::::

::: paragraph
This will build your local subproject substituted into the main Camel
website build. It will check syntax in your local subproject, and xrefs
within it and from it to the rest of the site. It will not check xrefs
into your subproject.
:::

::: paragraph
Under `camel-website/documentation` you will find only the pages for
your subproject. Looking at this in a browser, links within the
subproject will stay in the local pages, and links to the rest of the
site will go to the main camel site. There is no obvious way to get back
to your local pages.
:::
::::::::

:::::::: sect2
### Full local Antora build {#_full_local_antora_build}

::: paragraph
You only have to do this once, and again whenever there are significant
changes to the website.
:::

::: paragraph
In your project, run
:::

:::: listingblock
::: content
``` highlight
./local-build.sh full
```
:::
::::

::: paragraph
This will build the entire site, including your changes in your branch,
and generate a \'site-manifest\' listing all the contents of the
website, that can be used to build only small parts of the site. This
full build will also check that all xrefs into your subproject branch
are valid.
:::
::::::::

:::::::: sect2
### Subsequent partial continuous builds {#_subsequent_partial_continuous_builds}

::: paragraph
After this full build completes, you can work on documentation with live
updates in your browser by running
:::

:::: listingblock
::: content
``` highlight
./local-build.sh
```
:::
::::

::: paragraph
This will do an initial build of just the current branch in the current
subproject, incorporating it into the full site built in the full build,
start a web server to serve the site, set up browser-sync on pages you
are looking at in your browser, and rebuild the (partial) site as it
detects changes. Depending on the amount of content generation Antora is
doing, this may take up to a minute or so.
:::

::: paragraph
This partial build will detect broken xrefs within your branch and from
your branch to the main site, but will not (yet) detect broken xrefs
from the rest of the site into your branch. If you rename or remove a
page please do a full build or check the PR build carefully for broken
xrefs.
:::
::::::::

:::: sect2
### Viewing a full build in httpd {#_viewing_a_full_build_in_httpd}

::: paragraph
If you do a full build (`yarn build-all` or `yarn build` rather than
`yarn build:antora` or use of the `local-build.sh` script in a
subproject) and have Docker available locally you can view your build
served with httpd by running `local-httpd-in-docker.sh`. This is
especially valuable to check redirects set up with `page-aliases`.
:::
::::
::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::

:::::: sect1
## Creating a documentation pull request. {#_creating_a_documentation_pull_request}

::::: sectionbody
::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | Simple changes such as typo fixes |
| Note                              | or rewording can usually be done  |
| :::                               | directly at GitHub after pressing |
|                                   | the `edit this page` button at    |
|                                   | the top left of each page. Note   |
|                                   | that if the page source starts    |
|                                   | with a comment that the page is   |
|                                   | copied or generated this will not |
|                                   | work! Please do not use this      |
|                                   | method if you are changing any    |
|                                   | xrefs or making significant       |
|                                   | changes to format; instead follow |
|                                   | the procedure below.              |
+-----------------------------------+-----------------------------------+
:::

::: {.olist .arabic}
1.  Fork/clone the appropriate repository from GitHub and switch to the
    branch you are working with.

2.  Create a branch for your work with a name starting with the original
    branch name, e.g. `git switch -c main-doc-fix`

3.  Edit the `.adoc` sources as needed. Preview your work in the
    Intellij Asciidoc plugin preview or in a browser with an Asciidoctor
    extension installed.

4.  Do a [local website build with your
    changes](#_local_build_instructions).

5.  Commit and push your work and create a PR in the (sub)project
    repository.

6.  Fork/clone the camel-website repository, and create an appropriate
    branch, e.g. `git switch -c camel-quarkus-main-456`. The following
    process will work for any number of doc PRs against any number of
    source repositories: usually you will have one subproject repo and
    one branch.

7.  Locate the `- url` of the project(s) you are working with in the
    `antora-playbook.yml` under `sources`, and locate the branch(es) you
    have altered under that `- url`.

8.  Add something like this to the end of the `antora-playbook.yml`:

    ::::::: openblock
    :::::: content
    :::: listingblock
    ::: content
    ``` highlight
        - require: '@djencks/antora-source-map'
    #      log_level: trace #(1)
          source-map: (2)
            - url: 'https://github.com/apache/camel-kamelets.git' # (3)
              mapped-url: 'https://github.com/djencks/camel-kamelets.git' # (4)
              branches: # (5)
                - branch: main # (6)
                  mapped-branch: main-collect # (7)
                - branch: 0.6.x
                  mapped-branch: 0.6.x-collect
                - branch: 0.5.x
                  mapped-branch: 0.5.x-collect
    ```
    :::
    ::::

    ::: {.colist .arabic}
    1.  Turning on trace logging will show you in great detail what's
        changed from the regular playbook, which can be useful if the
        build is not doing what you expect.

    2.  List of source urls to substitute, probably only one.

    3.  The GitHub URL of the subproject you are working on.

    4.  The GitHub URL of your fork of the subproject.

    5.  List of modified branches: probably only one.

    6.  Name of the branch your PR will merge into.

    7.  Name of your PR branch.
    :::
    ::::::
    :::::::

9.  At this point you can test your playbook changes locally by running
    `yarn build:antora` or `yarn build`.

10. Commit the playbook changes, push to your fork of the
    `camel-website` repository, and open a PR.

11. If all goes well you will get an email telling you where the Netlify
    preview is; this is also shown on the PR page.

12. Check for build problems and examine the preview.

13. Upon approval, your content PR will be merged. A `camel-website` PR
    constructed as described here will not need to be merged and may be
    closed.
:::
:::::
::::::

::::: sect1
## New, renamed, or removed pages {#_new_renamed_or_removed_pages}

:::: sectionbody
::: ulist
- Add, rename, or remove the xref for your page in the appropriate
  nav.adoc file.

- Build the entire website and check for broken xrefs: these will appear
  as errors in the Antora log output.
:::
::::
:::::

:::::: sect1
## Changed xrefs {#_changed_xrefs}

::::: sectionbody
::: paragraph
First, read [A guide to xrefs](#_a_guide_to_xrefs)
:::

::: ulist
- Build the entire website and check for broken xrefs.
:::
:::::
::::::

::::: sect1
## Adding a new component version {#_adding_a_new_component_version}

:::: sectionbody
::: paragraph
See [release-guide-website.html](release-guide-website.html).
:::
::::
:::::

:::::::::::::::::::::::::::::: sect1
## A guide to xrefs {#_a_guide_to_xrefs}

::::::::::::::::::::::::::::: sectionbody
::: paragraph
For a general explanation of Antora xref syntax see [the Antora
documentation](https://docs.antora.org/antora/3.0/page/xref/). Due to
the logical structure of the Camel documentation, xrefs will have a very
limited choice of structure.
:::

::: {.admonitionblock .important}
+-----------------------------------+-----------------------------------+
| ::: title                         | A bit of confusion is possible    |
| Important                         | here between Antora components    |
| :::                               | and Camel components. Generally   |
|                                   | an Antora component corresponds   |
|                                   | more or less to a Camel           |
|                                   | subproject, and never to a camel  |
|                                   | commponent. All the camel         |
|                                   | components are documented in an   |
|                                   | Antora component named            |
|                                   | `components`. In this section the |
|                                   | word `component` means an Antora  |
|                                   | component.                        |
+-----------------------------------+-----------------------------------+
:::

::: {.admonitionblock .important}
+-----------------------------------+-----------------------------------+
| ::: title                         | Antora components may be          |
| Important                         | `distributed` which means that    |
| :::                               | the content comes from more than  |
|                                   | one place, possibly from          |
|                                   | different repositories. For       |
|                                   | instance, the `components`        |
|                                   | component has content from the    |
|                                   | main camel repository under the   |
|                                   | start_paths `docs/components` and |
|                                   | `core                             |
|                                   | /camel-core-engine/src/main/docs` |
|                                   | and from the `camel-spring-boot`  |
|                                   | repository under                  |
|                                   | `components-starter` and          |
|                                   | `docs/components`. Furthermore    |
|                                   | the content may not appear in the |
|                                   | normal Antora structure but may   |
|                                   | be collected from a more          |
|                                   | maven-project-friendly            |
|                                   | arrangement with an Antora        |
|                                   | extension.                        |
+-----------------------------------+-----------------------------------+
:::

:::::::::::: sect2
### xrefs within an (Antora) component {#_xrefs_within_an_antora_component}

::: paragraph
Generally there will never be a reason to refer from one version of a
component to another version. To assure this happens without maintenance
issues, leave out the version and component segments from the xref, e.g.
in the `components` component
:::

:::: listingblock
::: content
``` highlight
xref:eips:enterprise-integration-patterns.adoc[]
```
:::
::::

::: paragraph
NOT
:::

:::: listingblock
::: content
``` highlight
xref:next@components:eips:enterprise-integration-patterns.adoc[]
```
:::
::::

::: paragraph
Do this no matter how many locations the component is distributed over.
:::

::: paragraph
An xref within the same module can leave out the module segment,
although it does no harm.
:::

::: paragraph
Do not specify the component name: if you do, the link will be to the
`latest` (non-prerelease, i.e., non-`next`) version, not the current
version.
:::
::::::::::::

:::::: sect2
### Links to the user manual {#_links_to_the_user_manual}

::: paragraph
The user-manual component is
[unversioned](https://docs.antora.org/antora/3.0/component-with-no-version/).
Leave out the version segment. For example, this will link to this page
from anywhere in the documentation:
:::

:::: listingblock
::: content
``` highlight
xref:manual::improving-the-documentation.adoc[]
```
:::
::::
::::::

::::::::::: sect2
### Links between subprojects {#_links_between_subprojects}

::: paragraph
Each camel subproject relates to other subprojects, and each version of
a subproject relates to specific versions of these other subprojects.
These subproject versions are specified in the `antora.yml` component
descriptor for the documentation component for that subproject. Note
that for distributed components each start path has a component
descriptor but only one has the additional `asciidoc/attributes` key.
For example,
:::

:::: listingblock
::: content
``` highlight
name: camel-kafka-connector
title: Camel Kafka Connector
version: next
prerelease: true
display-version: Next (Pre-release)

nav:
- modules/ROOT/nav.adoc

asciidoc:
  attributes:
    camel-version: 3.12.x
    camel-k-runtime-version: 1.8.0
    camel-k-version:
    camel-kamelets-version: 0.3.0
```
:::
::::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | Setting these up is WIP           |
| Note                              |                                   |
| :::                               |                                   |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
Use these attributes to refer to documentation for the related
subproject, e.g.
:::

:::: listingblock
::: content
``` highlight
xref{blank}:{camel-version}@components:eips:enterprise-integration-patterns.adoc[]
```
:::
::::

::: paragraph
If there's a missing attribute, please raise an issue rather than using
a concrete version.
:::
:::::::::::
:::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
