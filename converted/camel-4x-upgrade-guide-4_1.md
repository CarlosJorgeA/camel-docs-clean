::: {#header}
# Apache Camel 4.x Upgrade Guide
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
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

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Upgrading Camel 4.0 to 4.1 {#_upgrading_camel_4_0_to_4_1}

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
:::::: sect2
### camel-api {#_camel_api}

::: paragraph
Added method
`newInstance(Class<T> type, Class<?> factoryClass, String factoryMethod)`
to `org.apache.camel.spi.Injector`.
:::

::: paragraph
Added `isIgnoreLoadingError` and `setIgnoreLoadingError` methods to
`org.apache.camel.spi.RoutesLoader`, and
`org.apache.camel.main.RoutesCollector`.
:::

::: paragraph
Added `getDuration` method to `org.apache.camel.StartupStep`.
:::
::::::

:::::::::::::: sect2
### XML and YAML DSL {#_xml_and_yaml_dsl}

::::::::::::: sect3
#### Creating beans from script {#_creating_beans_from_script}

::: paragraph
In route templates (or kamelets), for advanced use cases, you are able
to create beans from an inlined script.
:::

::: paragraph
The name of the script was defined in `type`, but has been changed to a
new `scriptLanguage` attribute. And `beanType` has been removed as you
must use `type` instead.
:::

::: paragraph
Before:
:::

::::: exampleblock
:::: content
::: dlist

XML

:   :::: listingblock
    ::: content
    ``` highlight
        <bean name="myBean" type="groovy" beanType="com.foo.MyBean">
            <script>
              <!-- groovy code here to create the bean -->
            </script>
        </bean>
    ```
    :::
    ::::

YAML

:   :::: listingblock
    ::: content
    ``` highlight
    - beans:
      - name: "myClient"
        beanType: "com.foo.MyBean"
        type: "groovy"
        script: |
          # groovy script here
    ```
    :::
    ::::
:::
::::
:::::

::: paragraph
After:
:::

::::: exampleblock
:::: content
::: dlist

XML

:   :::: listingblock
    ::: content
    ``` highlight
        <bean name="myBean" type="com.foo.MyBean"
              scriptLanguage="groovy">
            <script>
              <!-- groovy code here to create the bean -->
            </script>
        </bean>
    ```
    :::
    ::::

YAML

:   :::: listingblock
    ::: content
    ``` highlight
    - beans:
      - name: "myClient"
        type: "com.foo.MyBean"
        scriptLanguage: "groovy"
        script: |
          # groovy script here
    ```
    :::
    ::::
:::
::::
:::::
:::::::::::::
::::::::::::::

:::: sect2
### camel-management {#_camel_management}

::: paragraph
Dumping routes to JMX no longer includes `customId="true"` in the XML
nodes.
:::
::::

::::: sect2
### camel-scheduler {#_camel_scheduler}

::: paragraph
The `scheduler` no longer includes header with the timestamp of when the
exchange was fired. This means the exchange by default has no headers,
and `null` body.
:::

::: paragraph
The option `includeMetadata` can be set to `true` on the endpoint or
component level, to turn on these additional metadata headers again.
:::
:::::

:::::: sect2
### camel-timer {#_camel_timer}

::: paragraph
The `timer` no longer includes header `firedTime` with the timestamp of
when the exchange was fired. This means the exchange by default has no
headers, and `null` body.
:::

::: paragraph
The `firedTime` header has been renamed to `CamelTimerFireTime`.
:::

::: paragraph
The option `includeMetadata` can be set to `true` on the endpoint or
component level, to turn on these additional metadata headers again.
:::
::::::

::::::::::: sect2
### camel-aws2-step-functions {#_camel_aws2_step_functions}

::: paragraph
The following Message Headers of `camel-aws2-step-functions` component
have been renamed to follow standard camel naming convention.
:::

+-----------------------------------+-----------------------------------+
| Old Name                          | New Name                          |
+===================================+===================================+
| CamelAwsStateMachineOperation     | CamelAwsStepFunctionsOperation    |
+-----------------------------------+-----------------------------------+
| CamelAwsStateMachinesMaxResults   | CamelAwsSte                       |
|                                   | pFunctionsStateMachinesMaxResults |
+-----------------------------------+-----------------------------------+
| CamelAwsStep                      | CamelAwsStepFunctionsActivityName |
| FunctionsStateMachineActivityName |                                   |
+-----------------------------------+-----------------------------------+
| CamelAwsSte                       | CamelAwsStepFunctionsActivityArn  |
| pFunctionsStateMachineActivityArn |                                   |
+-----------------------------------+-----------------------------------+
| CamelAw                           | CamelAws                          |
| sStateMachineActivitiesMaxResults | StepFunctionsActivitiesMaxResults |
+-----------------------------------+-----------------------------------+
| CamelAwsStateMachineExecutionArn  | CamelAwsStepFunctionsExecutionArn |
+-----------------------------------+-----------------------------------+
| CamelAwsStateMachineExecutionName | C                                 |
|                                   | amelAwsStepFunctionsExecutionName |
+-----------------------------------+-----------------------------------+
| C                                 | Ca                                |
| amelAwsStateMachineExecutionInput | melAwsStepFunctionsExecutionInput |
+-----------------------------------+-----------------------------------+
| CamelAw                           | CamelAws                          |
| sStateMachineExecutionTraceHeader | StepFunctionsExecutionTraceHeader |
+-----------------------------------+-----------------------------------+
| CamelAwsState                     | CamelAwsStepFu                    |
| MachineExecutionHistoryMaxResults | nctionsExecutionHistoryMaxResults |
+-----------------------------------+-----------------------------------+
| CamelAwsStateMachineExe           | CamelAwsStepFunctionsExe          |
| cutionHistoryIncludeExecutionData | cutionHistoryIncludeExecutionData |
+-----------------------------------+-----------------------------------+
| CamelAwsStateMa                   | CamelAwsStepFunc                  |
| chineExecutionHistoryReverseOrder | tionsExecutionHistoryReverseOrder |
+-----------------------------------+-----------------------------------+
| CamelA                            | CamelAw                           |
| wsStateMachineExecutionMaxResults | sStepFunctionsExecutionMaxResults |
+-----------------------------------+-----------------------------------+

::: paragraph
This is applicable only if literal constant headers are used such as
`CamelAwsStateMachinesMaxResults`. If the headers are used from
StepFunctions2Constants Interface like
StepFunctions2Constants.STATE_MACHINES_MAX_RESULTS , then there is no
change;
:::

::: paragraph
For Example before
:::

:::: listingblock
::: content
    from("direct:listActivities")
      .setHeader("CamelAwsStepFunctionsActivitiesMaxResults",5)
       .to("aws2-step-functions://test?awsSfnClient=#awsSfnClient&operation=listActivities")
:::
::::

::: paragraph
Should be changed to
:::

:::: listingblock
::: content
    from("direct:listActivities")
      .setHeader("CamelAwsStepFunctionsActivitiesMaxResults",5)
        .to("aws2-step-functions://test?awsSfnClient=#awsSfnClient&operation=listActivities")
:::
::::
:::::::::::

:::::::::: sect2
### camel-aws2-sns {#_camel_aws2_sns}

::: paragraph
The `queueUrl` parameter has been replaced by the `queueArn` parameter
:::

::: paragraph
For Example before
:::

:::: listingblock
::: content
    from("direct:start")
      .to("aws2-sns://mytopic?subject=mySubject&autoCreateTopic=true&subscribeSNStoSQS=true&queueUrl=https://xxxx")
:::
::::

::: paragraph
Should be changed to
:::

:::: listingblock
::: content
    from("direct:start")
      .to("aws2-sns://mytopic?subject=mySubject&autoCreateTopic=true&subscribeSNStoSQS=true&queueArn=arn:aws:sqs:xxxxx")
:::
::::
::::::::::

:::: sect2
### camel-pdf {#_camel_pdf}

::: paragraph
The Camel-PDF component has been updated to Apache PDFBox 3.0.0, and the
font parameter is now defined through the following enum values:
COURIER,COURIER_BOLD,COURIER_OBLIQUE,COURIER_BOLD_OBLIQUE,
HELVETICA,HELVETICA_BOLD,HELVETICA_OBLIQUE,HELVETICA_BOLD_OBLIQUE,TIMES_ROMAN,TIMES_BOLD,TIMES_ITALIC,TIMES_BOLD_ITALIC,SYMBOL
and ZAPF_DINGBATS
:::
::::

:::::: sect2
### camel-jbang {#_camel_jbang}

::: paragraph
The `pipe` command has been renamed to `script`.
:::

::: paragraph
The `--secrets-refresh` and `--secret-refresh-providers` have been
removed, since they were logically incorrect in the export command
context. More information at CAMEL-19927 issue.
:::

::: paragraph
The generated XML route, created using the command `camel init`, now
uses `<camel>` as the root tag instead of `<routes>`.
:::
::::::

::::::::: sect2
### camel-jetty / camel-servlet / camel-atmosphere-websocket / camel-http-common {#_camel_jetty_camel_servlet_camel_atmosphere_websocket_camel_http_common}

::: paragraph
By default, stack traces will not be included in HTTP responses,
exceptions are muted. Stack traces can be included in HTTP responses by
disabling `muteException`. For example:
:::

:::: listingblock
::: content
    from("jetty:http://localhost:{{port}}/myapp/myservice?muteException=false")
:::
::::

::: paragraph
When exceptions are muted stack traces may be logged by enabling
`logException`. For example
:::

:::: listingblock
::: content
    from("jetty:http://localhost:{{port}}/myapp/myservice?logException=true")
:::
::::
:::::::::

:::: sect2
### YAML DSL {#_yaml_dsl}

::: paragraph
The kebab-case style schema file, `camel-yaml-dsl.json` has been removed
from the distribution in favor of the camelCase style schema file,
`camelYamlDsl.json`. While the Camel runtime stays supporting kebab-case
style also for the moment, it is recommended to migrate to camelCase
style. Any tooling should encourage users to use camelCase style.
:::
::::

:::::::::: sect2
### camel-tracing {#_camel_tracing}

::: paragraph
The `Tag` Enum containing constants for tagging spans has been
deprecated. Instead, use constants from the `TagConstants` Class that
align to Open Telemetry v1.21.0 semantic conventions.
:::

::: paragraph
For example, instead of
:::

:::: listingblock
::: content
    span.setTag(Tag.URL_SCHEME, scheme);
:::
::::

::: paragraph
use
:::

:::: listingblock
::: content
    span.setTag(TagConstants.URL_SCHEME, scheme);
:::
::::
::::::::::

:::: sect2
### camel-kafka {#_camel_kafka}

::: paragraph
The default value for `sessionTimeoutMs` has been updated to `45000` ms,
while the default value for `consumerRequestTimeoutMs` has been updated
to `30000`. More information in CAMEL-19921 issue.
:::
::::

:::: sect2
### camel-core, rest {#_camel_core_rest}

::: paragraph
A new rest configuration option, `enableNoContentResponse`, allows HTTP
204 to be returned with an empty body if a Message contains an empty
JSON object or empty XML root object when set to `true`.
:::
::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
