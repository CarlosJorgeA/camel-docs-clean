::: {#header}
# Route Template
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
::::::: {#preamble}
:::::: sectionbody
::: paragraph
A Route template is as its name implies a template for a route, which is
used to create routes from a set of input parameters. In other words,
route templates are parameterized routes.
:::

::: paragraph
*Route template* + *input parameters* ⇒ *route*
:::

::: paragraph
From a route template, you can create one or more routes.
:::
::::::
:::::::

:::::::::::::::::::: sect1
## Defining route templates in the DSL {#_defining_route_templates_in_the_dsl}

::::::::::::::::::: sectionbody
::: paragraph
Route templates are to be defined in the DSL (just like routes) as shown
in the following:
:::

:::: listingblock
::: content
``` highlight
public class MyRouteTemplates extends RouteBuilder {

    @Override
    public void configure() throws Exception {
        // create a route template with the given name
        routeTemplate("myTemplate")
            // here we define the required input parameters (can have default values)
            .templateParameter("name")
            .templateParameter("greeting")
            .templateParameter("myPeriod", "3s")
            // here comes the route in the template
            // notice how we use {{name}} to refer to the template parameters
            // we can also use {{propertyName}} to refer to property placeholders
            .from("timer:{{name}}?period={{myPeriod}}")
                .setBody(simple("{{greeting}} ${body}"))
                .log("${body}");
    }
}
```
:::
::::

::: paragraph
And in Spring XML DSL
:::

:::: listingblock
::: content
``` highlight
<camelContext>
  <routeTemplate id="myTemplate">
    <templateParameter name="name"/>
    <templateParameter name="greeting"/>
    <templateParameter name="myPeriod" defaultValue="3s"/>
    <route>
      <from uri="timer:{{name}}?period={{myPeriod}}"/>
      <setBody><simple>{{greeting}} ${body}</simple></setBody>
      <log message="${body}"/>
    </route>
  </routeTemplate>
</camelContext>
```
:::
::::

::: paragraph
And in XML DSL
:::

:::: listingblock
::: content
``` highlight
<routeTemplates xmlns="http://camel.apache.org/schema/spring">
    <routeTemplate id="myTemplate">
        <templateParameter name="name"/>
        <templateParameter name="greeting"/>
        <templateParameter name="myPeriod" defaultValue="3s"/>
        <route>
            <from uri="timer:{{name}}?period={{myPeriod}}"/>
            <setBody><simple>{{greeting}} ${body}</simple></setBody>
            <log message="${body}"/>
        </route>
    </routeTemplate>
</routeTemplates>
```
:::
::::

::: paragraph
In the examples above, there was one route template, but you can define
as many as you want. Each template must have a unique id. The template
parameters are used for defining the parameters the template accepts. As
you can see, there are three parameters: *`name`*, *`greeting`*, and
*`myPeriod`*. The first two parameters are mandatory, whereas
*`myPeriod`* is optional as it has a default value of 3s.
:::

::: paragraph
The template parameters are then used in the route as regular property
placeholders with the `{{ }}` syntax. Notice how we use `{{name}}` and
`{{greeting}}` in the timer endpoint and the simple language.
:::

::: paragraph
The route can, of course, use regular property placeholders as well. Now
imagine there was a property placeholder with the name greeting:
:::

:::: listingblock
::: content
``` highlight
greeting = Davs
```
:::
::::

::: paragraph
Then Camel would normally have used this value `Davs` when creating the
route. However, as the route template has defined a template parameter
with the same name `greeting` then a value must be provided when
creating routes from the template.
:::

::: paragraph
Template parameters take precedence over regular property placeholders.
:::
:::::::::::::::::::
::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Creating a route from a route template {#_creating_a_route_from_a_route_template}

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
To create routes from route templates, then you should use
`org.apache.camel.builder.TemplatedRouteBuilder`.
:::

::: paragraph
In the following code snippet, you can see how this is done with the
builder:
:::

:::: listingblock
::: content
``` highlight
// create two routes from the template
TemplatedRouteBuilder.builder(context, "myTemplate")
    .parameter("name", "one")
    .parameter("greeting", "Hello")
    .add();

TemplatedRouteBuilder.builder(context, "myTemplate")
    .parameter("name", "two")
    .parameter("greeting", "Bonjour")
    .parameter("myPeriod", "5s")
    .add();
```
:::
::::

::: paragraph
And in Java DSL:
:::

:::: listingblock
::: content
``` highlight
templatedRoute("myTemplate")
        .parameter("name", "one")
        .parameter("greeting", "Hello");
templatedRoute("myTemplate")
        .parameter("name", "two")
        .parameter("greeting", "Bonjour")
        .parameter("myPeriod", "5s");
```
:::
::::

::: paragraph
And in Spring XML DSL:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
  <templatedRoute routeTemplateRef="myTemplate">
    <parameter name="name" value="one"/>
    <parameter name="greeting" value="Hello"/>
  </templatedRoute>
  <templatedRoute routeTemplateRef="myTemplate">
    <parameter name="name" value="two"/>
    <parameter name="greeting" value="Bonjour"/>
    <parameter name="myPeriod" value="5s"/>
  </templatedRoute>
</camelContext>
```
:::
::::

::: paragraph
And in XML DSL:
:::

:::: listingblock
::: content
``` highlight
<templatedRoutes xmlns="http://camel.apache.org/schema/spring">
  <templatedRoute routeTemplateRef="myTemplate">
    <parameter name="name" value="one"/>
    <parameter name="greeting" value="Hello"/>
  </templatedRoute>
  <templatedRoute routeTemplateRef="myTemplate">
    <parameter name="name" value="two"/>
    <parameter name="greeting" value="Bonjour"/>
    <parameter name="myPeriod" value="5s"/>
  </templatedRoute>
</templatedRoutes>
```
:::
::::

::: paragraph
And in YAML DSL:
:::

:::: listingblock
::: content
``` highlight
- templated-route:
    route-template-ref: "myTemplate"
    parameters:
      - name: "name"
        value: "one"
      - name: "greeting"
        value: "Hello"
- templated-route:
    route-template-ref: "myTemplate"
    parameters:
      - name: "name"
        value: "two"
      - name: "greeting"
        value: "Bonjour"
      - name: "myPeriod"
        value: "5s"
```
:::
::::

::: paragraph
The returned value from `add` is the route id of the new route that was
added. However `null` is returned if the route is not yet created and
added, which can happen if `CamelContext` is not started yet.
:::

::: paragraph
If no route id is provided, then Camel will auto assign a route id. In
the example above then Camel would assign route ids such as `route1`,
`route2` to these routes.
:::

::: paragraph
If you want to specify a route id, then use `routeId` as follows, where
the id is set to myCoolRoute:
:::

:::: listingblock
::: content
``` highlight
TemplatedRouteBuilder.builder(context, "myTemplate")
    .routeId("myCoolRoute")
    .parameter("name", "one")
    .parameter("greeting", "hello")
    .parameter("myPeriod", "5s")
    .add();
```
:::
::::

::: paragraph
And in Java DSL:
:::

:::: listingblock
::: content
``` highlight
templatedRoute("myTemplate")
        .routeId("myCoolRoute")
        .parameter("name", "one")
        .parameter("greeting", "hello")
        .parameter("myPeriod", "5s");
```
:::
::::

::: paragraph
And in Spring XML DSL:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
  <templatedRoute routeTemplateRef="myTemplate" routeId="myCoolRoute">
    <parameter name="name" value="one"/>
    <parameter name="greeting" value="hello"/>
    <parameter name="myPeriod" value="5s"/>
  </templatedRoute>
</camelContext>
```
:::
::::

::: paragraph
And in XML DSL:
:::

:::: listingblock
::: content
``` highlight
<templatedRoutes xmlns="http://camel.apache.org/schema/spring">
  <templatedRoute routeTemplateRef="myTemplate" routeId="myCoolRoute">
    <parameter name="name" value="one"/>
    <parameter name="greeting" value="hello"/>
    <parameter name="myPeriod" value="5s"/>
  </templatedRoute>
</templatedRoutes>
```
:::
::::

::: paragraph
And in YAML DSL:
:::

:::: listingblock
::: content
``` highlight
- templated-route:
    route-template-ref: "myTemplate"
    route-id: "myCoolRoute"
    parameters:
      - name: "name"
        value: "one"
      - name: "greeting"
        value: "hello"
      - name: "myPeriod"
        value: "5s"
```
:::
::::

::::::::::: sect2
### Using template parameters with Java DSL simple builder {#_using_template_parameters_with_java_dsl_simple_builder}

::: paragraph
When using Java DSL and simple language, then beware that you should not
use the *simple fluent builder* when defining the simple
expressions/predicates.
:::

::: paragraph
For example, given the following route template in Java DSL:
:::

:::: listingblock
::: content
``` highlight
public class MyRouteTemplates extends RouteBuilder {

    @Override
    public void configure() throws Exception {
        routeTemplate("myTemplate")
            .templateParameter("name")
            .templateParameter("color")
            .from("direct:{{name}}")
                .choice()
                    .when(simple("{{color}}").isEqualTo("red"))
                        .to("direct:red")
                    .otherwise()
                        .to("color:other")
                .end();
    }
}
```
:::
::::

::: paragraph
Then notice how the simple predicate is using *simple fluent builder*
`simple("{{color}}").isEqualTo("red")`. This is **not supported** with
route templates and would not work when creating multiple routes from
the template.
:::

::: paragraph
Instead, the simple expression should be a literal String value *only*
as follows:
:::

:::: listingblock
::: content
        .when(simple("'{{color}}' == 'red'")
:::
::::
:::::::::::

:::::::::::::::::::::::: sect2
### Using hardcoded node IDs in route templates {#_using_hardcoded_node_ids_in_route_templates}

::: paragraph
If route templates contain hardcoded node IDs, then routes created from
templates will use the same IDs. Therefore, if two or more routes are
created from the same template, you will have *duplicate id detected*
error.
:::

::: paragraph
Given the route template below, then it has hardcoded ID (*`new-order`*)
in node calling the http services.
:::

:::: listingblock
::: content
``` highlight
public class MyRouteTemplates extends RouteBuilder {

    @Override
    public void configure() throws Exception {
        routeTemplate("orderTemplate")
            .templateParameter("queue")
            .from("jms:{{queue}}")
                .to("http:orderserver.acme.com/neworder").id("new-order")
                .log("Processing order");
    }
}
```
:::
::::

::: paragraph
When creating routes from templates, you can then provide a *prefix*
which is used for all node IDs. This allows to create 2 or more routes
without *duplicate id* errors.
:::

::: paragraph
For example in the following, we create a new route *`myCoolRoute`* from
the *`myTemplate`* template, and use a prefix of *`web`*.
:::

::: paragraph
And in Java DSL
:::

:::: listingblock
::: content
``` highlight
templatedRoute("orderTemplate")
        .routeId("webOrder")
        .prefixId("web")
        .parameter("queue", "order.web");
```
:::
::::

::: paragraph
Then we can create a 2nd route:
:::

:::: listingblock
::: content
``` highlight
templatedRoute("orderTemplate")
        .routeId("ftpOrder")
        .prefixId("ftp")
        .parameter("queue", "order.ftp");
```
:::
::::

::: paragraph
And in Spring XML DSL:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
  <templatedRoute routeTemplateRef="orderTemplate" routeId="webOrder" prefixId="web">
    <parameter name="queue" value="web"/>
  </templatedRoute>
</camelContext>
```
:::
::::

::: paragraph
And in XML DSL:
:::

:::: listingblock
::: content
``` highlight
<templatedRoutes xmlns="http://camel.apache.org/schema/spring">
  <templatedRoute routeTemplateRef="orderTemplate" routeId="webOrder" prefixId="web">
    <parameter name="queue" value="web"/>
  </templatedRoute>
</templatedRoutes>
```
:::
::::

::: paragraph
And in YAML DSL:
:::

:::: listingblock
::: content
``` highlight
- templated-route:
    route-template-ref: "orderTemplate"
    route-id: "webOrder"
    prefix-id: "web"
    parameters:
      - name: "queue"
        value: "web"
```
:::
::::
::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Binding beans to route template {#_binding_beans_to_route_template}

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
The route template allows binding beans that are locally scoped and only
used as part of creating routes from the template. This allows using the
same template to create multiple routes, where beans are local (private)
for each created route.
:::

::: paragraph
For example, given the following route template where we use
`templateBean` to set up the local bean as shown:
:::

:::: listingblock
::: content
``` highlight
routeTemplate("s3template")
    .templateParameter("region")
    .templateParameter("bucket")
    .templateBean("myClient", S3Client.class, rtc ->
            S3Client.builder().region(rtc.getProperty("region", Region.class)).build();
    )
    .from("direct:s3-store")
     // must refer to the bean with {{myClient}}
    .to("aws2-s3:{{bucket}}?amazonS3Client=#{{myClient}}")
```
:::
::::

::: paragraph
The template has two parameters to specify the AWS region and the S3
bucket. To connect to S3 then a
`software.amazon.awssdk.services.s3.S3Client` bean is necessary.
:::

::: paragraph
To create this bean, we specify this with the `templateBean` DSL where
we specify the bean id as `myClient`. The type of the bean can be
specified (`S3Client.class`), however, it is optional (can be used if
you need to let beans be discovered by type and not by name).
:::

::: paragraph
This ensures that the code creating the bean is executed later (when
Camel is creating a route from the template), then the code must be
specified as a *supplier*. Because we want during creation of the bean
access to template parameters, we use a Camel `BeanSupplier` which gives
access to `RouteTemplateContext` that is the *`rtc`* variable in the
code above.
:::

::: {.admonitionblock .important}
+-----------------------------------+-----------------------------------+
| ::: title                         | The local bean with id `myClient` |
| Important                         | **must** be referred to using     |
| :::                               | Camel's property placeholder      |
|                                   | syntax, eg `{{myClient}}` in the  |
|                                   | route template, as shown above    |
|                                   | with the *to* endpoint. This is   |
|                                   | because the local bean must be    |
|                                   | made unique and Camel will        |
|                                   | internally re-assign the bean id  |
|                                   | to use a unique id instead of     |
|                                   | `myClient`. And this is done with |
|                                   | the help of the property          |
|                                   | placeholder functionality.        |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
If multiple routes are created from this template, then each of the
created routes have their own `S3Client` bean created.
:::

::::::::::::::::::::::::::::::: sect2
### Binding beans to route templates from template builder {#_binding_beans_to_route_templates_from_template_builder}

::: paragraph
The `TemplatedRouteBuilder` also allows to bind local beans (which
allows specifying those beans) when creating routes from existing
templates.
:::

::: paragraph
Suppose the route template below is defined in XML:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
  <routeTemplate id="s3template">
    <templateParameter name="region"/>
    <templateParameter name="bucket"/>
    <route>
      <from uri="direct:s3-store"/>
      <to uri="aws2-s3:{{bucket}}?amazonS3Client=#{{myClient}}"/>
    </route>
  </routeTemplate>
</camelContext>
```
:::
::::

::: paragraph
The template has no bean bindings for `#{{myClient}}` which would be
required for creating the template.
:::

::: paragraph
When creating routes form the template via `TemplatedRouteBuilder` then
you can provide the bean binding if you desire the bean to be locally
scoped (not shared with others):
:::

:::: listingblock
::: content
``` highlight
TemplatedRouteBuilder.builder(context, "s3template")
    .parameter("region", "US-EAST-1")
    .parameter("bucket", "myBucket")
    .bean("myClient", S3Client.class,
                S3Client.builder()
                    .region(rtc.getProperty("region", Region.class))
                    .build())
    .routeId("mys3route")
    .add();
```
:::
::::

::: paragraph
As you can see the binding is similar to when using `templateBean`
directly in the route template.
:::

::: paragraph
And in Java DSL:
:::

:::: listingblock
::: content
``` highlight
templatedRoute("s3template")
        .parameter("region", "US-EAST-1")
        .parameter("bucket", "myBucket")
        .bean("myClient", S3Client.class,
                rtc -> S3Client.builder() // (1)
                    .region(rtc.getProperty("region", Region.class))
                    .build())
        .routeId("mys3route");
```
:::
::::

::: {.colist .arabic}
1.  Note that the third parameter of the `bean` method is not directly
    the bean but rather a factory method that will be used to create the
    bean, here we use a lambda expression as factory method.
:::

::: paragraph
And in XML DSL:
:::

:::: listingblock
::: content
``` highlight
  <templatedRoute routeTemplateRef="s3template" routeId="mys3route">
    <parameter name="region" value="US-EAST-1"/>
    <parameter name="bucket" value="myBucket"/>
    <bean name="myClient" type="software.amazon.awssdk.services.s3.S3Client"
          scriptLanguage="groovy"> <!--(1)-->
        <script>
            import software.amazon.awssdk.services.s3.S3Client
            S3Client.builder()
                .region(rtc.getProperty("region", Region.class))
                .build()
        </script>
    </bean>
  </templatedRoute>
```
:::
::::

::: {.colist .arabic}
1.  For non-Java DSL, in case of a complex bean factory, you can still
    rely on a language like `groovy` to define your bean factory inside
    a `script` element.
:::

::: paragraph
And in YAML DSL:
:::

:::: listingblock
::: content
``` highlight
- templated-route:
    route-template-ref: "s3template"
    route-id: "mys3route"
    parameters:
      - name: "region"
        value: "US-EAST-1"
      - name: "bucket"
        value: "myBucket"
    beans:
      - name: "myClient"
        type: "software.amazon.awssdk.services.s3.S3Client"
        scriptLanguage: "groovy"
        script: | # (1)
            import software.amazon.awssdk.services.s3.S3Client
            S3Client.builder()
                .region(rtc.getProperty("region", Region.class))
                .build()
```
:::
::::

::: {.colist .arabic}
1.  For non-Java DSL, in case of a complex bean factory, you can still
    rely on a language like `groovy` to define your bean factory as
    value of the `script` key.
:::

::: paragraph
Instead of binding the beans from the template builder, you could also
create the bean outside the template, and bind it by reference.
:::

:::: listingblock
::: content
``` highlight
final S3Client myClient = S3Client.builder().region(Region.US_EAST_1).build();

TemplatedRouteBuilder.builder(context, "s3template")
    .parameter("region", Region.US_EAST_1)
    .parameter("bucket", "myBucket")
    .bean("myClient", myClient)
    .routeId("mys3route")
    .add();
```
:::
::::

::: paragraph
And in Java DSL:
:::

:::: listingblock
::: content
``` highlight
templatedRoute("s3template")
        .parameter("region", "US-EAST-1")
        .parameter("bucket", "myBucket")
        .bean("myClient", S3Client.class, rtc -> myClient)
        .routeId("mys3route");
```
:::
::::

::: paragraph
You should prefer to create the local beans directly from within the
template (if possible) because this ensures the route template has this
out of the box. Otherwise, the bean must be created or provided every
time a new route is created from the route template. However, the latter
gives freedom to create the bean in any other custom way.
:::
:::::::::::::::::::::::::::::::

:::::::::::::::::: sect2
### Binding beans to route templates using bean types {#_binding_beans_to_route_templates_using_bean_types}

::: paragraph
You can create a local bean by referring to a fully qualified class name
which Camel will use to create a new local bean instance. When using
this, the created bean is created via default constructor of the class.
:::

::: paragraph
The bean instance can be configured with properties via getter/setter
style. The previous example with creating the AWS S3Client would not
support this kind as this uses *fluent builder* pattern (not
getter/setter).
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | In **Camel 4.6** onwards, you can |
| Tip                               | also use constructor arguments    |
| :::                               | for beans                         |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
So suppose we have a class as follows:
:::

:::: listingblock
::: content
``` highlight
public class MyBar {
    private String name;
    private String address;

    // getter/setter omitted

    public String location() {
        return "The bar " + name + " is located at " + address;
    }
}
```
:::
::::

::: paragraph
Then we can use the `MyBar` class as a local bean in a route template as
follows:
:::

:::: listingblock
::: content
``` highlight
routeTemplate("barTemplate")
    .templateParameter("bar")
    .templateParameter("street")
    .templateBean("myBar")
        .typeClass("com.foo.MyBar")
        .property("name", "{{bar}}")
        .property("address", "{{street}}")
    .end()
    .from("direct:going-out")
    .to("bean:{{myBar}}")
```
:::
::::

::: paragraph
With Java DSL, you can also refer to the bean class using type safe way:
:::

:::: listingblock
::: content
``` highlight
.templateBean("myBar")
    .typeClass(MyBar.class)
    .property("name", "{{bar}}")
    .property("address", "{{street}}")
.end()
```
:::
::::

::: paragraph
In XML DSL you would do:
:::

:::: listingblock
::: content
``` highlight
<camelContext xmlns="http://camel.apache.org/schema/spring">
    <routeTemplate id="myBar">
        <templateParameter name="bar"/>
        <templateParameter name="street"/>
        <templateBean name="myBean" type="#class:com.foo.MyBar">
            <properties>
                <property key="name" value="{{bar}}"/>
                <property key="address" value="{{street}}"/>
            </properties>
        </templateBean>
        <route>
            <from uri="direct:going-out"/>
            <to uri="bean:{{myBar}}"/>
        </route>
    </routeTemplate>
</camelContext>
```
:::
::::
::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::::::: sect2
### Binding beans to route templates using scripting languages {#_binding_beans_to_route_templates_using_scripting_languages}

::: paragraph
You can use scripting languages like groovy, java, mvel to create the
bean. This allows defining route templates with the scripting language
built-in (such as groovy).
:::

::: paragraph
For example, creating the AWS S3 client can be done as shown in Java
(with inlined groovy code):
:::

:::: listingblock
::: content
``` highlight
routeTemplate("s3template")
    .templateParameter("region")
    .templateParameter("bucket")
    .templateBean("myClient", "groovy",
            "software.amazon.awssdk.services.s3.S3Client.S3Client.builder()
            .region(rtc.getProperty("region", Region.class))
            .build()"
    )
    .from("direct:s3-store")
     // must refer to the bean with {{myClient}}
    .to("aws2-s3:{{bucket}}?amazonS3Client=#{{myClient}}")
```
:::
::::

::: paragraph
The groovy code can be externalized into a file on the classpath or file
system, by using `resource:` as prefix, such as:
:::

:::: listingblock
::: content
``` highlight
routeTemplate("s3template")
    .templateParameter("region")
    .templateParameter("bucket")
    .templateBean("myClient", "groovy", "resource:classpath:s3bean.groovy")
    .from("direct:s3-store")
     // must refer to the bean with {{myClient}}
    .to("aws2-s3:{{bucket}}?amazonS3Client=#{{myClient}}")
```
:::
::::

::: paragraph
Then create the file `s3bean.groovy` in the classpath root:
:::

:::: listingblock
::: content
``` highlight
import software.amazon.awssdk.services.s3.S3Client
S3Client.builder()
    .region(rtc.getProperty("region", Region.class))
    .build()
```
:::
::::

::: paragraph
The route template in XML DSL can then also use groovy language to
create the bean as follows:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
  <routeTemplate id="s3template">
    <templateParameter name="region"/>
    <templateParameter name="bucket"/>
    <templateBean name="myClient" type="groovy">
        <script>
            import software.amazon.awssdk.services.s3.S3Client
            S3Client.builder()
                .region(rtc.getProperty("region", Region.class))
                .build()
        </script>
    </templateBean>
    <route>
      <from uri="direct:s3-store"/>
      <to uri="aws2-s3:{{bucket}}?amazonS3Client=#{{myClient}}"/>
    </route>
  </routeTemplate>
</camelContext>
```
:::
::::

::: paragraph
Notice how the groovy code can be inlined directly in the route template
in XML also. Of course, you can also externalize the bean creation code
to an external file, by using `resource:` as prefix:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
  <routeTemplate id="s3template">
    <templateParameter name="region"/>
    <templateParameter name="bucket"/>
    <templateBean name="myClient" type="groovy">
        <script>resource:classpath:s3bean.groovy</script>
    </templateBean>
    <route>
      <from uri="direct:s3-store"/>
      <to uri="aws2-s3:{{bucket}}?amazonS3Client=#{{myClient}}"/>
    </route>
  </routeTemplate>
</camelContext>
```
:::
::::

::: paragraph
The languages supported are:
:::

+-------------+--------------------------------------------------------+
| Type        | Description                                            |
+=============+========================================================+
| **bean**    | Calling a method on a Java class to create the bean.   |
+-------------+--------------------------------------------------------+
| **groovy**  | Using a groovy script to create the bean.              |
+-------------+--------------------------------------------------------+
| **java**    | Java code which is runtime compiled (using jOOR        |
|             | library) to create the bean.                           |
+-------------+--------------------------------------------------------+
| **mvel**    | To use a Mvel template script to create the bean.      |
+-------------+--------------------------------------------------------+
| **ognl**    | To use OGNL template script to create the bean.        |
+-------------+--------------------------------------------------------+
| ***name***  | To use a third-party language by the given *`name`* to |
|             | create the bean.                                       |
+-------------+--------------------------------------------------------+

::: paragraph
Camel will bind `RouteTemplateContext` as the root object with name
`rtc` when evaluating the script. This means you can get access to all
the information from `RouteTemplateContext` and `CamelContext` via
`rtc`.
:::

::: paragraph
This is what we have done in the scripts in the previous examples where
we get hold of a template parameter with:
:::

:::: listingblock
::: content
``` highlight
    rtc.getProperty('region', String.class)
```
:::
::::

::: paragraph
To get access to `CamelContext` you can do:
:::

:::: listingblock
::: content
``` highlight
    var cn = rtc.getCamelContext().getName()
```
:::
::::

::: paragraph
The most powerful languages to use are groovy and java. The other
languages are limited in flexibility as they are not complete
programming languages, but are more suited for templating needs.
:::

::: paragraph
It is recommended to either use groovy or java, if creating the local
bean requires coding, and the route templates are not defined using Java
code.
:::

::: paragraph
The bean language can be used when creating the local bean from an
existing Java method (static or not-static method), and the route
templates are not defined using Java code.
:::

::: paragraph
For example suppose there is a class named `com.foo.MyAwsHelper` that
has a method called `createS3Client` then you can call this method from
the route template in XML DSL:
:::

:::: listingblock
::: content
``` highlight
<camelContext>
  <routeTemplate id="s3template">
    <templateParameter name="region"/>
    <templateParameter name="bucket"/>
    <templateBean name="myClient" type="bean">
        <script>com.foo.MyAwsHelper?method=createS3Client</script>
    </templateBean>
    <route>
      <from uri="direct:s3-store"/>
      <to uri="aws2-s3:{{bucket}}?amazonS3Client=#{{myClient}}"/>
    </route>
  </routeTemplate>
</camelContext>
```
:::
::::

::: paragraph
The method signature of createS3Client must then have one parameter for
the `RouteTemplateContext` as shown:
:::

:::: listingblock
::: content
``` highlight
public static S3Client createS3Client(RouteTemplateContext rtc) {
    return S3Client.builder()
        .region(rtc.getProperty("region", Region.class))
        .build();
}
```
:::
::::

::: paragraph
If you are using pure Java code (both template and creating local bean),
then you can create the local bean using Java lambda style as previously
documented.
:::

::::::::: sect3
#### Configuring the type of the created bean {#_configuring_the_type_of_the_created_bean}

::: paragraph
The `type` must be set to define what FQN class the created bean.
:::

:::: listingblock
::: content
``` highlight
<camelContext>
  <routeTemplate id="s3template">
    <templateParameter name="region"/>
    <templateParameter name="bucket"/>
    <templateBean name="myClient" scriptLanguage="bean" type="software.amazon.awssdk.services.s3.S3Client">
        <script>com.foo.MyAwsHelper?method=createS3Client</script>
    </templateBean>
    <route>
      <from uri="direct:s3-store"/>
      <to uri="aws2-s3:{{bucket}}?amazonS3Client=#{{myClient}}"/>
    </route>
  </routeTemplate>
</camelContext>
```
:::
::::

::: paragraph
And in Java DSL you can do:
:::

:::: listingblock
::: content
``` highlight
routeTemplate("s3template")
    .templateParameter("region")
    .templateParameter("bucket")
    .templateBean("myClient", S3Client.class, "bean", "com.foo.MyAwsHelper?method=createS3Client")
    .from("direct:s3-store")
     // must refer to the bean with {{myClient}}
    .to("aws2-s3:{{bucket}}?amazonS3Client=#{{myClient}}")
```
:::
::::
:::::::::
::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

::::::: sect1
## Configuring route templates when creating route {#_configuring_route_templates_when_creating_route}

:::::: sectionbody
::: paragraph
There may be some special situations where you want to be able to do
some custom configuration/code when a route is about to be created from
a route template. To support this you can use the `configure` in the
route template DSL where you can specify the code to execute as show:
:::

:::: listingblock
::: content
``` highlight
routeTemplate("myTemplate")
    .templateParameter("myTopic")
    .configure((RouteTemplateContext rtc) ->
        // do some custom code here
    )
    .from("direct:to-topic")
    .to("kafka:{{myTopic}}");
```
:::
::::
::::::
:::::::

::::: sect1
## JMX management {#_jmx_management}

:::: sectionbody
::: paragraph
The route templates can be dumped as XML from the
`ManagedCamelContextMBean` MBean via the `dumpRouteTemplatesAsXml`
operation.
:::
::::
:::::

::::::::::: sect1
## Creating routes from a properties file {#_creating_routes_from_a_properties_file}

:::::::::: sectionbody
::: paragraph
When using `camel-main` you can specify the parameters for route
templates in `application.properties` file.
:::

::: paragraph
For example, given the route template below (from a `RouteBuilder`
class):
:::

:::: listingblock
::: content
``` highlight
routeTemplate("mytemplate")
    .templateParameter("input")
    .templateParameter("result")
    .from("direct:{{input}}")
        .to("mock:{{result}}");
```
:::
::::

::: paragraph
Then we can create two routes from this template by configuring the
values in the `application.properties` file:
:::

:::: listingblock
::: content
``` highlight
camel.route-template[0].template-id=mytemplate
camel.route-template[0].input=foo
camel.route-template[0].result=cheese

camel.route-template[1].template-id=mytemplate
camel.route-template[1].input=bar
camel.route-template[1].result=cheese
```
:::
::::
::::::::::
:::::::::::

::::::: sect1
## Creating routes from custom sources of template parameters {#_creating_routes_from_custom_sources_of_template_parameters}

:::::: sectionbody
::: paragraph
The SPI interface `org.apache.camel.spi.RouteTemplateParameterSource`
can be used to implement custom sources that are used during startup of
Camel to create routes via the templates with parameters from the custom
source(s).
:::

::: paragraph
For example, a custom source can be implemented to read parameters from
a shared database that Camel uses during startup to create routes. This
allows externalizing these parameters and as well to easily add more
routes with varying parameters.
:::

::: paragraph
To let Camel discover custom sources, then register the source into the
Camel registry.
:::
::::::
:::::::

::::: sect1
## See Also {#_see_also}

:::: sectionbody
::: paragraph
See the example
[camel-examples/examples/routetemplate/](https://github.com/apache/camel-examples/tree/main/routetemplate).
:::
::::
:::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
