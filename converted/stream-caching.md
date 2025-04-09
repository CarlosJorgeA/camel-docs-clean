::: {#header}
# Stream caching
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
::::::::: {#preamble}
:::::::: sectionbody
::: paragraph
While stream types (like `StreamSource`, `InputStream` and `Reader`) are
commonly used in messaging for performance reasons, they also have an
important drawback: they can only be read once. In order to be able to
work with message content multiple times, the stream needs to be cached.
:::

::: paragraph
Streams are cached in memory. However, for large stream messages, you
can set `spoolEnabled=true` and then large message (over 128 KB) will be
cached in a temporary file instead. Camel itself will handle deleting
the temporary file once the cached stream is no longer necessary.
:::

::: {.admonitionblock .important}
+-----------------------------------+-----------------------------------+
| ::: title                         | ::: paragraph                     |
| Important                         | **StreamCache - Affecting the     |
| :::                               | message payload**                 |
|                                   | :::                               |
|                                   |                                   |
|                                   | ::: paragraph                     |
|                                   | The `StreamCache` will affect     |
|                                   | your payload object as it will    |
|                                   | replace the `Stream` payload with |
|                                   | a `org.apache.camel.StreamCache`  |
|                                   | object. This `StreamCache` is     |
|                                   | capable of being re-readable and  |
|                                   | thus possible to better be routed |
|                                   | within Camel using redelivery or  |
|                                   | [Content Based                    |
|                                   | Router]                           |
|                                   | (components:eips:choice-eip.html) |
|                                   | or the likes.                     |
|                                   | :::                               |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
In order to determine if a message payload requires caching, then Camel
uses the [Type Converter](type-converter.html) functionality, to
determine if the message payload type can be converted into an
`org.apache.camel.StreamCache` instance.
:::

::: {.admonitionblock .important}
+-----------------------------------+-----------------------------------+
| ::: title                         | ::: paragraph                     |
| Important                         | All the classes from the Camel    |
| :::                               | release that implements           |
|                                   | `org.apache.camel.StreamCache` is |
|                                   | NOT intended for end users to     |
|                                   | create as instances, but they are |
|                                   | part of Camels stream-caching     |
|                                   | functionality.                    |
|                                   | :::                               |
+-----------------------------------+-----------------------------------+
:::
::::::::
:::::::::

:::::::::::::::: sect1
## Configuring Stream Caching {#_configuring_stream_caching}

::::::::::::::: sectionbody
::: paragraph
Stream caching is configured using
`org.apache.camel.spi.StreamCachingStrategy`.
:::

::: paragraph
The strategy has the following options:
:::

+-------------+-------------+------------------------------------------+
| Option      | Default     | Description                              |
+=============+=============+==========================================+
| enabled     | true        | Whether stream caching is enabled        |
+-------------+-------------+------------------------------------------+
| a           |             | To filter stream caching of a given set  |
| llowClasses |             | of allowed/denied classes. By default,   |
|             |             | all classes that are                     |
|             |             | `java.io.InputStream` is allowed.        |
|             |             | Multiple class names can be separated by |
|             |             | comma.                                   |
+-------------+-------------+------------------------------------------+
| denyClasses |             | To filter stream caching of a given set  |
|             |             | of allowed/denied classes. By default,   |
|             |             | all classes that are                     |
|             |             | `java.io.InputStream` is allowed.        |
|             |             | Multiple class names can be separated by |
|             |             | comma.                                   |
+-------------+-------------+------------------------------------------+
| s           | false       | Whether spool to disk is enabled         |
| poolEnabled |             |                                          |
+-------------+-------------+------------------------------------------+
| spo         | \${java.    | Base directory where temporary files for |
| olDirectory | io.tmpdir}/ | spooled streams should be stored. This   |
|             | camel/camel | option supports naming patterns as       |
|             | -tmp-#uuid# | documented below.                        |
+-------------+-------------+------------------------------------------+
| spoolCipher | null        | If set, the temporary files are          |
|             |             | encrypted using the specified cipher     |
|             |             | transformation (i.e., a valid stream or  |
|             |             | 8-bit cipher name such as \"RC4\",       |
|             |             | \"AES/CTR/NoPadding\". An empty name     |
|             |             | \"\" is treated as null).                |
+-------------+-------------+------------------------------------------+
| spo         | 128 KB      | Size in bytes when the stream should be  |
| olThreshold |             | spooled to disk instead of keeping in    |
|             |             | memory. Use a value of 0 or negative to  |
|             |             | disable it all together so streams is    |
|             |             | always kept in memory regardless of      |
|             |             | their size.                              |
+-------------+-------------+------------------------------------------+
| spoolU      | 0           | A percentage (1 to 99) of current used   |
| sedHeapMemo |             | heap memory to use as threshold for      |
| ryThreshold |             | spooling streams to disk. The upper      |
|             |             | bounds is based on heap committed        |
|             |             | (guaranteed memory the JVM can claim).   |
|             |             | This can be used to spool to disk when   |
|             |             | running low on memory.                   |
+-------------+-------------+------------------------------------------+
| sp          | Max         | If `spoolUsedHeapMemoryThreshold` is in  |
| oolUsedHeap |             | use, then whether the used heap memory   |
| MemoryLimit |             | upper limit is either Max or Committed.  |
+-------------+-------------+------------------------------------------+
| an          | false       | Whether any or all `SpoolRule`s must     |
| ySpoolRules |             | return `true` to determine if the stream |
|             |             | should be spooled or not. This can be    |
|             |             | used as applying AND/OR binary logic to  |
|             |             | all the rules. By default it's AND       |
|             |             | based.                                   |
+-------------+-------------+------------------------------------------+
| bufferSize  | 4096        | Sets the buffer size to use when         |
|             |             | allocating in-memory buffers used for    |
|             |             | in-memory stream caches.                 |
+-------------+-------------+------------------------------------------+
| removeSpoo  | true        | Whether to remove the spool directory    |
| lDirectoryW |             | when stopping                            |
| henStopping |             | [CamelContext](camelcontext.html).       |
+-------------+-------------+------------------------------------------+
| statis      | false       | Whether utilization statistics is        |
| ticsEnabled |             | enabled. By enabling this you can see    |
|             |             | these statics for example with JMX.      |
+-------------+-------------+------------------------------------------+

:::::::::::: sect2
### SpoolDirectory naming pattern {#_spooldirectory_naming_pattern}

::: paragraph
The following patterns is supported:
:::

::: ulist
- `#uuid#` = a random UUID

- `#camelId#` = the CamelContext id (e.g. the name)

- `#name#` = same as `#camelId#`

- `#counter#` = an incrementing counter

- `#bundleId#` = the OSGi bundle id (only for OSGi environments)

- `#symbolicName#` = the OSGi symbolic name (only for OSGi environments)

- `#version#` = the OSGi bundle version (only for OSGi environments)

- `${env:key}` = the environment variable with the key

- `${key}` = the JVM system property with the key
:::

::: paragraph
A couple of examples:
:::

::: paragraph
To store in the java temp directory with a sub directory using the
`CamelContext` name:
:::

:::: listingblock
::: content
``` highlight
context.getStreamCachingStrategy().setSpoolDirectory"${java.io.tmpdir}#name#/");
```
:::
::::

::: paragraph
To store in `KARAF_HOME/tmp/bundleId` directory:
:::

:::: listingblock
::: content
``` highlight
context.getStreamCachingStrategy().setSpoolDirectory"${env:KARAF_HOME}/tmp/bundle#bundleId#");
```
:::
::::
::::::::::::
:::::::::::::::
::::::::::::::::

::::::::::::: sect1
## Configuring StreamCachingStrategy in Java {#_configuring_streamcachingstrategy_in_java}

:::::::::::: sectionbody
::: paragraph
You can configure the `StreamCachingStrategy` in Java as shown below:
:::

:::: listingblock
::: content
``` highlight
context.getStreamCachingStrategy().setSpoolEnabled(true);
context.getStreamCachingStrategy().setSpoolDirectory("/tmp/cachedir");
context.getStreamCachingStrategy().setSpoolThreshold(64 * 1024);
context.getStreamCachingStrategy().setBufferSize(16 * 1024);
// to enable encryption using RC4
// context.getStreamCachingStrategy().setSpoolCipher("RC4");
```
:::
::::

::: paragraph
And remember to enable Stream caching on the `CamelContext`:
:::

:::: listingblock
::: content
``` highlight
context.setStreamCaching(true);
```
:::
::::

::: paragraph
or on routes:
:::

:::: listingblock
::: content
``` highlight
from("file:inbox")
  .streamCaching()
  .to("bean:foo");
```
:::
::::
::::::::::::
:::::::::::::

::::::::::::::::::::::: sect1
## Configuring StreamCachingStrategy in XML {#_configuring_streamcachingstrategy_in_xml}

:::::::::::::::::::::: sectionbody
::: paragraph
In XML you can enable stream caching on the `<camelContext>` and then do
the configuration in the `streamCaching` element:
:::

:::: listingblock
::: content
``` highlight
<camelContext streamCache="true">

  <streamCaching id="myCacheConfig" bufferSize="16384" spoolEnabled="true" spoolDirectory="/tmp/cachedir" spoolThreshold="65536"/>

  <route>
    <from uri="direct:c"/>
    <to uri="mock:c"/>
  </route>

</camelContext>
```
:::
::::

:::::::::::::::::: sect2
### Using spoolUsedHeapMemoryThreshold {#_using_spoolusedheapmemorythreshold}

::: paragraph
By default, stream caching will spool only big payloads (128 KB or
bigger) to disk. However you can also set the
`spoolUsedHeapMemoryThreshold` option which is a percentage of used heap
memory. This can be used to also spool to disk when running low on
memory.
:::

::: paragraph
For example with:
:::

:::: listingblock
::: content
``` highlight
<streamCaching id="myCacheConfig" spoolEnabled="true" spoolDirectory="/tmp/cachedir" spoolUsedHeapMemoryThreshold="70"/>
```
:::
::::

::: paragraph
Then notice that as `spoolThreshold` is default enabled with 128 KB,
then we have both thresholds in use (`spoolThreshold` and
`spoolUsedHeapMemoryThreshold`). And in this example then we only spool
to disk if payload is \> 128 KB and that used heap memory is \> 70%. The
reason is that we have the option `anySpoolRules` as default `false`.
That means both rules must be `true` (e.g. AND).
:::

::: paragraph
If we want to spool to disk if either of the rules (e.g. OR), then we
can do:
:::

:::: listingblock
::: content
``` highlight
<streamCaching id="myCacheConfig" spoolEnabled="true" spoolDirectory="/tmp/cachedir" spoolUsedHeapMemoryThreshold="70" anySpoolRules="true"/>
```
:::
::::

::: paragraph
If we only want to spool to disk if we run low on memory then we can
set:
:::

:::: listingblock
::: content
``` highlight
<streamCaching id="myCacheConfig" spoolEnabled="true" spoolDirectory="/tmp/cachedir" spoolThreshold="-1" spoolUsedHeapMemoryThreshold="70"/>
```
:::
::::

::: paragraph
then we do not use the `spoolThreshold` rule, and only the heap memory
based is in use.
:::

::: paragraph
By default, the upper limit of the used heap memory is based on the
maximum heap size. Though you can also configure to use the committed
heap size as the upper limit, this is done using the
`spoolUsedHeapMemoryLimit` option as shown below:
:::

:::: listingblock
::: content
``` highlight
<streamCaching id="myCacheConfig" spoolEnabled="true" spoolDirectory="/tmp/cachedir" spoolUsedHeapMemoryThreshold="70" spoolUsedHeapMemoryLimit="Committed"/>
```
:::
::::
::::::::::::::::::
::::::::::::::::::::::
:::::::::::::::::::::::

:::::::::::::::: sect1
## Using custom SpoolRule implementations {#_using_custom_spoolrule_implementations}

::::::::::::::: sectionbody
::: paragraph
You can implement your custom rules to determine if the stream should be
spooled to disk. This can be done by implementing the interface
`org.apache.camel.spi.StreamCachingStrategy.SpoolRule` which has a
single method:
:::

:::: listingblock
::: content
``` highlight
boolean shouldSpoolCache(long length);
```
:::
::::

::: paragraph
The `length` is the length of the stream. To use the rule then add it to
the `StreamCachingStrategy` as shown below:
:::

:::: listingblock
::: content
``` highlight
SpoolRule mySpoolRule = ...
context.getStreamCachingStrategy().addSpoolRule(mySpoolRule);
```
:::
::::

::: paragraph
And from XML you need to define a `<bean>` with your custom rule:
:::

:::: listingblock
::: content
``` highlight
<bean id="mySpoolRule" class="com.foo.MySpoolRule"/>

<streamCaching id="myCacheConfig" spoolEnabled="true" spoolDirectory="/tmp/cachedir" spoolRules="mySpoolRule"/>
```
:::
::::

::: paragraph
Using the spoolRules attribute on `<streamCaching>`. if you have more
rules, then separate them by comma.
:::

:::: listingblock
::: content
``` highlight
<streamCaching id="myCacheConfig" spoolEnabled="true" spoolDirectory="/tmp/cachedir" spoolRules="mySpoolRule,myOtherSpoolRule"/>
```
:::
::::
:::::::::::::::
::::::::::::::::

::::::: sect1
## Using StreamCachingProcessor {#_using_streamcachingprocessor}

:::::: sectionbody
::: paragraph
Since Camel 4.11 this processor can be used to convert the current
message body to a `StreamCache`. This allows the body to be re-read
multiple times and can be placed at any point in a Camel route.
:::

:::: listingblock
::: content
``` highlight
from("direct:start")
    .process(new StreamCachingProcessor())
    .to("log:cached");
```
:::
::::
::::::
:::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
