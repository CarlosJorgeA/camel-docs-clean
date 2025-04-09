::: {#header}
# Validator
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
::::: {#preamble}
:::: sectionbody
::: paragraph
Validator performs declarative validation of the message according to
the declared *Input Type* and/or *Output Type* on a route definition
which declares the expected message type.
:::
::::
:::::

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
and **name** is the individual data type name.
:::
::::::
:::::::

:::: sect1
## Supported Validators {#_supported_validators}

::: sectionbody
+-----------------+-----------------------------------------------------+
| Validator       | Description                                         |
+=================+=====================================================+
| Predicate       | Validate with using Expression or Predicate         |
| Validator       |                                                     |
+-----------------+-----------------------------------------------------+
| Endpoint        | Validate by forwarding to the Endpoint to be used   |
| Validator       | with validation component such as Validation        |
|                 | Component or Bean Validation Component.             |
+-----------------+-----------------------------------------------------+
| Custom          | Validate with using custom validator class.         |
| Validator       | Validator must be a subclass of                     |
|                 | `org.apache.camel.spi.Validator`                    |
+-----------------+-----------------------------------------------------+
:::
::::

::::: sect1
## Common Options {#_common_options}

:::: sectionbody
::: paragraph
All validators have following common options to specify which data type
is supported by the validator. `type` must be specified.
:::

+-----------------+-----------------------------------------------------+
| Name            | Description                                         |
+=================+=====================================================+
| type            | Data type to validate                               |
+-----------------+-----------------------------------------------------+
::::
:::::

::::::::::: sect1
## Predicate Validator Options {#_predicate_validator_options}

:::::::::: sectionbody
+-----------------+-----------------------------------------------------+
| Name            | Description                                         |
+=================+=====================================================+
| expression      | Expression or Predicate to be used for validation   |
+-----------------+-----------------------------------------------------+

::: paragraph
Here is an example to specify a validation predicate:
:::

::: paragraph
Java DSL:
:::

:::: listingblock
::: content
``` highlight
validator()
    .type("csv:CSVOrder")
    .withExpression(bodyAs(String.class).contains("{name:XOrder}"));
```
:::
::::

::: paragraph
XML DSL:
:::

:::: listingblock
::: content
``` highlight
<predicateValidator Type="csv:CSVOrder">
    <simple>${body} contains '{name:XOrder}'</simple>
</predicateValidator>
```
:::
::::
::::::::::
:::::::::::

::::::::::: sect1
## Endpoint Validator Options {#_endpoint_validator_options}

:::::::::: sectionbody
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
validator()
    .type("xml")
    .withUri("validator:xsd/schema.xsd");
```
:::
::::

::: paragraph
And here is an example to specify endpoint ref in XML DSL:
:::

:::: listingblock
::: content
``` highlight
<endpointValidator uri="validator:xsd/schema.xsd" type="xml"/>
```
:::
::::

::: paragraph
Note that the Endpoint Validator just forwards the message to the
specified endpoint. In above example, camel forwards the message to the
`validator:` endpoint, which actually is a [Validation
Component](components::validator-component.html). You can also use any
other validation component like Bean Validation Component.
:::
::::::::::
:::::::::::

:::::::::::: sect1
## Custom Validator Options {#_custom_validator_options}

::::::::::: sectionbody
::: paragraph
The validator must be an implementation of
`org.apache.camel.spi.Validator`
:::

+-----------------+-----------------------------------------------------+
| Name            | Description                                         |
+=================+=====================================================+
| ref             | Reference to the custom Validator bean ID           |
+-----------------+-----------------------------------------------------+
| className       | Fully qualified class name of the custom Validator  |
|                 | class                                               |
+-----------------+-----------------------------------------------------+

::: paragraph
Here is an example to specify custom Validator class:
:::

::: paragraph
Java DSL:
:::

:::: listingblock
::: content
``` highlight
validator()
    .type("json")
    .withJava(com.example.MyCustomValidator.class);
```
:::
::::

::: paragraph
XML DSL:
:::

:::: listingblock
::: content
``` highlight
<customTransformer className="com.example.MyCustomValidator" type="json"/>
```
:::
::::
:::::::::::
::::::::::::

:::::::::::::::::: sect1
## Examples {#_examples}

::::::::::::::::: sectionbody
::: paragraph
For example to declare the Endpoint Validator which uses validator
component to validate `xml:ABCOrder`, we can do as follows:
:::

::: paragraph
Java DSL:
:::

:::: listingblock
::: content
``` highlight
validator()
    .type("xml:ABCOrder")
    .withUri("validator:xsd/schema.xsd");
```
:::
::::

::: paragraph
And in XML DSL:
:::

:::: listingblock
::: content
``` highlight
<camelContext id="camel" xmlns="http://camel.apache.org/schema/spring">
    <validators>
        <endpointValidator uri="validator:xsd/schema.xsd" type="xml:ABCOrder"/>
    </validators>
</camelContext>
```
:::
::::

::: paragraph
If you have following route definition, above validator will be applied
when `direct:abc` endpoint receives the message. Note that
`inputTypeWithValidate` is used instead of `inputType` in Java DSL, and
the `validate` attribute on the inputType declaration is set to `true`
in XML DSL:
:::

::: paragraph
Java DSL:
:::

:::: listingblock
::: content
``` highlight
from("direct:abc")
    .inputTypeWithValidate("xml:ABCOrder")
    .log("${body}");
```
:::
::::

::: paragraph
XML DSL:
:::

:::: listingblock
::: content
``` highlight
<camelContext id="camel" xmlns="http://camel.apache.org/schema/spring">
    <route>
        <from uri="direct:abc"/>
        <inputType urn="xml:ABCOrder" validate="true"/>
        <log message="${body}"/>
    </route>
</camelContext>
```
:::
::::
:::::::::::::::::
::::::::::::::::::

::::: sect1
## See Also {#_see_also}

:::: sectionbody
::: paragraph
The [Transformer](transformer.html) is a related functionality.
:::
::::
:::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
