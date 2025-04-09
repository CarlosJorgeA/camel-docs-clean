::: {#header}
# Apache Camel 3.x Upgrade Guide
:::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
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

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Upgrading Camel 3.15 to 3.16 {#_upgrading_camel_3_15_to_3_16}

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
:::::: sect2
### camel-core {#_camel_core}

::: paragraph
Change the default from `Override` to `Ignore` in the
`TypeConverterExists` option, and the TypeConverterExistsLoggingLevel
from `WARN` to `DEBUG`.
:::

::: paragraph
Previously when Camel detected a duplicate type converter during
startup, Camel would override the existing converter and log a WARN. A
more correct behaviour would be to keep the existing and ignore the
duplicate.
:::

::: paragraph
Removed the deprecated class `org.apache.camel.impl.RouteIdFactory`.
:::
::::::

::::::::::::::::::::::::::::::::::::: sect2
### Rest DSL {#_rest_dsl}

::: paragraph
The Rest DSL have some changes.
:::

:::::::::::::::: sect3
#### Removed support for embedded routes {#_removed_support_for_embedded_routes}

::: paragraph
The Rest DSL no longer allows embedding routes directly into each REST
service. Instead, you must use `to` to route to a Camel endpoint. If you
have an embedded route you can them make this into an individual route
using `direct` as the input endpoint, and then link to this route from
the REST service.
:::

::: paragraph
For example
:::

:::: listingblock
::: content
``` highlight
rest("/users")
  .get("/{id}")
  .route().
    // embedded route here
```
:::
::::

::: paragraph
Should now be:
:::

:::: listingblock
::: content
``` highlight
rest("/users")
  .get("/{id}")
  .to("direct:users-by-id");

from("direct:users-by-id")
    // embedded route here
```
:::
::::

::: paragraph
And in XML:
:::

:::: listingblock
::: content
``` highlight
<rest path="/users">
  <get path="/{id}">
    <route>
      ...
   </route>
  </get>
</rest>
```
:::
::::

::: paragraph
Should now be:
:::

:::: listingblock
::: content
``` highlight
<rest path="/users">
  <get path="/{id}">
    <to uri="direct:users-by-id"/>
  </get>
</rest>

<route>
  <from uri="direct:users-by-id"/>
  ...
</route>
```
:::
::::
::::::::::::::::

::::::::::::::: sect3
#### Renamed uri to path {#_renamed_uri_to_path}

::: paragraph
Rename `uri` to `path` on the verb classes listed above. When using XML
or YAML DSL then migration is needed such as:
:::

:::: listingblock
::: content
``` highlight
<rest>
  <get uri="/hello/{name}">
    ...
  </get>
</rest>
```
:::
::::

::: paragraph
Should be:
:::

:::: listingblock
::: content
``` highlight
<rest>
  <get path="/hello/{name}">
    ...
  </get>
</rest>
```
:::
::::

::: paragraph
And in YAML DSL:
:::

:::: listingblock
::: content
``` highlight
rest:
  get:
    - uri: "/hello/{name}"
      to: "direct:hello"
```
:::
::::

::: paragraph
Should be:
:::

:::: listingblock
::: content
``` highlight
rest:
  get:
    - path: "/hello/{name}"
      to: "direct:hello"
```
:::
::::
:::::::::::::::

:::::: sect3
#### Renamed classes {#_renamed_classes}

::: paragraph
The generic verb-based definition, where the HTTP verb can be specified
as a string value is no longer supported for defining a REST service.
Only the built-in verbs (get, post, put etc.) must be used.
:::

::: paragraph
Renamed the following classes in package `org.apache.camel.model.rest`:
:::

::: ulist
- `DeleteVerbDefinition` to `DeleteDefinition`

- `GetVerbDefinition` to `GetDefinition`

- `HeadVerbDefinition` to `HeadDefinition`

- `PatchVerbDefinition` to `PatchDefinition`

- `PostVerbDefinition` to `PostDefinition`

- `PutVerbDefinition` to `PutDefinition`

- `RestSecurityApiKey` to `ApiKeyDefinition`

- `RestSecurityBasicAuth` to `BasicAuthDefinition`

- `RestSecurityBearerToken` to `BearerTokenDefinition`

- `RestSecurityMultalTLS` to `MutalTLSDefinition`

- `RestSecurityOAuth2` to `OAuth2Definition`

- `RestSecurityOpenIdConnect` to `OpenIdConnectDefinition`

- `RestOperationParamDefinition` to `ParamDefinition`

- `RestOperationResponseHeaderDefinition` to `ResponseHeaderDefinition`

- `RestOperationResponseMsgDefinition` to `ResponseMessageDefinition`
:::
::::::

:::: sect3
#### Removed classes {#_removed_classes}

::: paragraph
For the sake of simplicity, the class `RestSecuritiesRequirement` has
been removed because it contains only elements of type
`SecurityDefinition` which have been moved directly into
`RestDefinition`.
:::
::::
:::::::::::::::::::::::::::::::::::::

:::: sect2
### Aggregate EIP {#_aggregate_eip}

::: paragraph
Renamed `strategyRef` to `aggregationStrategy`, and marked this option
as required. Renamed `strategyMethodName` to
`aggregationStrategyMethodName`. Renamed `strategyMethodAllowNull` to
`aggregationStrategyMethodAllowNull`. Renamed `aggregationRepositoryRef`
to `aggregationRepository`. Renamed `aggregateControllerRef` to
`aggregateController`. Renamed `executorServiceRef` to
`executorService`. Renamed `timeoutCheckerExecutorServiceRef` to
`timeoutCheckerExecutorService`. Removed some deprecated methods, and
some unnecessary methods in Java DSL.
:::
::::

:::: sect2
### Circuit Breaker EIP {#_circuit_breaker_eip}

::: paragraph
Renamed `circuitBreakerRef` to `circuitBreaker`. Renamed `configRef` to
`config`. Renamed `bulkheadExecutorServiceRef` to
`bulkheadExecutorService`. Renamed `timeoutScheduledExecutorServiceRef`
to `timeoutScheduledExecutorService`.
:::
::::

:::: sect2
### Claim Check EIP {#_claim_check_eip}

::: paragraph
Renamed `strategyRef` to `aggregationStrategy`, and marked this option
as required. Renamed `strategyMethodName` to
`aggregationStrategyMethodName`. Removed some unnecessary methods in
Java DSL.
:::
::::

:::: sect2
### Delay EIP {#_delay_eip}

::: paragraph
Renamed `executorServiceRef` to `executorService`.
:::
::::

:::::::::: sect2
### DoSwitch EIP {#_doswitch_eip}

::: paragraph
Replaced by Choice EIP in precondition mode.
:::

::: paragraph
Before it was:
:::

:::: listingblock
::: content
``` highlight
.doSwitch()
    .when(simple("{{?red}}")).to("mock:red")
    .when(simple("{{?blue}}")).to("mock:blue")
.end()
```
:::
::::

::: paragraph
Now it is:
:::

:::: listingblock
::: content
``` highlight
.choice().precondition()
    .when(simple("{{?red}}")).to("mock:red")
    .when(simple("{{?blue}}")).to("mock:blue")
.end()
```
:::
::::
::::::::::

:::: sect2
### Enrich & Poll Enrich EIPs {#_enrich_poll_enrich_eips}

::: paragraph
Renamed `strategyRef` to `aggregationStrategy`, and marked this option
as required. Renamed `strategyMethodName` to
`aggregationStrategyMethodName`. Renamed `strategyMethodAllowNull` to
`aggregationStrategyMethodAllowNull`. Removed some deprecated methods,
and some unnecessary methods in Java DSL.
:::
::::

:::: sect2
### Idempotent Consumer EIP {#_idempotent_consumer_eip}

::: paragraph
Renamed `messageIdRepositoryRef` to `idempotentRepository`. Removed some
unnecessary methods in Java DSL.
:::
::::

:::: sect2
### Log EIP {#_log_eip}

::: paragraph
Renamed `loggerRef` to `logger`.
:::
::::

:::: sect2
### Multicast, Recipient List & Split EIP {#_multicast_recipient_list_split_eip}

::: paragraph
Renamed `strategyRef` to `aggregationStrategy`. Renamed
`strategyMethodName` to `aggregationStrategyMethodName`. Renamed
`strategyMethodAllowNull` to `aggregationStrategyMethodAllowNull`.
Renamed `onPrepareRef` to `onPrepare`. Renamed `executorServiceRef` to
`executorService`. Removed some deprecated methods, and some unnecessary
methods in Java DSL.
:::
::::

:::: sect2
### OnCompletion EIP {#_oncompletion_eip}

::: paragraph
Renamed `executorServiceRef` to `executorService`.
:::
::::

:::: sect2
### Resequence EIP {#_resequence_eip}

::: paragraph
Renamed `comparatorRef` to `comparator`.
:::
::::

:::: sect2
### Sort EIP {#_sort_eip}

::: paragraph
Renamed `comparatorRef` to `comparator`.
:::
::::

:::: sect2
### Threads EIP {#_threads_eip}

::: paragraph
Renamed `executorServiceRef` to `executorService`.
:::
::::

:::: sect2
### Throttle EIP {#_throttle_eip}

::: paragraph
Renamed `executorServiceRef` to `executorService`.
:::
::::

:::::::::: sect2
### Saga EIP {#_saga_eip}

::: paragraph
Renamed `sagaServiceRef` to `sagaService`. Removed the deprecated
`timeoutInMilliseconds` option, use `timeout` instead.
:::

::: paragraph
In the `<option>` the `optionName` is renamed to `key`. When using XML
DSL then this is affected as follows:
:::

:::: listingblock
::: content
``` highlight
<saga sagaServiceRef="mySagaService">
    <compensation uri="mock:compensation"/>
    <completion uri="mock:completion"/>
    <option optionName="myOptionKey">
        <constant>myOptionValue</constant>
    </option>
    <option optionName="myOptionKey2">
        <constant>myOptionValue2</constant>
    </option>
</saga>
```
:::
::::

::: paragraph
To:
:::

:::: listingblock
::: content
``` highlight
<saga sagaServiceRef="mySagaService">
    <compensation uri="mock:compensation"/>
    <completion uri="mock:completion"/>
    <option key="myOptionKey">
        <constant>myOptionValue</constant>
    </option>
    <option key="myOptionKey2">
        <constant>myOptionValue2</constant>
    </option>
</saga>
```
:::
::::
::::::::::

:::: sect2
### WireTap EIP {#_wiretap_eip}

::: paragraph
Renamed `onPrepareRef` to `onPrepare`. Renamed `executorServiceRef` to
`executorService`. Removed the *new message* mode as this functionality
is better done by using onPrepare processor in copy mode.
:::
::::

:::::::::::::::::: sect2
### camel-health {#_camel_health}

::: paragraph
The `HealthCheck` API has been simplified and removed the following
configurations:
:::

::: ulist
- interval

- success threshold

- failure threshold
:::

::: paragraph
These options would complicate health checks as they affect the outcome
of health checks. It is better the checks always execute and the
responsibility of the monitoring systems how to deal with interval
between checks and thresholds.
:::

::: paragraph
Removed the option to disable context health check as it should always
be enabled.
:::

::::::::::::: sect3
#### Disabling health checks {#_disabling_health_checks}

::: paragraph
The configuration for disabling individual health-checks has changed
:::

::: paragraph
Before each health-check could be configured and set `enabled=false`.
For example to disable health-checks for route with id `netty` you would
do:
:::

:::: listingblock
::: content
``` highlight
camel.health.config[netty].check = routes
camel.health.config[netty].enabled = false
```
:::
::::

::: paragraph
With Camel 3.16 onwards you instead specify pattern(s) for health checks
to be excluded from being invoked, which is done in a single
configuration:
:::

:::: listingblock
::: content
``` highlight
camel.health.exclude-pattern = netty
```
:::
::::

::: paragraph
You can specify multiple patterns (and use wildcards) such as:
:::

:::: listingblock
::: content
``` highlight
camel.health.exclude-pattern = netty,foo,bar*
```
:::
::::
:::::::::::::
::::::::::::::::::

::::: sect2
### camel-main {#_camel_main}

::: paragraph
The option `camel.main.packageScanRouteBuilders` has been renamed to
`camel.main.basePackageScan`.
:::

::: paragraph
Using configuration classes must now implement the interface
`org.apache.camel.main.CamelConfiguration` and the `configure` method
now takes a `CamelContext` as argument.
:::
:::::

:::: sect2
### camel-any23 {#_camel_any23}

::: paragraph
The option `baseURI` is renamed to `baseUri`.
:::
::::

:::: sect2
### camel-aws2-kinesis {#_camel_aws2_kinesis}

::: paragraph
The `camel-aws2-kinesis` component will now set only the raw data as
body while consuming from a stream. The data will be an input stream.
This changes the behavior from the old versions because in the past the
component was returning the full record as part of the body.
:::
::::

:::: sect2
### camel-aws2-sqs {#_camel_aws2_sqs}

::: paragraph
The `camel-aws2-sqs` component will now map message headers from their
original type such as boolean, integer, etc. This requires using Camel
for both sending and receiving as AWS only have string or binary types,
so Camel stores custom metadata in the message header to know its
original type.
:::
::::

:::: sect2
### camel-stream {#_camel_stream}

::: paragraph
The producer will now by default append new line character to end of
output. The option `appendNewLine` can be used to turn this off.
:::
::::

:::: sect2
### camel-testcontainers {#_camel_testcontainers}

::: paragraph
This component was deprecated and is removed on this version. Users
should migrate to [camel-test-infra](test-infra.html).
:::
::::

:::: sect2
### camel-testcontainers-junit5 {#_camel_testcontainers_junit5}

::: paragraph
This component was deprecated and is removed on this version. Users
should migrate to [camel-test-infra](test-infra.html).
:::
::::

:::: sect2
### camel-testcontainers-spring {#_camel_testcontainers_spring}

::: paragraph
This component was deprecated and is removed on this version. Users
should migrate to [camel-test-infra](test-infra.html).
:::
::::

:::: sect2
### camel-testcontainers-spring-junit5 {#_camel_testcontainers_spring_junit5}

::: paragraph
This component was deprecated and is removed on this version. Users
should migrate to [camel-test-infra](test-infra.html).
:::
::::

:::: sect2
### camel-salesforce {#_camel_salesforce}

::: paragraph
The `sObjectName` query parameter and header now take precedence over
the class name of the `AbstractSObjectBase` DTO for determining the name
of the SObject. We suggest testing your existing routes to identify any
potential issues. See
[https://issues.apache.org/jira/browse/CAMEL-17608](https://issues.apache.org/jira/browse/CAMEL-17608){.bare}
for more details.
:::
::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
