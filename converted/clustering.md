::: {#header}
# Clustering
:::

::::::::::::::::::: {#content}
:::::: {#preamble}
::::: sectionbody
::: paragraph
Camel offers the following cluster related SPI:
:::

::: ulist
- **Cluster Service**

  ::: paragraph
  A regular Camel service that manages cluster resources such as *views*
  (see below)
  :::

- **Cluster View**

  ::: paragraph
  Represent a view of the cluster with its own set of isolated
  resources. As today views provide supports for:
  :::

  ::: ulist
  - Leader Election

  - Topology events like members joining/leaving the cluster
  :::

- **Cluster Member**

  ::: paragraph
  Represent a member of the cluster.
  :::
:::
:::::
::::::

:::::::::: sect1
## Cluster SPI Setup {#_cluster_spi_setup}

::::::::: sectionbody
::: paragraph
A *Cluster Service* is just like any other camel service so set it up
you only need to register your implementations to the `CamelContext`:
:::

:::: listingblock
::: content
``` highlight
MyClusterServiceImpl service = new MyClusterServiceImpl();
context.addService(service);
```
:::
::::

::: paragraph
The configuration of the *Cluster Service* depends on the implementation
you have chose. Out of the box camel provides the following
implementations:
:::

+-----------------+-----------------+-----------------------------------+
| Type            | Module          | Class                             |
+=================+=================+===================================+
| consul          | camel-consul    | org.apache.camel.component.co     |
|                 |                 | nsul.cluster.ConsulClusterService |
+-----------------+-----------------+-----------------------------------+
| file            | camel-file      | org.apache.camel.component.fi     |
|                 |                 | le.cluster.FileLockClusterService |
+-----------------+-----------------+-----------------------------------+
| infinispan      | c               | org.                              |
|                 | amel-infinispan | apache.camel.component.infinispan |
|                 |                 | .cluster.InfinispanClusterService |
+-----------------+-----------------+-----------------------------------+
| jgroups         | camel-jgroups   | or                                |
|                 |                 | g.apache.camel.component.jgroups. |
|                 |                 | cluster.JGroupsLockClusterService |
+-----------------+-----------------+-----------------------------------+
| jgroups-raft    | cam             | org.apa                           |
|                 | el-jgroups-raft | che.camel.component.jgroups.raft. |
|                 |                 | cluster.JGroupsRaftClusterService |
+-----------------+-----------------+-----------------------------------+
| kubernetes      | c               | org.                              |
|                 | amel-kubernetes | apache.camel.component.kubernetes |
|                 |                 | .cluster.KubernetesClusterService |
+-----------------+-----------------+-----------------------------------+
| zookeeper       | camel-zookeeper | or                                |
|                 |                 | g.apache.camel.component.zookeepe |
|                 |                 | r.cluster.ZooKeeperClusterService |
+-----------------+-----------------+-----------------------------------+

::: paragraph
Configuration examples:
:::

::: ulist
- **Spring Boot**

  :::: listingblock
  ::: content
  ``` highlight
  camel.cluster.file.enabled = true
  camel.cluster.file.id = ${random.uuid}
  camel.cluster.file.root = ${java.io.tmpdir}
  ```
  :::
  ::::

- **Spring XML**

  :::: listingblock
  ::: content
  ``` highlight
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="
           http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd
           http://camel.apache.org/schema/spring
           http://camel.apache.org/schema/spring/camel-spring.xsd">

    <bean id="zx" class="org.apache.camel.component.zookeeper.cluster.ZooKeeperClusterService">
      <property name="id" value="node-1"/>
      <property name="basePath" value="/camel/cluster"/>
      <property name="nodes" value="localhost:2181"/>
    </bean>

    <camelContext xmlns="http://camel.apache.org/schema/spring" autoStartup="false">
      ...
    </camelContext>

  </beans>
  ```
  :::
  ::::
:::
:::::::::
::::::::::

:::::: sect1
## Cluster SPI Usage {#_cluster_spi_usage}

::::: sectionbody
::: paragraph
The *Cluster SPI* is leveraged by the following new implementations:
:::

::: ulist
- **ClusteredRoutePolicy**

  ::: paragraph
  This is an implementation of a RoutePolicy that starts the routes it
  is associated to when the Cluster View it uses takes the leadership
  :::

  :::: listingblock
  ::: content
  ``` highlight
  context.addRoutes(new RouteBuilder {
      @Override
      public void configure() throws Exception {
          // Create the route policy
          RoutePolicy policy = ClusteredRoutePolicy.forNamespace("my-ns");

          // bind the policy to one or more routes
          from("timer:clustered?delay=1000&period=1000")
              .routePolicy(policy)
              .log("Route ${routeId} is running ...");
      }
  });
  ```
  :::
  ::::

  ::: paragraph
  To apply the same policy to all the routes a dedicated
  *RoutePolicyFactory* can be used:
  :::

  :::: listingblock
  ::: content
  ``` highlight
  // add the clustered route policy factory to context
  context.addRoutePolicyFactory(ClusteredRoutePolicyFactory.forNamespace("my-ns"));

  context.addRoutes(new RouteBuilder {
      @Override
      public void configure() throws Exception {
          // bind the policy to one or more routes
          from("timer:clustered?delay=1000&period=1000")
              .log("Route ${routeId} is running ...");
      }
  });
  ```
  :::
  ::::

- **ClusteredRouteController**

  ::: paragraph
  This is an implementation of the *RouteController SPI* that lets the
  camel context start then starts/stops the routes when the leadership
  is taken/lost. This is well integrated with spring-boot apps so
  assuming you have your routes set-up like:
  :::

  :::: listingblock
  ::: content
  ``` highlight
  @Bean
  public RouteBuilder routeBuilder() {
      return new RouteBuilder() {
          @Override
          public void configure() throws Exception {
              from("timer:heartbeat?period=10000")
                  .routeId("heartbeat")
                  .log("HeartBeat route (timer) ...");
              from("timer:clustered?period=5000")
                  .routeId("clustered")
                  .log("Clustered route (timer) ...");
          }
      };
  }
  ```
  :::
  ::::

  ::: paragraph
  You can then leverage Spring Boot configuration to make them
  clustered:
  :::

  :::: listingblock
  ::: content
  ``` highlight
  # enable the route controller
  camel.clustered.controller.enabled = true

  # define the default namespace for routes
  camel.clustered.controller.namespace = my-ns

  # exclude the route with id 'heartbeat' from the clustered ones
  camel.clustered.controller.routes[heartbeat].clustered = false
  ```
  :::
  ::::

- **Master Component**

  ::: paragraph
  The master component is similar to a *ClusteredRoutePolicy* but it
  works on consumer level so it ensures the only a single endpoint in a
  cluster is consuming resources at any point in time. Set it up is very
  easy and all you need is to prefix singleton endpoints according to
  the master component syntax:
  :::

  :::: listingblock
  ::: content
  ``` highlight
  master:namespace:delegateUri
  ```
  :::
  ::::

  ::: paragraph
  A concrete example:
  :::

  :::: listingblock
  ::: content
  ``` highlight
  @Bean
  public RouteBuilder routeBuilder() {
      return new RouteBuilder() {
          @Override
          public void configure() throws Exception {
              from("timer:heartbeat?period=10000")
                  .routeId("heartbeat")
                  .log("HeartBeat route (timer) ...");

              from("master:my-ns:timer:clustered?period=5000")
                  .routeId("clustered")
                  .log("Clustered route (timer) ...");
          }
      };
  }
  ```
  :::
  ::::
:::
:::::
::::::
:::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
