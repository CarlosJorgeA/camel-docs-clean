::: {#header}
# Data Format
:::

:::::::::::::::::::: {#content}
:::::::::: {#preamble}
::::::::: sectionbody
::: paragraph
Camel supports a pluggable `DataFormat` to allow messages to be
marshalled to and from binary or text formats to support a kind of
[Message Translator](components:eips:message-translator.html).
:::

:::: imageblock
::: content
![image](images/MessageTranslator.gif)
:::
::::

::: paragraph
Camel has support for message transformation using several techniques.
One such technique is data formats, where marshal and unmarshal comes
from.
:::

::: paragraph
So in other words, the [Marshal](components:eips:marshal-eip.html) and
[Unmarshal](components:eips:unmarshal-eip.html) EIPs are used data
formats.
:::

::: ulist
- *Marshal* - Transforms the message body (such as Java object) into a
  binary or textual format, ready to be wired over the network.

- *Unmarshal* - Transforms data in some binary or textual format (such
  as received over the network) into a Java object; or some other
  representation according to the data format being used.
:::
:::::::::
::::::::::

::::: sect1
## Supported data formats {#_supported_data_formats}

:::: sectionbody
::: paragraph
There are more than 40 different data formats that support formats such
as XML, CSV, JSON, YAML, Avro, Protobuf, and many more.
:::
::::
:::::

::::: sect1
## Example {#_example}

:::: sectionbody
::: paragraph
See [Marshal](components:eips:marshal-eip.html) for more information and
examples.
:::
::::
:::::

::::: sect1
## See Also {#_see_also}

:::: sectionbody
::: ulist
- [Data Format DSL](manual::dataformat-dsl.html)
:::
::::
:::::
::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
