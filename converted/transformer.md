::: {#header}
# Transformer
:::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
:::::: {#preamble}
::::: sectionbody
::: paragraph
Transformer (`org.apache.camel.spi.Transformer`) performs declarative
transformation of the message according to the declared *Input Type*
and/or *Output Type* on a route definition which declares the expected
message type. The default Camel Message implements `DataTypeAware`,
which allows to hold the message type represented by `DataType`.
:::

::: paragraph
If the input type and/or output type is declared by *Input Type* and/or
*Output Type* in the route definition, and in case it is different from
actual message type at runtime, Camel internal processor looks for a
`Transformer` which transforms from the current message type to the
expected message type and apply. Once transform succeed or message is
already in expected type, then the message data type is updated.
:::
:::::
::::::

::::::: sect1
## Data type format {#_data_type_format}

:::::: sectionbody
:::: listingblock
::: content
``` highlight
scheme:name
```
:::
::::

::: paragraph
where **scheme** is the type of data model like `java`, `xml` or `json`,
and **name** is the individual data type name. The scheme could also
represent a Camel component scheme such as `http` or `aws2-s3` in order
to reference component specific data types. When using the `java` scheme
the data type name may be the qualified class name (e.g.
`java:org.apache.camel.Foo`) If you only specify **scheme** then it hits
all the data types which has that scheme like a wildcard.
:::
::::::
:::::::

:::::::::::::::::::: sect1
## Supported Transformers {#_supported_transformers}

::::::::::::::::::: sectionbody
+-----------------+-----------------------------------------------------+
| Transformer     | Description                                         |
+=================+=====================================================+
| Data Format     | Transform with using Data Format                    |
| Transformer     |                                                     |
+-----------------+-----------------------------------------------------+
| Endpoint        | Transform with using Endpoint                       |
| Transformer     |                                                     |
+-----------------+-----------------------------------------------------+
| Custom          | Transform with using custom transformer class.      |
| Transformer     | Transformer must be a subclass of                   |
|                 | `org.apache.camel.spi.Transformer`                  |
+-----------------+-----------------------------------------------------+
| Loading         | Loads multiple transformer implementations (e.g.    |
| Transformer     | via annotation classpath scan). Also preloads known |
|                 | default Camel transformer implementations.          |
+-----------------+-----------------------------------------------------+

::::::::::::: sect2
### Common Options {#_common_options}

::: paragraph
All transformers have following common options to specify which data
type is supported by the transformer. `name` or both of `fromType` and
`toType` must be specified.
:::

+-----------------+-----------------------------------------------------+
| Name            | Description                                         |
+=================+=====================================================+
| scheme          | The supported data type scheme. It is possible to   |
|                 | just reference a scheme like `xml` or `json`. For   |
|                 | example if `xml` is specified, the transformer is   |
|                 | applied for all java -\> xml and xml -\> java       |
|                 | transformation.                                     |
+-----------------+-----------------------------------------------------+
| name            | The name of the transformer. If name is specified   |
|                 | users may use a combination of a scheme and name    |
|                 | (e.g. `xml:Order`) to reference the transformer in  |
|                 | a route.                                            |
+-----------------+-----------------------------------------------------+
| fromType        | [Data type](#) to transform from.                   |
+-----------------+-----------------------------------------------------+
| toType          | [Data type](#) to transform to.                     |
+-----------------+-----------------------------------------------------+

::: paragraph
Transformer implementations may use `scheme:name` or the combination of
`fromType/toType` as an identifier.
:::

::: paragraph
When using the `scheme:name` identifier users may reference the
transformer by its full name in a route.
:::

::::: exampleblock
:::: content
::: dlist

Java

:   :::: listingblock
    ::: content
    ``` highlight
    from("direct:abc")
        .inputType("myScheme:myTransformer")
        .to("...");
    ```
    :::
    ::::

XML

:   :::: listingblock
    ::: content
    ``` highlight
    <camelContext id="camel" xmlns="http://camel.apache.org/schema/spring">
        <route>
            <from uri="direct:abc"/>
            <inputType urn="myScheme:myTransformer"/>
            <to uri="..."/>
        </route>
    </camelContext>
    ```
    :::
    ::::
:::
::::
:::::

::: paragraph
As mentioned earlier the transformer may also skip the name and just use
a `scheme` (e.g. `xml`) in order to apply to all data type
transformations of a given scheme (e.g. `xml:Order`,
`xml:OrderResponse`, `xml:anything`)
:::

::: paragraph
When using the combination of `fromType/toType` as an identifier the
transformer gets matched automatically by the given data types used on
the Camel route (e.g. inputType) and the given Exchange data type
(specified by the Exchange message using `DataTypeAware` interface).
:::

::: paragraph
In general, the transformer resolving mechanism tries to find the best
match when searching for a proper transformation from a given data type
to a given data type. The mechanism tries to find exact matches for
`fromType` and `toType` first, then wildcard matches (using transformers
for the given data type scheme only), then named transformers using
`scheme:name` identifiers, then named transformers matching `scheme`
only.
:::

::: paragraph
If not already preloaded by the configuration the Transformer resolving
mechanism also performs lazy loading of transformer implementations
using the factory finder resource path lookup.
:::
:::::::::::::

::::::: sect2
### DataFormat Transformer Options {#_dataformat_transformer_options}

+-----------------+-----------------------------------------------------+
| Name            | Description                                         |
+=================+=====================================================+
| type            | Data Format type                                    |
+-----------------+-----------------------------------------------------+
| ref             | reference to the Data Format ID                     |
+-----------------+-----------------------------------------------------+

::: paragraph
Here is an example to specify
[Bindy](components:dataformats:bindy-dataformat.html) DataFormat type:
:::

::::: exampleblock
:::: content
::: dlist

Java

:   :::: listingblock
    ::: content
    ``` highlight
    BindyDataFormat bindy = new BindyDataFormat();
    bindy.setType(BindyType.Csv);
    bindy.setClassType(com.example.Order.class);
    transformer()
        .fromType(com.example.Order.class)
        .toType("csv:CSVOrder")
        .withDataFormat(bindy);
    ```
    :::
    ::::

XML

:   :::: listingblock
    ::: content
    ``` highlight
    <dataFormatTransformer fromType="java:com.example.Order" toType="csv:CSVOrder">
        <bindy id="csvdf" type="Csv" classType="com.example.Order"/>
    </dataFormatTransformer>
    ```
    :::
    ::::
:::
::::
:::::
:::::::
:::::::::::::::::::
::::::::::::::::::::

:::::::::: sect1
## Endpoint Transformer Options {#_endpoint_transformer_options}

::::::::: sectionbody
+-----------------+-----------------------------------------------------+
| Name            | Description                                         |
+=================+=====================================================+
| ref             | Reference to the Endpoint ID                        |
+-----------------+-----------------------------------------------------+
| uri             | Endpoint URI                                        |
+-----------------+-----------------------------------------------------+

::: paragraph
Here is an example to specify endpoint URI in Java DSL:
:::

:::: listingblock
::: content
``` highlight
transformer()
    .fromType("xml")
    .toType("json")
    .withUri("component:componentPathOptions?mappingFile=myMapping.xml...");
```
:::
::::

::: paragraph
And here is an example to specify endpoint ref in XML DSL:
:::

:::: listingblock
::: content
``` highlight
<endpointTransformer ref="myEndpoint" fromType="xml" toType="json"/>
```
:::
::::
:::::::::
::::::::::

::::::::: sect1
## Custom Transformer Options {#_custom_transformer_options}

:::::::: sectionbody
::: paragraph
Note that Transformer must be a subclass of
`org.apache.camel.spi.Transformer`
:::

+-----------------+-----------------------------------------------------+
| Name            | Description                                         |
+=================+=====================================================+
| ref             | Reference to the custom Transformer bean ID         |
+-----------------+-----------------------------------------------------+
| className       | Fully qualified class name of the custom            |
|                 | Transformer class                                   |
+-----------------+-----------------------------------------------------+

::: paragraph
Here is an example to specify custom Transformer class:
:::

::::: exampleblock
:::: content
::: dlist

Java

:   :::: listingblock
    ::: content
    ``` highlight
    transformer()
        .fromType("xml")
        .toType("json")
        .withJava(com.example.MyCustomTransformer.class);
    ```
    :::
    ::::

XML

:   :::: listingblock
    ::: content
    ``` highlight
    <customTransformer className="com.example.MyCustomTransformer" fromType="xml" toType="json"/>
    ```
    :::
    ::::
:::
::::
:::::
::::::::
:::::::::

:::::::::::::::: sect1
## Load Transformer Options {#_load_transformer_options}

::::::::::::::: sectionbody
::: paragraph
Users are able to preload known default transformers. Also users may
load transformers via classpath scan.
:::

+-----------------+-----------------------------------------------------+
| Name            | Description                                         |
+=================+=====================================================+
| defaults        | Loads known default transformer implementations     |
|                 | (e.g. plain-text, application-octet-stream)         |
+-----------------+-----------------------------------------------------+
| location        | Classpath location to scan for transformer          |
|                 | implementations. Transformer implementations must   |
|                 | use the `org.apache.camel.spi.DataTypeTransformer`  |
|                 | annotation to get recognized by the scanner.        |
+-----------------+-----------------------------------------------------+

::: paragraph
Here is an example to load default Transformer classes:
:::

::::: exampleblock
:::: content
::: dlist

Java

:   :::: listingblock
    ::: content
    ``` highlight
    transformer()
        .withDefaults()
    ```
    :::
    ::::

XML

:   :::: listingblock
    ::: content
    ``` highlight
    <loadTransformer defaults="true"/>
    ```
    :::
    ::::
:::
::::
:::::

::: paragraph
Here is an example to load Transformer classes via classpath scan:
:::

::::: exampleblock
:::: content
::: dlist

Java

:   :::: listingblock
    ::: content
    ``` highlight
    transformer()
        .scan("org.apache.camel.transformer.standard")
    ```
    :::
    ::::

XML

:   :::: listingblock
    ::: content
    ``` highlight
    <loadTransformer packageScan="org.apache.camel.transformer.standard"/>
    ```
    :::
    ::::
:::
::::
:::::

::: paragraph
The classpath scan looks for classes that use the
`org.apache.camel.spi.DataTypeTransformer` annotation. The annotation
defines the transformer name and/or the supported from/to data types.
:::

:::: listingblock
::: content
``` highlight
@DataTypeTransformer(name = "uppercase")
public class UppercaseDataTypeTransformer extends Transformer {

    @Override
    public void transform(Message message, DataType fromType, DataType toType) {
        message.setBody(message.getBody(String.class).toUpperCase());
    }
}
```
:::
::::
:::::::::::::::
::::::::::::::::

:::::::::::: sect1
## Example {#_example}

::::::::::: sectionbody
::: paragraph
For example to declare the Endpoint Transformer which uses xslt
component to transform from `xml:ABCOrder` to `xml:XYZOrder`, we can do
as follows:
:::

::::: exampleblock
:::: content
::: dlist

Java

:   :::: listingblock
    ::: content
    ``` highlight
    transformer()
        .fromType("xml:ABCOrder")
        .toType("xml:XYZOrder")
        .withUri("xslt:transform.xsl");
    ```
    :::
    ::::

XML

:   :::: listingblock
    ::: content
    ``` highlight
    <camelContext id="camel" xmlns="http://camel.apache.org/schema/spring">
        <transformers>
            <endpointTransformer uri="xslt:transform.xsl" fromType="xml:ABCOrder" toType="xml:XYZOrder"/>
        </transformers>
        ....
    </camelContext>
    ```
    :::
    ::::
:::
::::
:::::

::: paragraph
If you have following route definition, above transformer will be
applied when `direct:abc` endpoint sends the message to `direct:xyz`:
:::

::::: exampleblock
:::: content
::: dlist

Java

:   :::: listingblock
    ::: content
    ``` highlight
    from("direct:abc")
        .inputType("xml:ABCOrder")
        .to("direct:xyz");

    from("direct:xyz")
        .inputType("xml:XYZOrder")
        .to("somewhere:else");
    ```
    :::
    ::::

XML

:   :::: listingblock
    ::: content
    ``` highlight
    <camelContext id="camel" xmlns="http://camel.apache.org/schema/spring">
        <route>
            <from uri="direct:abc"/>
            <inputType urn="xml:ABCOrder"/>
            <to uri="direct:xyz"/>
        </route>
        <route>
            <from uri="direct:xyz"/>
            <inputType urn="xml:XYZOrder"/>
            <to uri="somewhere:else"/>
        </route>
    </camelContext>
    ```
    :::
    ::::
:::
::::
:::::
:::::::::::
::::::::::::

::::: sect1
## See Also {#_see_also}

:::: sectionbody
::: paragraph
The [Validator](validator.html) is a related functionality.
:::
::::
:::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
