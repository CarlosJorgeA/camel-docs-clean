::: {#header}
# Apache Camel 3.x Upgrade Guide
:::

:::::::::::::: {#content}
::::: {#preamble}
:::: sectionbody
::: paragraph
This document is for helping you upgrade your Apache Camel application
from Camel 3.x to 3.y. For example, if you are upgrading Camel 3.0 to
3.2, then you should follow the guides from both 3.0 to 3.1 and 3.1 to
3.2.
:::
::::
:::::

::::: sect1
## Upgrading Camel 3.21 to 3.22 {#_upgrading_camel_3_21_to_3_22}

:::: sectionbody
::: paragraph
No changes expected.
:::
::::
:::::

::::::: sect1
## Upgrading Camel 3.22.x to 3.22.3 {#_upgrading_camel_3_22_x_to_3_22_3}

:::::: sectionbody
::::: sect2
### camel-file {#_camel_file}

::: paragraph
The `readLock=changed` with using `readLockMinAge` has been restored to
same behaviour as previously in 3.x.
:::

::: paragraph
For example, using `readLockMinAge=5s` would pickup files that are older
than 5 seconds from startup time. If you have many existing files on
startup that are old, then Camel will now again be fast, and pick up
these files immediately.
:::
:::::
::::::
:::::::
::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
