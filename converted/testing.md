::: {#header}
# Testing
:::

:::::::::::::::::::::::::: {#content}
:::::: {#preamble}
::::: sectionbody
::: paragraph
Testing is a crucial activity in any piece of software development or
integration. Typically, Camel users would use various different
technologies wired together in a variety of EIPs with different
endpoints, languages, bean integration, and dependency injection, so
it's easy for things to go wrong! Testing is the crucial weapon to
ensure that things work as you would expect.
:::

::: paragraph
Camel is a Java library, so you can easily wire up tests in JUnit.
However, the Camel project has tried to make the testing of Camel as
easy and powerful as possible, so we have introduced the following
features.
:::
:::::
::::::

:::::::: sect1
## Testing modules {#_testing_modules}

::::::: sectionbody
::: paragraph
The following modules are supported:
:::

+-------------+--------------------------------------------------------+
| Component   | Description                                            |
+=============+========================================================+
| [`          | **JUnit 5**: Is an older standalone Java library       |
| camel-test- | letting you easily create Camel test cases using a     |
| junit5`](co | single Java class for all your configuration and       |
| mponents:ot | routing without.                                       |
| hers:test-j |                                                        |
| unit5.html) |                                                        |
+-------------+--------------------------------------------------------+
| [           | **JUnit 5**: Used for testing Camel in Camel Main mode |
| `camel-test |                                                        |
| -main-junit |                                                        |
| 5`](compone |                                                        |
| nts:others: |                                                        |
| test-main-j |                                                        |
| unit5.html) |                                                        |
+-------------+--------------------------------------------------------+
| [`cam       | **JUnit 5**: Used for testing Camel with Spring /      |
| el-test-spr | Spring Boot                                            |
| ing-junit5` |                                                        |
| ](component |                                                        |
| s:others:te |                                                        |
| st-spring-j |                                                        |
| unit5.html) |                                                        |
+-------------+--------------------------------------------------------+
| [`ca        | **Camel Test Infra**: Camel Test Infra is a set of     |
| mel-test-in | modules that leverage modern JUnit 5 features to       |
| fra`](test- | abstract the provisioning and execution of test        |
| infra.html) | infrastructure. Among other things, it provides        |
|             | abstraction of the infrastructure (based on Test       |
|             | Containers - being the de-facto successor of the       |
|             | camel-testcontainers components) as well as JUnit 5    |
|             | extensions for the Camel Context itself.               |
+-------------+--------------------------------------------------------+

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | If you are using Camel Quarkus,   |
| Note                              | then you can find information in  |
| :::                               | its documentation how to do       |
|                                   | testing with Quarkus and Camel.   |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
In all approaches, the test classes look pretty much the same in that
they all reuse the [Camel binding and injection
annotations](bean-integration.html).
:::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | For more details on the different |
| Tip                               | testing modules, then see their   |
| :::                               | respective documentation from the |
|                                   | links in the table above.         |
+-----------------------------------+-----------------------------------+
:::
:::::::
::::::::

::::::::::::::: sect1
## Testing functionality {#_testing_functionality}

:::::::::::::: sectionbody
::: paragraph
Camel provides a set of features that are common to use when writing
unit or integration tests with Camel.
:::

::::: sect2
### Testing endpoints {#_testing_endpoints}

::: paragraph
Camel provides a number of [endpoints](endpoint.html) which can make
testing easier.
:::

+-----------------+-----------------------------------------------------+
| Name            | Description                                         |
+=================+=====================================================+
| [Mock](co       | For testing routes and mediation rules using mocks  |
| mponents::mock- | and allowing assertions to be added to an endpoint. |
| component.html) |                                                     |
+-----------------+-----------------------------------------------------+
| [DataSet](compo | For load & soak testing, this endpoint provides a   |
| nents::dataset- | way to create huge numbers of messages for sending  |
| component.html) | to components and asserting that they are consumed  |
|                 | correctly.                                          |
+-----------------+-----------------------------------------------------+
| [DataSet        | Used to automatically load a set of expected        |
| T               | messages from another endpoint which is then        |
| est](components | compared to the messages that arrive at this        |
| ::dataset-test- | endpoint.                                           |
| component.html) |                                                     |
+-----------------+-----------------------------------------------------+

::: paragraph
The main endpoint is the [Mock](components::mock-component.html)
endpoint, which allows expectations to be added to different endpoints;
you can then run your tests and assert that your expectations are met at
the end.
:::
:::::

::::: sect2
### Stubbing out physical transport technologies {#_stubbing_out_physical_transport_technologies}

::: paragraph
If you wish to test out a route but want to avoid actually using real
physical transport, then the following endpoints can be useful:
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | For example, to unit test a       |
| Note                              | transformation route rather than  |
| :::                               | performing a full integration     |
|                                   | test                              |
+-----------------------------------+-----------------------------------+
:::

+-----------------+-----------------------------------------------------+
| Name            | Description                                         |
+=================+=====================================================+
| [Direct](comp   | Direct invocation of the consumer from the producer |
| onents::direct- | so that single threaded (non-SEDA) in VM invocation |
| component.html) | is performed which can be useful to mock out        |
|                 | physical transports                                 |
+-----------------+-----------------------------------------------------+
| [SEDA](co       | Deliver messages asynchronously to consumers via a  |
| mponents::seda- | `BlockingQueue` which is good for testing           |
| component.html) | asynchronous transports                             |
+-----------------+-----------------------------------------------------+
| [Stub](co       | Works like [SEDA](components::stub-component.html)  |
| mponents::stub- | but does not validate the endpoint URI, which makes |
| component.html) | stubbing straightforward.                           |
+-----------------+-----------------------------------------------------+
:::::

:::: sect2
### Testing existing routes {#_testing_existing_routes}

::: paragraph
Camel provides some features to aid during testing of existing routes
where you cannot or will not use [Mock](components::mock-component.html)
etc. For example, you may have a production ready route which you want
to test with some third party API that sends messages into this route.
:::

+-----------------+-----------------------------------------------------+
| Name            | Description                                         |
+=================+=====================================================+
| [Notif          | Allows you to be notified when a certain condition  |
| yBuilder](notif | has occurred. For example, when the route has       |
| y-builder.html) | completed five messages. You can build complex      |
|                 | expressions to match your criteria when to be       |
|                 | notified.                                           |
+-----------------+-----------------------------------------------------+
| [AdviceWith](ad | Allows you to *advice* (enhance) an existing route  |
| vice-with.html) | using a [RouteBuilder](route-builder.html) style.   |
|                 | For example, you can send (or send and skip) a      |
|                 | message to a                                        |
|                 | [Mock](components::mock-component.html) endpoint    |
|                 | for validating the message send by Camel is as      |
|                 | expected.                                           |
+-----------------+-----------------------------------------------------+
::::

:::: sect2
### Third Party Testing libraries {#_third_party_testing_libraries}

::: paragraph
There are a number of third party testing libraries that Camel users
have found useful.
:::

+-----------------+-----------------------------------------------------+
| Name            | Description                                         |
+=================+=====================================================+
| [Citrus         | Framework for automated integration tests           |
| Integration     | supporting a wide range of message protocols and    |
| Framework]      | data formats                                        |
| (https://citrus |                                                     |
| framework.org/) |                                                     |
+-----------------+-----------------------------------------------------+
| [Citrus         | YAKS is a framework to enable Cloud Native BDD      |
| Yaks](http      | testing on Kubernetes                               |
| s://citrusframe |                                                     |
| work.org/yaks/) |                                                     |
+-----------------+-----------------------------------------------------+
::::
::::::::::::::
:::::::::::::::
::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
