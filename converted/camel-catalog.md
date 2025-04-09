::: {#header}
# Camel Catalog
:::

:::::::::::::::::::::: {#content}
::::: {#preamble}
:::: sectionbody
::: paragraph
Each release includes a catalog with all sorts of information about
what's included in the release.
:::
::::
:::::

:::::::::::::::::: sect1
## About Camel Catalog {#_about_camel_catalog}

::::::::::::::::: sectionbody
::: paragraph
The catalog is shipped in an independent standalone `camel-catalog` JAR
containing the following information:
:::

::: ulist
- List of all components, data formats, languages, EIPs, and everything
  else in the release

- Curated lists for Camel Quarkus, Camel Spring Boot, and Camel Main
  runtimes

- JSON schema with extensive details for every option

- Human-readable documentation for every option

- Categorization of options (for example, find all database components)

- XML schema for the XML DSLs
:::

::: paragraph
There is also a Java API for tooling:
:::

::: ulist
- validating Camel endpoints and the Simple language

- creating Camel endpoint URLs
:::

::: paragraph
The catalog provides a wealth of information that tooling can tap into
and use.
:::

:::::: sect2
### Tooling using Camel Catalog {#_tooling_using_camel_catalog}

::: paragraph
The following tools uses the catalog in their editor:
:::

::: ulist
- [Camel tooling for
  IDEA](https://plugins.jetbrains.com/plugin/9371-apache-camel-idea-plugin)

- [Camel tooling for
  Eclipse](https://marketplace.eclipse.org/content/language-support-apache-camel)

- [Camel tooling for VS
  Code](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-apache-camel)

- [Camel Karavan for VS
  Code](https://marketplace.visualstudio.com/items?itemName=camel-karavan.karavan)

- [Kaoto for VS
  Code](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-kaoto)

- [Hawtio](https://hawt.io/)
:::

::: paragraph
The [Camel Maven validation](camel-report-maven-plugin.html) plugin uses
the catalog during validation of all the Camel endpoints found while
scanning the source code.
:::
::::::

::::::: sect2
### Layout of camel-catalog {#_layout_of_camel_catalog}

::: paragraph
JAR includes the information using the following directory layout:
:::

:::: listingblock
::: content
``` highlight
org
└── apache
  └── camel
    └── catalog
      ├── beans (JSON schema)
      ├── components (JSON schema)
      ├── dataformats (JSON schema)
      ├── dev-consoles (JSON schema)
      ├── languages (JSON schema)
      ├── main (JSON schema)
      ├── models (JSON schema)
      ├── others (JSON schema)
      ├── releases (JSON schema)
      ├── schemas (XML schema)
      └── transformers (JSON schema)
```
:::
::::

::: paragraph
Each directory contains files with the information. Every Camel
component is included as JSON schema files in the components directory.
For example, the Timer component is included in the file timer.json.
:::
:::::::
:::::::::::::::::
::::::::::::::::::
::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
