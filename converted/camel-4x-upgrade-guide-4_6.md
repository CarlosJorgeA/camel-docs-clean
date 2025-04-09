::: {#header}
# Apache Camel 4.x Upgrade Guide
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
::::: {#preamble}
:::: sectionbody
::: paragraph
This document is for helping you upgrade your Apache Camel application
from Camel 4.x to 4.y. For example, if you are upgrading Camel 4.0 to
4.2, then you should follow the guides from both 4.0 to 4.1 and 4.1 to
4.2.
:::
::::
:::::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Upgrading Camel 4.5 to 4.6 {#_upgrading_camel_4_5_to_4_6}

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
:::::: sect2
### variables {#_variables}

::: paragraph
When using `variableReceive` then the variable is only set if processing
the `Exchange` was completely successfully.
:::

::: paragraph
For example, calling a route that fails due to an exception being thrown
(even if `onException` or `errorHandler` are in use) then the variable
is no longer set. Also, if the route is marked for rollback, or to stop
continuing routing with `.stop()`.
:::

::: paragraph
This is the same logic that the routing engine uses, whether to continue
routing the `Exchange` or not.
:::
::::::

:::: sect2
### camel-core-model {#_camel_core_model}

::: paragraph
Harmonized model for defining custom beans to be the same for Kamelets
and generic beans in XML/YAML DSLs. Before, there used to be three
specialized model classes that have been refactored into a single class,
offering the same capabilities for configuring beans (constructors,
properties, builder classes, init/destroy methods etc.)
:::
::::

:::: sect2
### XML DSL {#_xml_dsl}

::: paragraph
When using XML DSL to define properties on `<bean>` then `<property>`
must now be declared inside `<properties>`.
:::
::::

::::::::::::::::: sect2
### YAML DSL {#_yaml_dsl}

::: paragraph
When using YAML DSL to define properties on `bean` then `property` has
been removed in favour of using `properties`.
:::

::: paragraph
Before:
:::

:::: listingblock
::: content
``` highlight
    beans:
      - name: "myProcessor"
        type: "#class:com.foo.MyClass"
        property:
          - key: "payload"
            value: "test-payload"
```
:::
::::

::: paragraph
After:
:::

:::: listingblock
::: content
``` highlight
    beans:
      - name: "myProcessor"
        type: "#class:com.foo.MyClass"
        properties:
          payload: "test-payload"
```
:::
::::

::: paragraph
Renamed `streamCaching` to `streamCache` on the `route`
:::

::: paragraph
Before:
:::

:::: listingblock
::: content
``` highlight
route:
  streamCaching: false
  from:
    uri: "direct:foo"
    steps:
      - to: "mock:bar"
```
:::
::::

::: paragraph
After:
:::

:::: listingblock
::: content
``` highlight
route:
  streamCache: false
  from:
    uri: "direct:foo"
    steps:
      - to: "mock:bar"
```
:::
::::
:::::::::::::::::

:::: sect2
### camel-elasticsearch {#_camel_elasticsearch}

::: paragraph
The class
`org.apache.camel.component.es.aggregation.ElastichsearchBulkRequestAggregationStrategy`
has been renamed to
`org.apache.camel.component.es.aggregation.ElasticsearchBulkRequestAggregationStrategy`.
:::
::::

::::: sect2
### camel-rest-openapi {#_camel_rest_openapi}

::: paragraph
The `specifciationUri` in the `rest-api` component has changed from
being a `java.net.URI` to a `String` type , as it uses Camels
`ResourceLoader` to load from pluggable resources and also more tooling
friendly.
:::

::: paragraph
The validator using Atlassian `swagger-request-validator` JAR has been
removed, as this library is not JakartaEE compatible, and was causing
classloading problems. The validator is now using a similar validation
as Rest DSL. However, the validator no longer checks for JSON payloads
if any required nodes are missing.
:::
:::::

:::::: sect2
### camel-langchain4j-chat {#_camel_langchain4j_chat}

::: paragraph
The Camel Langchain4j Chat component name has been changed from
`` camel-langchain-chat`to `camel-langchain4j-chat `` to adhere to the
standardized naming conventions within the LangChain4j ecosystem.
:::

::: paragraph
If you are migrating from Camel 4.5, please ensure the following:
:::

::: ulist
- Update your maven dependencies, changing from
  `` camel-langchain-chat`to `camel-langchain4j-chat ``

- Adjust your endpoints, replacing `langchain-chat` with
  `langchain4j-chat`

- Transfer any Camel class references from
  `org.apache.camel.component.chat.ChatLangchain*` to
  `org.apache.camel.component.langchain4j.chat.ChatLangchain4j*`

- Modify the header name `The prompt Template` from
  `CamelLangChainChatPromptTemplate` to
  `CamelLangChain4jChatPromptTemplate`
:::
::::::

:::::: sect2
### camel-langchain4j-embeddings {#_camel_langchain4j_embeddings}

::: paragraph
The Camel Langchain4j Embeddings component name has been changed from
`camel-langchain-embedings` to `camel-langchain4j-embedings` to adhere
to the standardized naming conventions within the LangChain4j ecosystem.
:::

::: paragraph
If you are migrating from Camel 4.5, please ensure the following
:::

::: ulist
- Update your maven dependencies, changing from
  `` camel-langchain-embedings`to `camel-langchain4j-embedings ``

- Adjust your endpoints, replacing `langchain-embedings` with
  `langchain4j-embedings`

- Transfer any Camel class references from
  `org.apache.camel.component.langchain.embeddings.LangChainEmbeddings*`
  to
  `org.apache.camel.component.langchain4j.embeddings.LangChain4jEmbeddings*`

- Modify the header name `The Finish Reason` from
  `CamelLangChainEmbeddingsFinishReason` to
  `CamelLangChain4jEmbeddingsFinishReason`

- Modify the header name `The Input Token Count` from
  `CamelLangChainEmbeddingsInputTokenCount` to
  `CamelLangChain4jEmbeddingsInputTokenCount`

- Modify the header name `The Output Token Count` from
  `CamelLangChainEmbeddingsOutputTokenCount` to
  `CamelLangChain4jEmbeddingsOutputTokenCount`

- Modify the header name `The Total Token Count` from
  `CamelLangChainEmbeddingsTotalTokenCount` to
  `CamelLangChain4jEmbeddingsTotalTokenCount`

- Modify the header name `A dense vector embedding of a text` from
  `CamelLangChainEmbeddingsVector` to `CamelLangChain4jEmbeddingsVector`
:::
::::::

:::: sect2
### camel-platform-http {#_camel_platform_http}

::: paragraph
The `PlatformHttpEngine` class has changed the `createConsumer` method
to return a
`org.apache.camel.component.platform.http.spi.PlatformHttpConsumer`
type, instead of `org.apache.camel.Consumer`.
:::
::::

:::: sect2
### camel-platform-http-vertx {#_camel_platform_http_vertx}

::: paragraph
The id used for multipart file upload attachments on `AttachmentMessage`
has changed from being the name of the uploaded file, to the value
specifed on the `name` field of the `Content-Disposition` header sent in
the request body.
:::
::::

:::: sect2
### camel-google-sheets {#_camel_google_sheets}

::: paragraph
The option `scopes` is changed from a `Collection` to be a `String` type
to make it easy to configure in endpoint URI from all DSLs and tooling.
Multiple scopes can be separated by comma.
:::
::::

:::: sect2
### camel-kafka {#_camel_kafka}

::: paragraph
The Kafka component now supports custom subscription adapters for
applications with very complex subscription logic.
:::
::::

::::: sect2
### camel-azure-servicebus {#_camel_azure_servicebus}

::: paragraph
The Camel Azure ServiceBus consumer has been refactored to internally
use the high-level client instead of the low-level client to more easily
support automatic reconnection, competing consumers and high
availability, amongst other concerns. The corresponding changes to
configuration options are:
:::

::: ulist
- The consumer now supports the Competing Consumers EIP. This can be
  enabled by increasing the newly added `maxConcurrentCalls` option to a
  value greater than 1.

- The `disableAutoComplete` option has been removed. Auto-complete is
  always disabled on the underlying client to ensure that Camel is able
  to correctly complete, abandon or dead-letter consumed messages as
  appropriate. The presence of the `disableAutoComplete` option made
  little sense, since it is not propagated to the underlying client and
  enabling this option caused Camel not to take any steps to
  acknowledge/reject consumed messages. Enabling this option would
  result in message locks being held indefinitely, which is almost
  certainly not the desired behaviour.

- The high level client always operates in `receiveMessages` mode (peek
  mode is not supported), so the `consumerOperation` option has been
  removed, along with the `ServiceBusConsumerOperationDefinition` enum.
  The associated `peekNumMaxMessages` option has also been removed.

- The `receiverAsyncClient` option has been replaced with a
  `processorClient` option to enable use of a custom-configured client.
  The parameter type accepted by this option is
  `ServiceBusProcessorClient`.

- The `reconnectDelay` option has been removed, since reconnection is
  now handled internally by the client.
:::
:::::

::::: sect2
### camel-jbang {#_camel_jbang}

::: paragraph
When running using `camel run --source-dir=mydir` then Camel JBang will
now preload existing files on startup, such as `application.properties`,
and beans and routes (same as if you run with `cd mydir; camel run *`).
This allows configuring settings on Camel on startup, which was not
possible beforehand.
:::

::: paragraph
The `--open-api` option has changed from *code-first* to use the new
*contract-first* Rest DSL style by using the specification file direct
as-is.
:::
:::::

:::::: sect2
### camel-as2 {#_camel_as2}

::: paragraph
The `camel-as2` component has upgraded HTTP Client 4.x to 5.x, and
because of that, there are some options that have changed.
:::

::: paragraph
Changes for AS2-MDN asynchronous delivery:
:::

::: ulist
- Added an option allowing clients to request an asynchronous receipt by
  including a \'Receipt-Delivery-Option\' header specifying the return
  URL in the request that is sent to the server.

- Added a consumer capable of receiving signed or unsigned asynchronous
  message disposition notifications sent by the message receiver
  confirming receipt of the message.
:::
::::::

::::::: sect2
### camel-spring-boot {#_camel_spring_boot}

::: paragraph
The autoconfiguration of [Cluster Service](clustering.html)
implementations has been moved to dedicated starters:
:::

+----------------------+-----------------------------------------------+
| Type                 | Starter                                       |
+======================+===============================================+
| Consul               | camel-consul-cluster-service-starter          |
+----------------------+-----------------------------------------------+
| File                 | camel-file-cluster-service-starter            |
+----------------------+-----------------------------------------------+
| Infinispan           | camel-infinispan-cluster-service-starter      |
+----------------------+-----------------------------------------------+
| JGroups Lock         | camel-jgroups-cluster-service-starter         |
+----------------------+-----------------------------------------------+
| JGroups Raft         | camel-jgroups-raft-cluster-service-starter    |
+----------------------+-----------------------------------------------+
| Kubernetes           | camel-kubernetes-cluster-service-starter      |
+----------------------+-----------------------------------------------+
| Zookeeper            | camel-zookeeper-cluster-service-starter       |
+----------------------+-----------------------------------------------+

::: paragraph
The Cluster Services are turned on by default unless they are explicitly
disabled, for example:
:::

:::: listingblock
::: content
``` highlight
camel.cluster.consul.enabled = false
```
:::
::::
:::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
