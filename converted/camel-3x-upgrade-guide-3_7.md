::: {#header}
# Apache Camel 3.x Upgrade Guide
:::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
::::: {#preamble}
:::: sectionbody
::: paragraph
This document is for helping you upgrade your Apache Camel application
from Camel 3.x to 3.y. For example, if you are upgrading Camel 3.0 to
3.2, then you should follow the guides from both 3.0 to 3.1 and 3.1 to
3.2.
:::
::::
:::::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Upgrading Camel 3.6 to 3.7 {#_upgrading_camel_3_6_to_3_7}

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
:::::: sect2
### Modularized core {#_modularized_core}

::: paragraph
The core has been further modularized and is now split up into three new
core modules:
:::

::: ulist
- camel-core-model

- camel-core-reifier

- camel-core-processor
:::

::: paragraph
This separates the route model from the runtime processors via the
reifiers, by having the classes in their own modules.
:::
::::::

::::::::::: sect2
### API changes {#_api_changes}

::: paragraph
The class `BreakpointSupport` has moved from
`org/apache/camel/processor/interceptor/BreakpointSupport` to
`org.apache.camel.support.BreakpointSupport`.
:::

::: paragraph
The following internal classes have been moved to locations that better
suit where other similar processors are located:
:::

::: paragraph
The class `org.apache.camel.impl.validator.ProcessorValidator` moved to
`org.apache.camel.processor.validator.ProcessorValidator`. The class
`org.apache.camel.impl.transformer.ProcessorTransformer` moved to
`org.apache.camel.processor.transformer.ProcessorTransformer`. The class
`org.apache.camel.impl.transformer.DataFormatTransformer` moved to
`org.apache.camel.processor.transformer.DataFormatTransformer`. The
class `org.apache.camel.impl.validator.ValidatorKey` moved to
`org.apache.camel.impl.engine.ValidatorKey`. The class
`org.apache.camel.impl.transformer.TransformerKey` moved to
`org.apache.camel.impl.engine.TransformerKey`.
:::

::: paragraph
The class `org.apache.camel.impl.DefaultExecutorServiceManager` was
moved from `camel-core-engine` JAR to
`org.apache.camel.impl.engine.DefaultExecutorServiceManager` in the
`camel-base` JAR.
:::

::: paragraph
The class `org.apache.camel.processor.ConvertBodyProcessor` was moved to
`org.apache.camel.support.ConvertBodyProcessor` in the `camel-support`
JAR.
:::

::: paragraph
The class `org.apache.camel.impl.engine.DefaultClaimCheckRepository`
moved to `org.apache.camel.processor.DefaultClaimCheckRepository` in the
`camel-core-processor` JAR.
:::

::: paragraph
The class `org.apache.camel.impl.engine.DefaultProducerCache` was moved
to `org.apache.camel.support.cache.DefaultProducerCache`. The class
`org.apache.camel.impl.engine.DefaultConsumerCache` was moved to
`org.apache.camel.support.cache.DefaultConsumerCache`. The class
`org.apache.camel.impl.engine.EmptyProducerCache` was moved to
`org.apache.camel.support.cache.EmptyProducerCache`. The class
`org.apache.camel.impl.engine.ServicePool` was moved to
`org.apache.camel.support.cache.ServicePool`. The class
`org.apache.camel.impl.engine.ProducerServicePool` was moved to
`org.apache.camel.support.cache.ProducerServicePool`. The class
`org.apache.camel.impl.engine.PollingConsumerServicePool` was moved to
`org.apache.camel.support.cache.PollingConsumerServicePool`. The class
`org.apache.camel.impl.engine.EventNotifierCallback` was moved to
`org.apache.camel.support.cache.EventNotifierCallback`.
:::

::: paragraph
Two methods related to adding and removing error handlers were removed
from the interface `org.apache.camel.spi.LifecycleStrategy`.
:::
:::::::::::

::::::: sect2
### Type Converters {#_type_converters}

::: paragraph
Camel 3.7 has optimized its type converter system, which can impact 3rd
party components which contain type converters. Previously, the type
converter system would attempt to find a type converter that would be
capable of converting between two given types (also by walking up the
parent classes and super interfaces). But this leads to slower
performance, and Camel now relies on there being converter methods with
the exact combo of converting from/to types.
:::

::::: sect3
#### Converting to milliseconds from text {#_converting_to_milliseconds_from_text}

::: paragraph
When converting to milliseconds using the shorthands for time precision
with hours, minutes and seconds, support for fractions was removed. For
example `delay=0.5m` (half minute) is no longer supported, use
`delay=30s` instead.
:::

::: paragraph
Support for using units as `days`, `hours`, `minutes`, `seconds`, and
`millis` has been removed. Units must now be one of `d` for days, `h`
for hours, `m` for minutes, `s` for seconds, and `ms` for millis (can be
omitted). So you can use `1h12m37s42ms` for 1 hour, 12 minutes, 37
seconds and 42 milliseconds.
:::
:::::
:::::::

:::: sect2
### JMX {#_jmx}

::: paragraph
The MBeans for error handlers have been removed.
:::
::::

::::: sect2
### ProcessorFactory {#_processorfactory}

::: paragraph
If a custom `org.apache.camel.spi.ProcessorFactory` is in use, the
factory should extend the default implementation
`org.apache.camel.processor.DefaultProcessorFactory` and in the
overridden methods, super should be called to let the default
implementation create the processor when no custom processors is
created.
:::

::: paragraph
The class `org.apache.camel.impl.engine.TypedProcessorFactor` moved from
`camel-base` JAR to `org.apache.camel.support.TypedProcessorFactor` in
the `camel-support` JAR.
:::
:::::

:::: sect2
### AdviceWith {#_advicewith}

::: paragraph
Advice routes moved the `adviceWith` method from
`org.apache.camel.reifier.RouteReifier` to
`org.apache.camel.builder.AdviceWith`. Also `adviceWith` methods on
`org.apache.camel.builder.AdviceWithRouteBuilder` are deprecated in
favour of using methods on `org.apache.camel.builder.AdviceWith`.
:::
::::

:::: sect2
### toD EIP {#_tod_eip}

::: paragraph
Support for using multiple languages in the toD EIP has been removed as
it was a rarley used feature and was causing problems for maintenance.
`toD` uses simple language by default, but an alternative language can
still be specified.
:::
::::

:::: sect2
### Components {#_components}

::: paragraph
The deprecated option `basicPropertyBinding` has been removed.
:::
::::

:::: sect2
### camel-couchbase {#_camel_couchbase}

::: paragraph
The original URI path has been changed, now the bucket part is simply a
required option and it's not part of the URI anymore.
:::
::::

:::: sect2
### camel-file-watch {#_camel_file_watch}

::: paragraph
The event type header is no longer an enum, but it is now the explicit
event as a `String` value.
:::
::::

:::: sect2
### camel-leveldb {#_camel_leveldb}

::: paragraph
Component uses a different serialization mechanism, which is not
backwards compatible with the original one. For full compatibility, use
the camel-leveldb-legacy component (the legacy component will be removed
in a future release).
:::
::::

:::: sect2
### camel-mock {#_camel_mock}

::: paragraph
The class `InterceptSendToMockEndpointStrategy` in `camel-base` JAR was
moved from
`org.apache.camel.impl.engine.InterceptSendToMockEndpointStrategy` to
`org.apache.camel.component.mock.InterceptSendToMockEndpointStrategy` in
the `camel-mock` JAR.
:::
::::

:::: sect2
### camel-saga {#_camel_saga}

::: paragraph
The class `org.apache.camel.impl.saga.InMemorySagaService` was moved to
`org.apache.camel.saga.InMemorySagaService`. The class
`org.apache.camel.impl.saga.InMemorySagaCoordinator` was moved to
`org.apache.camel.saga.InMemorySagaCoordinator`.
:::
::::

:::: sect2
### camel-management {#_camel_management}

::: paragraph
The `listTypeConverters` operation on
`ManagedTypeConverterRegistryMBean` has been removed.
:::
::::

:::::: sect2
### camel-kafka {#_camel_kafka}

::: paragraph
We changed some option's naming because they were a bit misleading:
:::

::: ulist
- From kafkaHeaderDeserializer to headerDeserializer

- From kafkaHeaderSerializer to headerSerializer

- From keySerializerClass to keySerializer

- From serializerClass to valueSerializer
:::

::: paragraph
For more information, have a look at CAMEL-15770
:::
::::::

::::::::::::::: sect2
### camel-git {#_camel_git}

::: paragraph
The Camel Git Commit consumer has been changed a bit.
:::

::: paragraph
For each exchange now in the body, you'll get the commit full message as
a String and the Commit Object like before.
:::

::: paragraph
Other information has been stored in headers declared in GitConstants
class:
:::

::: ulist
- `GIT_COMMIT_ID` - \"CamelGitCommitId\" - The commit Id

- `GIT_COMMIT_AUTHOR_NAME` - \"CamelGitAuthorName\" - The commit Author
  name

- `GIT_COMMIT_COMMITTER_NAME` - \"CamelGitCommiterName\" - The commit
  committer name

- `GIT_COMMIT_TIME` - \"CamelGitCommitTime\" - The commit time
:::

::: paragraph
The Camel Git Branch consumer has been changed a bit.
:::

::: paragraph
For each exchange now in the body, you'll get the branch ref name and
not the full ref like before.
:::

::: paragraph
Other information has been stored in headers declared in `GitConstants`
class:
:::

::: ulist
- `GIT_BRANCH_LEAF` - \"CamelGitBranchLeaf\" - Leaf

- `GIT_BRANCH_OBJECT_ID` - \"CamelGitBranchObjectId\" - Object Id
:::

::: paragraph
The Camel Git Tag consumer has been changed a bit.
:::

::: paragraph
For each exchange now in the body, you'll get the tag ref name and not
the full ref like before.
:::

::: paragraph
Other information has been stored in headers declared in GitConstants
class:
:::

::: ulist
- `GIT_TAG_LEAF` - \"CamelGitTagLeaf\" - Leaf

- `GIT_TAG_OBJECT_ID` - \"CamelGitTagObjectId\" - Object Id
:::
:::::::::::::::

::::: sect2
### camel-github {#_camel_github}

::: paragraph
Login using username and password is no longer supported by GitHub and
these options have been removed
([https://developer.github.com/changes/2020-02-14-deprecating-password-auth/](https://developer.github.com/changes/2020-02-14-deprecating-password-auth/){.bare}).
:::

::: paragraph
Login must be done using the `oauthToken` option.
:::
:::::

:::: sect2
### Camel-AWS2-S3 Autowire support {#_camel_aws2_s3_autowire_support}

::: paragraph
The camel-aws2-s3 component now has support for autowiring the
`amazonS3Client` option with a `S3Client` instance coming from the
registry. In this context, it wasn't making sense to maintain the
`autodiscoverClient` option, which has been removed.
:::
::::

:::: sect2
### Camel-AWS2-S3 UseIamCredentials {#_camel_aws2_s3_useiamcredentials}

::: paragraph
The option useIamCredentials has been renamed to
`useDefaultCredentialsProvider`, since we changed to a
`DefaultCredentialsProvider` approach.
:::
::::

:::: sect2
### Camel-AWS2-Cloudwatch Autowire support {#_camel_aws2_cloudwatch_autowire_support}

::: paragraph
The camel-aws2-cw component now has support for autowiring the
`amazonCwClient` option with a CloudWatchClient instance coming from the
registry. In this context, it wasn't making sense to maintain the
`autodiscoverClient` option, which has been removed.
:::
::::

::::: sect2
### Camel-AWS2-DDB Autowire support {#_camel_aws2_ddb_autowire_support}

::: paragraph
The camel-aws2-ddb component now has support for autowiring the
`amazonDDBClient` option with a DynamoDbClient instance coming from the
registry. In this context, it wasn't making sense to maintain the
`autodiscoverClient` option, which has been removed.
:::

::: paragraph
The camel-aws2-ddbstreams component now has support for autowiring the
`amazonDynamoDbStreamsClient` option with a DynamoDbStreamsClient
instance coming from the registry. In this context, it wasn't making
sense to maintain the `autodiscoverClient` option, which has been
removed.
:::
:::::

:::: sect2
### Camel-AWS2-EC2 Autowire support {#_camel_aws2_ec2_autowire_support}

::: paragraph
The camel-aws2-ec2 component now has support for autowiring the
`amazonEc2Client` option with an Ec2Client instance coming from the
registry. In this context, it wasn't making sense to maintain the
`autodiscoverClient` option, which has been removed.
:::
::::

:::: sect2
### Camel-AWS2-ECS Autowire support {#_camel_aws2_ecs_autowire_support}

::: paragraph
The camel-aws2-ecs component now has support for autowiring the
`ecsClient` option with an EcsClient instance coming from the registry.
In this context, it wasn't making sense to maintain the
`autodiscoverClient` option, which has been removed.
:::
::::

:::: sect2
### Camel-AWS2-EKS Autowire support {#_camel_aws2_eks_autowire_support}

::: paragraph
The camel-aws2-eks component now has support for autowiring the
`eksClient` option with an EksClient instance coming from the registry.
In this context, it wasn't making sense to maintain the
`autodiscoverClient` option, which has been removed.
:::
::::

:::: sect2
### Camel-AWS2-Eventbridge Autowire support {#_camel_aws2_eventbridge_autowire_support}

::: paragraph
The camel-aws2-eventbridge component now has support for autowiring the
`eventBridgeClient` option with an EventBridgeClient instance coming
from the registry. In this context, it wasn't making sense to maintain
the `autodiscoverClient` option, which has been removed.
:::
::::

:::: sect2
### Camel-AWS2-IAM Autowire support {#_camel_aws2_iam_autowire_support}

::: paragraph
The camel-aws2-iam component now has support for autowiring the
`iamClient` option with an IamClient instance coming from the registry.
In this context, it wasn't making sense to maintain the
`autodiscoverClient` option, which has been removed.
:::
::::

::::: sect2
### Camel-AWS2-Kinesis Autowire support {#_camel_aws2_kinesis_autowire_support}

::: paragraph
The camel-aws2-kinesis component now has support for autowiring the
`amazonKinesisClient` option with a KinesisClient instance coming from
the registry. In this context, it wasn't making sense to maintain the
`autodiscoverClient` option, which has been removed.
:::

::: paragraph
The camel-aws2-kinesis-firehose component now has support for autowiring
the `amazonKinesisFirehoseClient` option with a FirehoseClient instance
coming from the registry. In this context, it wasn't making sense to
maintain the `autodiscoverClient` option, which has been removed.
:::
:::::

:::: sect2
### Camel-AWS2-KMS Autowire support {#_camel_aws2_kms_autowire_support}

::: paragraph
The camel-aws2-kms component now has support for autowiring the
`awsLambdaClient` option with a LambdaClient instance coming from the
registry. In this context, it wasn't making sense to maintain the
`autodiscoverClient` option, which has been removed.
:::
::::

:::: sect2
### Camel-AWS2-Lambda Autowire support {#_camel_aws2_lambda_autowire_support}

::: paragraph
The camel-aws2-kms component now has support for autowiring the
`kmsClient` option with a KmsClient instance coming from the registry.
In this context, it wasn't making sense to maintain the
`autodiscoverClient` option, which has been removed.
:::
::::

:::: sect2
### Camel-AWS2-MQ Autowire support {#_camel_aws2_mq_autowire_support}

::: paragraph
The camel-aws2-mq component now has support for autowiring the
`amazonMqClient` option with a MqClient instance coming from the
registry. In this context, it wasn't making sense to maintain the
`autodiscoverClient` option, which has been removed.
:::
::::

:::: sect2
### Camel-AWS2-MSK Autowire support {#_camel_aws2_msk_autowire_support}

::: paragraph
The camel-aws2-msk component now has support for autowiring the
`mskClient` option with a KafkaClient instance coming from the registry.
In this context, it wasn't making sense to maintain the
`autodiscoverClient` option, which has been removed.
:::
::::

:::: sect2
### Camel-AWS2-SES Autowire support {#_camel_aws2_ses_autowire_support}

::: paragraph
The camel-aws2-ses component now has support for autowiring the
`amazonSESClient` option with a SesClient instance coming from the
registry. In this context, it wasn't making sense to maintain the
`autodiscoverClient` option, which has been removed.
:::
::::

:::: sect2
### Camel-AWS2-SNS Autowire support {#_camel_aws2_sns_autowire_support}

::: paragraph
The camel-aws2-sns component now has support for autowiring the
`amazonSNSClient` option with a SnsClient instance coming from the
registry. In this context, it wasn't making sense to maintain the
`autodiscoverClient` option, which has been removed.
:::
::::

:::: sect2
### Camel-AWS2-SNS UseIamCredentials {#_camel_aws2_sns_useiamcredentials}

::: paragraph
The option useIamCredentials has been renamed to
`useDefaultCredentialsProvider`, since we changed to a
DefaultCredentialsProvider approach.
:::
::::

:::: sect2
### Camel-AWS2-SQS Autowire support {#_camel_aws2_sqs_autowire_support}

::: paragraph
The camel-aws2-sqs component now has support for autowiring the 1
`amazonSQSClient` option with a SqsClient instance coming from the
registry. In this context, it wasn't making sense to maintain the
`autodiscoverClient` option, which has been removed.
:::
::::

:::: sect2
### Camel-AWS2-SQS UseIamCredentials {#_camel_aws2_sqs_useiamcredentials}

::: paragraph
The option useIamCredentials has been renamed to
`useDefaultCredentialsProvider`, since we changed to a
`DefaultCredentialsProvider` approach.
:::
::::

:::: sect2
### Camel-AWS2-STS Autowire support {#_camel_aws2_sts_autowire_support}

::: paragraph
The camel-aws2-sts component now has support for autowiring the
`stsClient` option with a StsClient instance coming from the registry.
In this context, it wasn't making sense to maintain the
`autodiscoverClient` option, which has been removed.
:::
::::

:::: sect2
### Camel-AWS2-Translate Autowire support {#_camel_aws2_translate_autowire_support}

::: paragraph
The camel-aws2-translate component now has support for autowiring the
`translateClient` option with a TranslateClient instance coming from the
registry. In this context, it wasn't making sense to maintain the
`autodiscoverClient` option, which has been removed.
:::
::::

:::: sect2
### Camel-AWS2-Athena Autowire support {#_camel_aws2_athena_autowire_support}

::: paragraph
The camel-aws2-athena component now has support for autowiring the
`amazonAthenaClient` option with an AthenaClient instance coming from
the registry. In this context, it wasn't making sense to maintain the
`autodiscoverClient` option, which has been removed.
:::
::::

:::::: sect2
### camel-salesforce {#_camel_salesforce}

::: paragraph
The default API version for camel-salesforce has been updated to 50.0.
Older versions are still supported and can be set via the `apiVersion`
component option.
:::

::: paragraph
The `packages` option must be set if using the XML `format` option. This
change is a result of adopting XStream's Security Framework.
:::

::: paragraph
CAMEL-15890 fixed a bug in which values for External Ids that contained
spaces would have spaces converted to \"+\". This has been fixed.
However, any such values that now have the plus sign in salesforce will
no longer match as Camel will now preserve the space. Therefore you may
need to have a transformation that explicitly converts spaces to \"+\"
if you need to preserve the old behavior.
:::
::::::

::::: sect2
### camel-google-bigquery {#_camel_google_bigquery}

::: paragraph
The camel-google-bigquery component was updated to use the latest
version of `google-cloud-bigquery`. Some features of
`GoogleBigQueryConnectionFactory` are no longer supported.
:::

::: paragraph
It is no longer possible to provide the service account private key as a
String parameter to `GoogleBigQueryConnectionFactory`. Instead, you
should use `setCredentialsFileLocation` to discover the private key from
your credential's file. Or use the fallback mechanism for discovering
credentials by setting the `GOOGLE_APPLICATION_CREDENTIALS` environment
variable. Refer to the component documentation for more information.
:::
:::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
