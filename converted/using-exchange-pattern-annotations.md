::: {#header}
# Using Exchange Pattern Annotations
:::

::::::::::::::::::::::::::::::::::::::::: {#content}
::::::::: {#preamble}
:::::::: sectionbody
::: paragraph
Invoking InOut methods for
ref:components:eips:requestReply-eip.adoc\[request/reply\] when working
with [POJO Producing](pojo-producing.html) is typically synchronous. As
such, the caller will block until the server returns a result.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | ::: paragraph                     |
| Note                              | InOut means that there is an In   |
| :::                               | message for the input and an Out  |
|                                   | for the output/result.            |
|                                   | :::                               |
+-----------------------------------+-----------------------------------+
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | ::: paragraph                     |
| Note                              | Other books, posts and reference  |
| :::                               | guides may use the terms In/Out   |
|                                   | and In/Only for the patterns. In  |
|                                   | this guide we use InOut and       |
|                                   | InOnly respectively, as these are |
|                                   | the names used within Camel.      |
|                                   | :::                               |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
You can also implement support for [Event
Messages](components:eips:event-message.html) with Apache Camel, using
the InOnly [pattern](exchange-pattern.html). These are often called
\"fire and forget\" (i.e., like sending a JMS message but not waiting
for any response).
:::

::: paragraph
Since version 1.5 Camel supports annotations for specifying the message
exchange pattern on Java methods, classes or interfaces.
:::
::::::::
:::::::::

:::::::: sect1
## Specifying InOnly methods {#UsingExchangePatternAnnotations-SpecifyingInOnlymethods}

::::::: sectionbody
::: paragraph
Typically the InOut pattern is what most users want, but you can
customize to use InOnly using an annotation. For instance:
:::

:::: listingblock
::: content
``` highlight
public interface Foo {
  Object someInOutMethod(String input);
  String anotherInOutMethod(Cheese input);

  @InOnly
  void someInOnlyMethod(Document input);
}
```
:::
::::

::: paragraph
The above code shows three methods on an interface: \* the first two use
the default InOut mechanism \* the third one, `someInOnlyMethod` uses
the InOnly annotation to specify it as being a one-way method call.
:::
:::::::
::::::::

::::::::::: sect1
## Class level annotations {#UsingExchangePatternAnnotations-Classlevelannotations}

:::::::::: sectionbody
::: paragraph
You can also use class level annotations to default all methods in an
interface to a pattern:
:::

:::: listingblock
::: content
``` highlight
@InOnly
public interface Foo {
  void someInOnlyMethod(Document input);
  void anotherInOnlyMethod(String input);
}
```
:::
::::

::: paragraph
Apache Camel will detect annotations on base classes or interfaces. For
instance, suppose you created a client side proxy for the following
code:
:::

:::: listingblock
::: content
``` highlight
public class MyFoo implements Foo {
  ...
}
```
:::
::::

::: paragraph
In this case, the methods inherited from Foo would be InOnly.
:::
::::::::::
:::::::::::

:::::::: sect1
## Overloading a class level annotation {#UsingExchangePatternAnnotations-Overloadingaclasslevelannotation}

::::::: sectionbody
::: paragraph
You can overload a class level annotation on specific methods. Suppose
you have a class or interface with many InOnly methods, but you want to
annote just one or two methods as InOut. You can do it like this:
:::

:::: listingblock
::: content
``` highlight
@InOnly
public interface Foo {
  void someInOnlyMethod(Document input);
  void anotherInOnlyMethod(String input);

  @InOut
  String someInOutMethod(String input);
}
```
:::
::::

::: paragraph
In the above `Foo` interface the only the `someInOutMethod` will be
InOut.
:::
:::::::
::::::::

:::::::::::: sect1
## Using your own annotations {#UsingExchangePatternAnnotations-Usingyourownannotations}

::::::::::: sectionbody
::: paragraph
You might want to create your own annotations to represent a group of
different bits of metadata; such as combining synchrony, concurrency and
transaction behaviour.
:::

::: paragraph
In this case you can annotate your annotation with the `@Pattern`
annotation to the default exchange pattern you wish to use.
:::

::: paragraph
For example, lets say we want to create our own annotation called
`@MyAsyncService`:
:::

:::: listingblock
::: content
``` highlight
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE, ElementType.METHOD})

// lets add the message exchange pattern to it
@Pattern(ExchangePattern.InOnly)

// lets add some other annotations - maybe transaction behaviour?

public @interface MyAsyncService {
}
```
:::
::::

::: paragraph
Now we can use this annotation, and Camel will figure out the correct
exchange pattern.
:::

:::: listingblock
::: content
``` highlight
public interface Foo {
  void someInOnlyMethod(Document input);
  void anotherInOnlyMethod(String input);

  @MyAsyncService
  String someInOutMethod(String input);
}
```
:::
::::
:::::::::::
::::::::::::
:::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
