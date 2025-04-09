::: {#header}
# Data Format DSL
:::

:::::::::::::::::::::: {#content}
::::::: {#preamble}
:::::: sectionbody
::: paragraph
The Data Format DSL is a builder API that allows using type safe
construction of Camel [Data Formats](data-format.html).
:::

::: paragraph
The Data Format DSL is exclusively available as part of the Java DSL.
:::

::: paragraph
The DSL can be accessed directly from the `RouteBuilder` thanks to the
method `dataFormat()`.
:::
::::::
:::::::

:::::::::::::::: sect1
## Using Data Format DSL {#_using_data_format_dsl}

::::::::::::::: sectionbody
::: paragraph
In the following example, a `CsvDataFormat` is created using the legacy
approach where the data format is instantiated explicitly and configured
using setters:
:::

:::: listingblock
::: content
``` highlight
public class MyRoutes extends RouteBuilder {
    @Override
    public void configure() {
        CsvDataFormat dataFormat = new CsvDataFormat(); // (1)
        dataFormat.setDelimiter("|"); // (2)
        from("direct:format")
            .setBody(constant(Map.of("foo", "abc", "bar", 123)))
            .marshal(dataFormat); // (3)
    }
}
```
:::
::::

::: {.colist .arabic}
1.  Instantiate the expected data format

2.  Configure the data format according to the needs

3.  Affect the data format with the expected configuration
:::

::: paragraph
The previous code could be simplified using the utility methods
available directly from the `DataFormatClause` corresponding to the type
returned by the `marshal()` and `unmarshal()` methods:
:::

:::: listingblock
::: content
``` highlight
public class MyRoutes extends RouteBuilder {
    @Override
    public void configure() {
        from("direct:format")
            .setBody(constant(Map.of("foo", "abc", "bar", 123)))
            .marshal()
            .csv(); // (1)
    }
}
```
:::
::::

::: {.colist .arabic}
1.  Select the `csv` data format with the default delimiter
:::

::: paragraph
This approach is suitable for very basic configuration, but as there are
only limited utility methods for each supported data format, for more
complex configuration, we can quickly face situations where the utility
method for our expected configuration doesn't exist. In this situation,
you can either use the legacy approach or the data format DSL like in
the next code snippet:
:::

:::: listingblock
::: content
``` highlight
public class MyRoutes extends RouteBuilder {
    @Override
    public void configure() {
        from("direct:format")
            .setBody(constant(Map.of("foo", "abc", "bar", 123)))
            .marshal(
                dataFormat() // (1)
                    .csv() // (2)
                        .delimiter(",") // (3)
                    .end() // (4)
            );
    }
}
```
:::
::::

::: {.colist .arabic}
1.  Give access to all the supported data formats

2.  Select the `csv` data format

3.  Configure the data format according to the needs

4.  Build the data format with the expected configuration
:::
:::::::::::::::
::::::::::::::::
::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
