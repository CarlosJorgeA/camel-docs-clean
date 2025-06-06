::: {#header}
# Test Infrastructure
:::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
::::::: {#preamble}
:::::: sectionbody
::: paragraph
The components in the Camel Test Infra provide utilities to simplify
testing with Camel and other systems may interact with it.
:::

::: paragraph
The test infra is divided in two parts:
:::

::: ulist
- One that offers container provisioning features for all scopes

- Another that provides container provisioning features for tests.
:::
::::::
:::::::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Simulating the Test Infrastructure {#_simulating_the_test_infrastructure}

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
One of the first steps when implementing a new test is to identify how
to simulate infrastructure required for it to run. The test-infra module
provides a reusable library of infrastructure that can be used for that
purpose.
:::

::: paragraph
In general, the integration test leverages the features provided by the
project [TestContainers](https://www.testcontainers.org/) and uses
container images to simulate the environments. Additionally, it may also
support running the tests against remote environments as well as, when
available, embeddable components. This varies by each component, and it
is recommended to check the code for additional details.
:::

:::::::::::::::::::::::::::::::::: sect2
### Writing A New Test Infrastructure Module {#_writing_a_new_test_infrastructure_module}

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | ::: paragraph                     |
| Note                              | This section is aimed at Camel    |
| :::                               | maintainers that need to write    |
|                                   | new test infra components. End    |
|                                   | users can skip this section.      |
|                                   | :::                               |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
The test code abstracts the provisioning of test environments behind
service classes (i.e.: JMSService, JDBCService, etc). The purpose of the
service class is to abstract both the type service (i.e.: Kafka,
Strimzi, etc.) and the location of the service (i.e.: remote, local,
embedded, etc). This provides flexibility to test the code under
different circumstances (i.e.: using a remote JMS broker or using a
local JMS broker running in a container managed by TestContainers). It
makes it easier to hit edge cases as well as try different operating
scenarios (i.e.: higher latency, slow backends, etc).
:::

::: paragraph
When a container image is not available via TestContainers, tests can
provide their own implementation using officially available images. The
license must be compatible with Apache 2.0. If an official image is not
available, a Dockerfile to build the service can be provided. The
Dockerfile should try to minimize the container size and resource usage
whenever possible.
:::

::: paragraph
The container information must reside in a file named
`container.properties` which should contain the container fully
qualified name:
:::

:::: listingblock
::: content
``` highlight
opensearch.container=mirror.gcr.io/opensearchproject/opensearch:2.18.0
opensearch.container.ppc64le=icr.io/ppc64le-oss/opensearch-ppc64le:2.12.0
```
:::
::::

::: paragraph
The keys must follow the pattern `<name>.properties`. Specific
architectures can be added to the key to denote which container to be
used for each architecture. Currently accepted values are:
:::

::: ulist
- `aarch64`: for Arm

- `s390x`: for s390x (Linux On Mainframe)

- `ppc64le`: for 64-bit little ending power
:::

::: paragraph
It is also possible to use embeddable components when required, although
this usually leads to more code and higher maintenance.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | support for embeddable components |
| Note                              | may be removed in future          |
| :::                               | versions.                         |
+-----------------------------------+-----------------------------------+
:::

::::::::::::: sect3
#### Recommended Structure for Test Infrastructure Modules {#_recommended_structure_for_test_infrastructure_modules}

::: paragraph
The actual Service interface and implementation should be added under
`src/main`, while the actual integration with JUnit can be added to
`src/test`, in this way the classes needed to run the service (most of
them, with testcontainers) are decoupled by JUnit and can be run
separately.
:::

:::::::: sect4
##### Main Sources {#_main_sources}

::: paragraph
The service should provide an interface, named after the infrastructure
being implemented, and this interface should extend the
[`InfrastructureService`](https://github.com/apache/camel/blob/main/test-infra/camel-test-infra-common/src/main/java/org/apache/camel/test/infra/common/services/InfrastructureService.java)
interface.
:::

::: paragraph
Ideally, there should be two concrete implementations of the services:
one of the remote service (if applicable) and another for the container
service:
:::

:::: listingblock
::: content
``` highlight
              MyService
                 / \
                /   \
               /     \
 MyRemoteService    MyContainerService
```
:::
::::

::: paragraph
In most cases, a specialized service factory class is responsible for
creating the service according to runtime parameters and/or other test
scenarios constraints. When a service allows different service types or
locations to be selected, this should be done via command line
properties (`-D<property.name>=<value>`). For example, when allowing a
service to choose between running as a local container or as remote
instance, a property in the format `<name>.instance.type` should be
handled. Additional runtime parameters used in different scenarios,
should be handled as `<name>.<parameter>`. More complex services may use
the builder available through the factory classes to compose the service
accordingly.
:::
::::::::

::::: sect4
##### Test Sources {#_test_sources}

::: paragraph
Once the main sources are implemented,
[TestService](https://github.com/apache/camel/blob/main/test-infra/camel-test-infra-common/src/test/java/org/apache/camel/test/infra/common/services/TestService.java)
interface can be used to integrate the actual Service implementation
with JUnit and its lifecycle. The services should try to minimize the
test execution time and resource usage when running. As such, the
[BeforeAllCallback](https://junit.org/junit5/docs/5.1.1/api/org/junit/jupiter/api/extension/BeforeAllCallback.html)
and
[AfterAllCallback](https://junit.org/junit5/docs/5.1.1/api/org/junit/jupiter/api/extension/AfterAllCallback.html)
should be the preferred extensions whenever possible because they allow
the instance of the infrastructure to be static throughout the test
execution.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | ::: paragraph                     |
| Note                              | Bear in mind that, according to   |
| :::                               | the [JUnit 5                      |
|                                   | extensi                           |
|                                   | on](https://junit.org/junit5/docs |
|                                   | /5.1.1/api/org/junit/jupiter/api/ |
|                                   | extension/RegisterExtension.html) |
|                                   | model, the time of service        |
|                                   | initialization may differ         |
|                                   | depending on whether the service  |
|                                   | instance is declared as static or |
|                                   | not in the test class. As such,   |
|                                   | the code should make no           |
|                                   | assumptions as to its time of     |
|                                   | initialization.                   |
|                                   | :::                               |
+-----------------------------------+-----------------------------------+
:::
:::::
:::::::::::::

::::: sect3
#### Registering Properties {#_registering_properties}

::: paragraph
All services should register the properties, via `System.setProperty`
that allow access to the services. This is required to resolve those
properties when running tests using the Spring framework. This
registration allows the properties to be resolved in Spring's XML files.
:::

::: paragraph
This registration is done in the `registerProperties` methods during the
service initialization.
:::
:::::

::::::: sect3
#### Registering Properties Example: {#_registering_properties_example}

::: paragraph
Registering the properties in the concrete service implementation:
:::

:::: listingblock
::: content
``` highlight
    public void registerProperties() {
        // MyServiceProperties.MY_SERVICE_HOST is a string with value "my.service.host"
        System.setProperty(MyServiceProperties.MY_SERVICE_HOST, container.getHost());

        // MyServiceProperties.MY_SERVICE_PORT is a string with value "my.service.port"
        System.setProperty(MyServiceProperties.MY_SERVICE_PORT, String.valueOf(container.getServicePort()));

        // MyServiceProperties.MY_SERVICE_ADDRESS is a string with value "my.service.address"
        System.setProperty(MyServiceProperties.MY_SERVICE_ADDRESS, getServiceAddress());
    }

    public void initialize() {
        LOG.info("Trying to start the MyService container");
        container.start();

        registerProperties();
        LOG.info("MyService instance running at {}", getServiceAddress());
    }
```
:::
::::

::: paragraph
Then, when referring these properties in Camel routes or Spring XML
properties, you may use `{{my.service.host}}`, `{{my.service.port}}` and
`{{my.service.address}}`.
:::
:::::::

:::: sect3
#### Packaging Recommendations {#_packaging_recommendations}

::: paragraph
This is infrastructure code for testing, therefore, it should be package
as test type artifacts. The [parent
pom](https://github.com/apache/camel/blob/main/test-infra/camel-test-infra-parent)
should provide all the necessary bits for packaging the test
infrastructure.
:::
::::
::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::: sect2
### Using The Test Infrastructure in Tests {#_using_the_test_infrastructure_in_tests}

::: paragraph
Using the test infra in a new component test is rather straightforward,
similar to using any other reusable component. You start by declaring
the test infra dependencies in your pom file.
:::

::: paragraph
This should be similar to:
:::

:::: listingblock
::: content
``` highlight
<!-- test infra -->
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-test-infra-myservice</artifactId>
    <version>${project.version}</version>
    <type>test-jar</type>
    <scope>test</scope>
</dependency>
```
:::
::::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | ::: paragraph                     |
| Note                              | On the dependencies above, the    |
| :::                               | dependency version is set to      |
|                                   | `${project.version}`. This should |
|                                   | be adjusted to the Camel version  |
|                                   | when used outside the Camel Core  |
|                                   | project.                          |
|                                   | :::                               |
+-----------------------------------+-----------------------------------+
:::

::: paragraph
On the test class, add a member variable for the service and annotate it
with the
[\@RegisterExtension](https://junit.org/junit5/docs/5.1.1/api/org/junit/jupiter/api/extension/RegisterExtension.html),
to let JUnit 5 manage its lifecycle.
:::

:::: listingblock
::: content
``` highlight
@RegisterExtension
static MyService service = MyServiceServiceFactory.createService();
```
:::
::::

::: paragraph
More complex test services can be created using something similar to:
:::

:::: listingblock
::: content
``` highlight
@RegisterExtension
static MyService service = MyServiceServiceFactory
    .builder()
        .addRemoveMapping(MyTestClass::myCustomRemoteService) // this is rarely necessary
        .addLocalMapping(MyTestClass::staticMethodReturningAService) // sets the handler for -Dmy-service.instance.type=local-myservice-local-container
        .addMapping("local-alternative-service", MyTestClass::anotherMethodReturningAService) // sets the handler for -Dmy-service.instance.type=local-alternative-service
    .createService();
```
:::
::::

::: paragraph
You can use the methods as well as the registered properties to access
the test infrastructure services available. When using these properties
in Spring XML files, you may use those properties.
:::

:::: listingblock
::: content
``` highlight
<someSpringXmlElement httpHost="{{my.service.host}}" port="{{my.service.port}}" />
```
:::
::::

::: paragraph
It's also possible to use these properties in the test code itself. For
example, when setting up the test url for the Camel component:
:::

:::: listingblock
::: content
``` highlight
    protected RouteBuilder createRouteBuilder() throws Exception {
        return new RouteBuilder() {
            public void configure() {
                from("direct:put")
                    .to("mycomponent:someoption?host={{my.service.host}}&port={{my.service.port}}");
            }
        };
    }
```
:::
::::

::::::: sect3
#### Execution Ordering {#_execution_ordering}

::: paragraph
When combining the different modules of the test infra, you may need to
ensure that they execute in the proper order. You can do so by using
JUnit's `@Order` annotation.
:::

::: paragraph
For instance:
:::

:::: listingblock
::: content
``` highlight
    @Order(1)
    @RegisterExtension
    protected static KafkaService service = KafkaServiceFactory.createSingletonService();

    @Order(2)
    @RegisterExtension
    protected static CamelContextExtension contextExtension = new DefaultCamelContextExtension();
```
:::
::::
:::::::
:::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::: sect1
## Container Runtime Support {#_container_runtime_support}

::::::::::::: sectionbody
::: paragraph
Most of the test infrastructure in this module is based on containers.
Therefore, they will require a container runtime to run. The tests have
been written and tested using:
:::

::: ulist
- Docker

- [Podman](https://podman.io/)
:::

:::::::::: sect2
### Podman Support {#_podman_support}

::: paragraph
Assuming Podman is properly installed and configured to behave like
docker (i.e.: short name resolution, resolving docker.io registry,
etc.), the only requirement for using Podman is to export the
`DOCKER_HOST` variable before running the tests.
:::

:::::: sect3
#### Linux {#_linux}

::: paragraph
On most systems that should be similar to the following command:
:::

:::: listingblock
::: content
``` highlight
export DOCKER_HOST=unix:///run/user/$UID/podman/podman.sock
```
:::
::::
::::::

:::: sect3
#### OS X and Windows {#_os_x_and_windows}

::: paragraph
Running the test-infra with Podman on OS X and Windows should work on
many cases. However, it requires additional steps and has a few issues.
Therefore, it is not recommended at this time.
:::
::::
::::::::::
:::::::::::::
::::::::::::::

::::::: sect1
## Known Issues and/or Tips {#_known_issues_andor_tips}

:::::: sectionbody
::::: sect2
### Multi-architecture support {#_multi_architecture_support}

::: paragraph
Some containers don't have images available for all architectures. In
this case, it is recommended to:
:::

::: {.olist .arabic}
1.  use an alternative image from a reputable source if they provide an
    image for that architecture.

2.  create a `Dockerfile` and build your own if the system is available
    on that arch.

3.  disable the tests on that architecture.
:::
:::::
::::::
:::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
