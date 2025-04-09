::: {#header}
# Apache Camel 3.x Upgrade Guide
:::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
::::: {#preamble}
:::: sectionbody
::: paragraph
This document is for helping you upgrade your Apache Camel application
from Camel 3.x to 3.y. For example if you are upgrading Camel 3.0 to
3.2, then you should follow the guides from both 3.0 to 3.1 and 3.1 to
3.2.
:::
::::
:::::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Upgrading Camel 3.8 to 3.9 {#_upgrading_camel_3_8_to_3_9}

::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::::::::: sect2
### API changes {#_api_changes}

::: paragraph
The `Consumer` API in `camel-api` has been enhanced to help support
Camel reducing the footprint during routing. One aspect is that we allow
recycling `Exchange` instances created by the consumers. This avoids
creating new `Exchange` instances in the memory for each incoming
message consumers process. By recycling
`` Exchange`s we reduce the overhead on the JVM garbage collector. This requires Camel to know if the `Exchange ``
should be recycled or not, and some API changes took place.
:::

::: paragraph
The `Consumer` API has two new methods which a consumer must use to
create an `Exchange` with `createExchange`. Default the exchange is auto
released when its complete in use, but some consumers needs custom
control, and can turn off auto release, which then requires the consumer
to manually release the exchange by calling `releaseExchange` when the
consumer is done with the exchange.
:::

::: paragraph
The default implementations in `DefaultConsumer` has adapted this API
and 3rd party components can continue as is, by using the older APIs.
For these 3rd party components to support recycling exchanges, then they
must be updated to use this new API.
:::

::: paragraph
A new `org.apache.camel.spi.ResourceLoader` has been introduced as part
of [CAMEL-16285](https://issues.apache.org/jira/browse/CAMEL-16285)
which provide a way to support additional schemas to resolve resources.
As a consequece:
:::

::: ulist
- `org.apache.camel.support.ResourceHelper` has been updated to use such
  mechanism instead fo the old one.

- it is not more possible to provide support for additional schemas
  using the URL protocol handler mechanism but instead, custom schemas
  can be added by implementing `org.apache.camel.spi.ResourceResolver`
  and either bind an instance to the Camel Registry (using
  `resource-loader-` as a prefix) or using the Service Factory mechanism
  (under the path
  `META-INF/services/org/apache/camel/resource-resolver/`)
:::

::: paragraph
As example, assuming you want to provide a support for the schema `foo`,
then you either have to binbd your instance to the Camel Registry with
`resource-loader-foo` as a key, or create a factory finder file with
path `META-INF/services/org/apache/camel/resource-resolver/foo` The
method for retrieving a resource URL provided by the
`org.apache.camel.support.ResourceHelper` class, i.e.
`resolveMandatoryResourceAsUrl` ad \` resolveResourceAsUr\` have been
amended to accept a `CamelContext` instance instead of a
`ClassResolver`.
:::
:::::::::

::::::: sect2
### Exchange properties {#_exchange_properties}

::: paragraph
The properties on `Exchange` have been optimized to separate into two:
internal state vs user properties.
:::

::: paragraph
The method `getProperties()` now only returns user properties. To
include internal properties as well, then use `getAllProperties()`.
:::

::: paragraph
The other APIs such as `getProperty(String)` works the same way as
before, being able to lookup a property regardless if it is internal or
custom.
:::

::: paragraph
The internal properties is a fixed set of known keys defined in the
`ExchangePropertyKey` enum class. These keys are used in camel-core such
as the routing engine, EIPs and others that needs to store internal
state on the `Exchange` which is done as exchange properties. Because
Camel end users can also store exchange properties then before they
would get mixed together. What we have done now is to separate them.
:::
:::::::

:::: sect2
### Choice and Filter EIP {#_choice_and_filter_eip}

::: paragraph
The `choice` and `filter` EIPs no longer store exchange property
`Exchange.FILTER_MATCHED`. The reason is the information is seldom in
use, and by removing we were able to optimize camel-core.
:::
::::

::::::: sect2
### OnCompletion EIP {#_oncompletion_eip}

::: paragraph
Camel now validates that a route has only 1 onCompletion. Previously
users could have code such as:
:::

:::: listingblock
::: content
``` highlight
from("direct:start")
        .onCompletion().onCompleteOnly().to("mock:ok").end()
        .onCompletion().onFailureOnly().to("mock:error").end()
        .to("mock:result");
```
:::
::::

::: paragraph
Which would lead to the last onCompletion override the first, meaning
that only `onCompletion().onFailureOnly()` would be active. Now this is
checked on startup and Camel reports an error.
:::
:::::::

::::::::::: sect2
### Modularized camel-spring {#_modularized_camel_spring}

::: paragraph
The `camel-spring` component has been modularized into:
:::

::: ulist
- `camel-spring` - Core module for Camel Spring support

- `camel-spring-xml` - XML DSL when using Spring XML (eg `<beans>`)
:::

::: paragraph
Also for Camel on Spring Boot:
:::

::: ulist
- `camel-spring-boot-starter` - Camel with Spring Boot

- `camel-spring-boot-xml-starter` - XML DSL when using Spring XML (eg
  `<beans>`) with Spring Boot
:::

::: paragraph
The motivation is to move out the XML DSL from `camel-spring` which
reduces the number of dependencies and makes the module lighter.
:::

::: paragraph
Users that use the Spring XML DSL (eg the top XML tag is Spring
`<beans>`) should migrate from using `camel-spring` to
`camel-spring-xml` as dependency.
:::

::: paragraph
Users that do not use Spring XML should not be affected.
:::

::: paragraph
In Spring XML `<proxy>` and \<remote\>\` have been removed. They were
only available in Spring XML and not in the other XML DSLs (Blueprint
and CDI).
:::
:::::::::::

:::::: sect2
### camel-spring-boot-starter vs camel-spring-boot-engine-starter {#_camel_spring_boot_starter_vs_camel_spring_boot_engine_starter}

::: paragraph
Based on the modularization of camel-spring we have made two starters
available when using Camel on Spring Boot
:::

::: ulist
- camel-spring-boot-starter - The regular starter that includes all the
  core components (similar to camel-core)

- camel-spring-boot-engine-starter - A minimal starter with just Camel
  core engine (similar to camel-core-engine)
:::

::: paragraph
We also made all the component -starter JARs not depend on any of the
above starters, which means you must pick the one to use above, and then
add which -starter JARs you want to use in your Camel spring boot
applications.
:::
::::::

:::: sect2
### Camel-AWS components removed {#_camel_aws_components_removed}

::: ulist
- Camel-AWS-SDB has been removed. There will be no substitution for
  this, because there isn't a SDK v2 client for this and furthermore the
  service is not listed in the AWS console anymore.

- Camel-AWS-Translate has been removed. Please switch to
  Camel-AWS2-Translate.

- Camel-AWS-SQS has been removed. Please switch to Camel-AWS2-SQS.

- Camel-AWS-SNS has been removed. Please switch to Camel-AWS2-SNS.

- Camel-AWS-MSK has been removed. Please switch to Camel-AWS2-MSK.

- Camel-AWS-MQ has been removed. Please switch to Camel-AWS2-MQ.

- Camel-AWS-KMS has been removed. Please switch to Camel-AWS2-KMS.

- Camel-AWS-Kinesis has been removed. Please switch to
  Camel-AWS2-Kinesis.

- Camel-AWS-Kinesis Firehose has been removed. Please switch to
  Camel-AWS2-Kinesis-firehose.

- Camel-AWS-IAM has been removed. Please switch to Camel-AWS2-IAM.

- Camel-AWS-EKS has been removed. Please switch to Camel-AWS2-EKS.

- Camel-AWS-ECS has been removed. Please switch to Camel-AWS2-ECS.

- Camel-AWS-EC2 has been removed. Please switch to Camel-AWS2-EC2.

- Camel-AWS-DDB has been removed. Please switch to Camel-AWS2-DDB.

- Camel-AWS-DDB Streams has been removed. Please switch to
  Camel-AWS2-DDB Streams.

- Camel-AWS-CW has been removed. Please switch to Camel-AWS2-CW.

- Camel-AWS-S3 has been removed. Please switch to Camel-AWS2-S3.

- Camel-AWS-SWF has been removed. There won't be a replacement for it.
:::
::::

:::: sect2
### camel-aws2-sqs {#_camel_aws2_sqs}

::: paragraph
The option `deleteIfFiltered` has been changed to use a different
exchange property with key `Sqs2Constants.SQS_DELETE_FILTERED` which
must be set. Beforehand a property by Filter EIP was being used, however
this property has been removed due to optimizing core Camel.
:::
::::

:::: sect2
### Camel-Azure component {#_camel_azure_component}

::: ulist
- Camel-Azure has been removed. Please switch to
  Camel-Azure-Storage-Blob and Camel-Azure-Storage-Queue.
:::
::::

:::: sect2
### camel-freemarker component {#_camel_freemarker_component}

::: paragraph
The freemarker component now has turned off localized lookup for
templates. This can be turned back on by setting `localizedLookup=true`
on the component.
:::
::::

:::: sect2
### Camel-AWS2 components {#_camel_aws2_components}

::: ulist
- Camel-AWS2-sqs has now the autoCreateQueue option set to false as
  default, so you'll need to create entities before or set the option
  explicitly to true

- Camel-AWS2-sns has now the autoCreateTopic option set to false as
  default, so you'll need to create entities before or set the option
  explicitly to true

- Camel-AWS2-s3 has now the autoCreateBucket option set to false as
  default, so you'll need to create entities before or set the option
  explicitly to true
:::
::::

:::: sect2
### Camel-jmh, camel-performance and camel-itest-performance {#_camel_jmh_camel_performance_and_camel_itest_performance}

::: paragraph
Camel-jmh, camel-performance and camel-itest-performance have been moved
to a new repository
[https://github.com/apache/camel-performance-tests](https://github.com/apache/camel-performance-tests){.bare}
:::
::::

:::: sect2
### Camel-Debezium {#_camel_debezium}

::: paragraph
The camel-debezium-parent module has been renamed to
camel-debezium-common-parent, while the old the name is now used as
parent name for the middle folder. This is an internal change that
shouldn't affect end users, added here for tracking purpose anyway.
:::
::::

:::: sect2
### camel-jclouds {#_camel_jclouds}

::: paragraph
The camel-jclouds feature for Camel on Karaf has been removed.
:::
::::

:::::: sect2
### camel-kafka {#_camel_kafka}

::: paragraph
The camel-kafka consumer has been improved to be more roboust and have
more confirations how to deal with exceptions while polling from Kafka
Brokers. In case of any exception thrown, then previously the consumer
will re-connect and therefore try again. This leads to Kafka broker
would reasign the partions, but it may assign back the same consumer
again, or another standby consumer.
:::

::: paragraph
The new behavior is to only retry certain kind of exceptions which Kafka
has marked as retryable. Any other exceptions is now causing Camel error
handler to handle the caused exception (will log by default but you can
use onException etc), and then skip to next offset so the next message
can be polled and processed by Camel.
:::

::: paragraph
See the updated camel-kafka documentation for more details.
:::
::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
