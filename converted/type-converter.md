::: {#header}
# Type Converter
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
:::::::::: {#preamble}
::::::::: sectionbody
::: paragraph
Converting body payloads from one type to another is common when routing
messages between endpoints. Conversions regularly occur between the
following types:
:::

::: ulist
- `File`

- `String`

- `byte[]` and `ByteBuffer`

- `InputStream` and `OutputStream`

- `Reader` and `Writer`

- XML payloads such as `Document` and `Source`
:::

::: paragraph
For example to convert the message body to XML `Document` type then this
can be done as follows in Java:
:::

:::: listingblock
::: content
``` highlight
Message message = exchange.getMessage();
Document document = message.getBody(Document.class);
```
:::
::::

::: paragraph
Notice that the code only tells Camel what you **want** as the result
type (`Document`) and not what the input type is, or how Camel should do
this.
:::
:::::::::
::::::::::

:::::::::::::::::::::::::::: sect1
## How Type Conversion works {#_how_type_conversion_works}

::::::::::::::::::::::::::: sectionbody
::: paragraph
The type conversion strategy is defined by the
[TypeConverter](https://www.javadoc.io/doc/org.apache.camel/camel-api/current/org/apache/camel/TypeConverter.html)
interface. The interface has several methods, however the most important
and common API is:
:::

:::: listingblock
::: content
``` highlight
<T> T convertTo(Class<T> type, Exchange exchange, Object value) throws TypeConversionException;
```
:::
::::

::: paragraph
This API is used by Camel when it converts an object from one type to
another. However if you pay attention then this API only has the result
type in the contract. The input type is inferred from the *value*
parameter.
:::

::: paragraph
There are many type converters in Camel, some comes out of the box from
camel-core, and others are additional converters that are shipped in
various Camel components.
:::

::::::::::: sect2
### Type converter registry {#_type_converter_registry}

::: paragraph
To keep track of all those converters, then Camel has a registry for
type converters (`org.apache.camel.spi.TypeConverterRegistry`).
:::

::: paragraph
This registry keeps track of all possible type converter combinations,
such as which converters that can convert to an `InputStream` and so
forth.
:::

::: paragraph
So the example from before, what Camel would do is to lookup in the
`TypeConverterRegistry` to find a suitable converter that can convert
the given input value to the `Document` type.
:::

::: paragraph
The `TypeConverterRegistry` can be accessed via Java:
:::

:::: listingblock
::: content
``` highlight
TypeConverterRegistry tcr = camelContext.getTypeConverterRegistry();
TypeConverter tc = tcr.lookup(Document.class, InputStream.class);
```
:::
::::

::: paragraph
However often you would not work directly with the
`TypeConverterRegistry` or `TypeConverter` APIs in Camel; as type
conversion are often implicit in use where you would just declare the
result type; and Camel takes care of this.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | ::: paragraph                     |
| Note                              | In Camel, all the official Camel  |
| :::                               | components, come with source code |
|                                   | generated `TypeConverter` (via    |
|                                   | `camel-component-maven-plugin`)   |
|                                   | that allows Camel to load these   |
|                                   | converters very quickly, and      |
|                                   | invoke these type converters at   |
|                                   | runtime via quick Java method     |
|                                   | invocations (no reflection        |
|                                   | overhead).                        |
|                                   | :::                               |
+-----------------------------------+-----------------------------------+
:::
:::::::::::

:::::::::::: sect2
### Type converter registry utilization statistics {#_type_converter_registry_utilization_statistics}

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | as of Camel 4.7.0, the statistics |
| Note                              | collector in the registry has     |
| :::                               | been made immutable. As such,     |
|                                   | enabling collection of statistics |
|                                   | has to be done prior to creating  |
|                                   | the type converter registry.      |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
Camel can gather utilization statistics of the runtime usage of type
converters. These statistics are available in [JMX](jmx.html) as well as
from
[`TypeConverterRegistry#getStatistics()`](https://www.javadoc.io/doc/org.apache.camel/camel-api/latest/org/apache/camel/spi/TypeConverterRegistry.html#getStatistics--).
:::

::: paragraph
These statistics are turned off by default as there is some performance
overhead under very high concurrent load.
:::

::: paragraph
Enabling statistics in Java:
:::

:::: listingblock
::: content
``` highlight
CamelContext context = ...;
context.setTypeConverterStatisticsEnabled(true);
```
:::
::::

::: paragraph
Enabling statistics in XML DSL:
:::

:::: listingblock
::: content
``` highlight
<camelContext typeConverterStatisticsEnabled="true">
    ...
</camelContext>
```
:::
::::
::::::::::::
:::::::::::::::::::::::::::
::::::::::::::::::::::::::::

:::::::::: sect1
## TypeConverter using \@Converter annotation {#_typeconverter_using_converter_annotation}

::::::::: sectionbody
::: paragraph
All the type converters that come out of the box are coded as Java
methods on *converter* classes.
:::

::: paragraph
This means a class has been annotated with `@Converter` and the methods
in the class annotated with `@Converter` become a type converter *pair*
like in the following example:
:::

:::: listingblock
::: content
``` highlight
@Converter(generateLoader = true)
public class IOConverter {
    @Converter
    public static InputStream toInputStream(File file) throws FileNotFoundException {
        return new BufferedInputStream(new FileInputStream(file));
    }
}
```
:::
::::

::: paragraph
This is from camel-core where the `IOConverter` class has a number of
converters (only 1 shown). The method `toInputStream` is annotated with
`@Converter` which then becomes a type converter that can convert from
`File` to `InputStream`.
:::

::: paragraph
All these *converter* classes are discovered and loaded by Camel.
:::
:::::::::
::::::::::

:::::::::::::::::::::::::: sect1
## Discovering Type Converters {#_discovering_type_converters}

::::::::::::::::::::::::: sectionbody
::: paragraph
Camel automatically discovers and loads the type converters from all
JARs on the classpath at startup.
:::

::: paragraph
Camel searches the classpath for a file called
`META-INF/services/org/apache/camel/TypeConverterLoader` which lists all
type converter loader classes. These are automatically generated by the
Camel Component Package Plugin. These *loader* classes will load the
type converters into the Camel type converter registry and invoke them
in a *fast way* using standard Java method calls.
:::

::::::: sect2
### Discovering Type Converters (fast way) {#_discovering_type_converters_fast_way}

::: paragraph
To enable the fast type converter way, you should enable
`generateLoader = true` on the class level annotation as shown:
:::

:::: listingblock
::: content
``` highlight
@Converter(generateLoader = true)
public class IOConverter {
    @Converter
    public static InputStream toInputStream(File file) throws FileNotFoundException {
        return new BufferedInputStream(new FileInputStream(file));
    }
}
```
:::
::::

::: paragraph
And then you should have the [Camel Component Package
Plugin](camel-component-maven-plugin.html) in as build plugin when
compiling the project.
:::
:::::::

::::::::::::: sect2
### Discovering Type Converters in the fastest way {#_discovering_type_converters_in_the_fastest_way}

::: paragraph
In Camel 3.7 we optimized the type converter system for optimal
performance when using the built-in converters. This was done by bulking
together all the converters in the same Maven module into a single
class. The class has a single `convert` method where all the supported
converters are available and discovered in a fast way using Java
primitives.
:::

::: paragraph
To enable this then set `generateBulkLoader=true` in the class
`@Converter` annotation. You should do this for all the converter
classes within the same Maven artifact. Then they will be bulked
together into a single class.
:::

:::: listingblock
::: content
``` highlight
@Converter(generateBulkLoader = true)
public class IOConverter {
    @Converter
    public static InputStream toInputStream(File file) throws FileNotFoundException {
        return new BufferedInputStream(new FileInputStream(file));
    }
}
```
:::
::::

::: paragraph
There are few limitations:
:::

::: ulist
- fallback converters are not supported

- the order of the `@Converter` methods matters. If you have multiple
  `@Converter` methods that accept as *from* type types which are from
  the same class hierarchy then put the methods first that are the most
  concrete.
:::

::: paragraph
For example in `camel-xml-jaxp` we have in the `XmlConverter` multiple
`@Converter` methods which can convert to `DomSource`. We had to put the
method that takes `org.w3c.dom.Document` before the method that takes
`org.w3c.dom.Node` as `Document` extends `Node`.
:::

::: paragraph
The following code shows snippet of the source code generated bulk
class. As you can see we have the `Document` method before the `Node`
method below:
:::

:::: listingblock
::: content
``` highlight
        } else if (to == javax.xml.transform.dom.DOMSource.class) {
            if (value instanceof org.w3c.dom.Document) {
                return getXmlConverter().toDOMSource((org.w3c.dom.Document) value);
            }
            if (value instanceof org.w3c.dom.Node) {
                return getXmlConverter().toDOMSource((org.w3c.dom.Node) value);
            }
```
:::
::::
:::::::::::::

:::::: sect2
### Returning null values {#_returning_null_values}

::: paragraph
By default, when using a method in a POJO annotation with `@Converter`
returning `null` is not a valid response. If null is returned, then
Camel will regard that type converter as a *miss*, and prevent from
using it in the future. If `null` should be allowed as a valid response,
then you must specify this in the annotation (via `allowNull`) as shown:
:::

:::: listingblock
::: content
``` highlight
@Converter(allowNull = true)
public static InputStream toInputStream(File file) throws IOException {
    if (file.exist()) {
        return new BufferedInputStream(new FileInputStream(file));
    } else {
        return null;
    }
}
```
:::
::::
::::::
:::::::::::::::::::::::::
::::::::::::::::::::::::::

:::::::::::::::: sect1
## Fallback Type Converters {#_fallback_type_converters}

::::::::::::::: sectionbody
::: paragraph
The
[AnnotationTypeConverterLoader](https://www.javadoc.io/doc/org.apache.camel/camel-base/current/org/apache/camel/impl/converter/AnnotationTypeConverterLoader.html)
has been enhanced to also look for methods defined with a
`@FallbackConverter` annotation, and register it as a fallback type
converter.
:::

::: paragraph
Fallback type converters are used as a last resort for converting a
given value to another type. It is used when the regular type converters
give up. The fallback converters are also meant for a broader scope, so
its method signature is a bit different:
:::

:::: listingblock
::: content
``` highlight
@FallbackConverter
public static <T> T convertTo(Class<T> type, Exchange exchange, Object value, TypeConverterRegistry registry)
```
:::
::::

::: paragraph
Or you can use the non-generic signature.
:::

:::: listingblock
::: content
``` highlight
@FallbackConverter
public static Object convertTo(Class type, Exchange exchange, Object value, TypeConverterRegistry registry)
```
:::
::::

::: paragraph
And the method name can be anything (`convertTo` is not required as a
name), so it can be named `convertMySpecialTypes` if you like.\
The `Exchange` parameter is optional, just like the regular `@Converter`
methods.
:::

::: paragraph
The purpose with this broad scope method signature is allowing you to
control if you can convert the given type or not. The `type` parameter
holds the type we want the `value` converted to. It is used internally
in Camel for wrapper objects, so we can delegate the type conversion to
the body that is wrapped.
:::

::: paragraph
For instance in the method below we will handle all type conversions
that are based on the wrapper class `GenericFile` and we let Camel do
the type conversions on its body instead.
:::

:::: listingblock
::: content
``` highlight
@FallbackConverter
public static <T> T convertTo(Class<T> type, Exchange exchange, Object value, TypeConverterRegistry registry) {
    // use a fallback type converter so we can convert the embedded body
    // if the value is GenericFile
    if (GenericFile.class.isAssignableFrom(value.getClass())) {
        GenericFile file = (GenericFile) value;
        Class from = file.getBody().getClass();
        TypeConverter tc = registry.lookup(type, from);
        if (tc != null) {
            Object body = file.getBody();
            return tc.convertTo(type, exchange, body);
        }
    }
    return null;
}
```
:::
::::
:::::::::::::::
::::::::::::::::

:::::::::: sect1
## Writing your own Type Converters {#_writing_your_own_type_converters}

::::::::: sectionbody
::: paragraph
You are welcome to write your own converters. Remember to use the
`@Converter` annotations on the classes and methods you wish to use. And
on the top-level class add `Converter(generateLoader = true)` to support
the *fast way* of using type converters.
:::

::: ulist
- static methods are encouraged to reduce caching, but instance methods
  are fine, particularly if you want to allow optional dependency
  injection to customize the converter

- converter methods should be thread safe and reentrant
:::

:::::: sect2
### Exchange parameter {#_exchange_parameter}

::: paragraph
The type converter accepts the `Exchange` as an optional 2nd parameter.
This is usable if the type converter for instance needs information from
the current exchange. For instance combined with the encoding support it
is possible for type converters to convert with the configured encoding.
An example from camel-core for the `byte[]` → `String` converter:
:::

:::: listingblock
::: content
``` highlight
@Converter
public static String toString(byte[] data, Exchange exchange) {
    String charsetName = exchange.getProperty(Exchange.CHARSET_NAME, String.class);
    if (charsetName != null) {
        try {
            return new String(data, charsetName);
        } catch (UnsupportedEncodingException e) {
            // ignore
        }
    }
    return new String(data);
}
```
:::
::::
::::::
:::::::::
::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
