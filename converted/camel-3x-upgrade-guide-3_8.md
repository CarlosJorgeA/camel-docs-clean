::: {#header}
# Apache Camel 3.x Upgrade Guide
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
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

::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## Upgrading Camel 3.7 to 3.8 {#_upgrading_camel_3_7_to_3_8}

:::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::::: sect2
### Route startup procedure change {#_route_startup_procedure_change}

::: paragraph
Camel will now initialize all routes during initialization of
`CamelContext` itself. Before all routes where lazy initialized when
they were started, which happens in the starting phase of
`CamelContext`. By moving this forward to initialization phase, we
ensure all is done together.
:::

::: paragraph
This means that all the resources used in the routes such as EIPs,
processors, beans, components, data formats, languages etc. are also
initialized. An affect of this change is that any custom Camel component
that may do initialization logic in the constructors of `Consumer` or
`Producer` should **not** do this, but move this logic to `doInit` or
`doStart` where such logic belongs.
:::
:::::

::::::::: sect2
### API changes {#_api_changes}

::: paragraph
The following type names are renamed in `CamelEvent.Type` enum:
:::

::: ulist
- `CamelContextRoutesStarting` to `RoutesStarting`

- `CamelContextRoutesStarted` to `RoutesStarted`

- `CamelContextRoutesStopping` to `RoutesStopping`

- `CamelContextRoutesStopped` to `RoutesStopped`
:::

::: paragraph
The method `isOnlyDynamicQueryParameters` was removed from
`org.apache.camel.spi.SendDynamicAware` and
`org.apache.camel.support.component.SendDynamicAwareSupport` classes.
:::

::: paragraph
The class `PackageScanResourceResolver` has been revisited and the
method `findResources` now returns a collection of
`org.apache.camel.spi.Resource` which provide both the location of the
resolved resources and a method to open the related `InpuStream`:
:::

:::: listingblock
::: content
``` highlight
Collection<Resource> findResources(String location) throws Exception;
```
:::
::::
:::::::::

:::: sect2
### OnCompletion EIP {#_oncompletion_eip}

::: paragraph
The `onCompletion` EIP has been fixed. It could trigger multiple
completions for a given `Exchange` before.
:::
::::

::::: sect2
### Transactions and Multicast, Splitter, or Recipient List EIPs {#_transactions_and_multicast_splitter_or_recipient_list_eips}

::: paragraph
When using `transacted` in Camel routes with Multicast, Splitter, or
Recipient List EIPs, the exection strackframe could grown deep and this
could cause a stack overflow exception. This has been fixed by
refactoring the EIP into a special transacted mode and the existing
reactive mode.
:::

::: paragraph
We do not anticipate any issues but if you are using transactions and
these EIPs then we would like to have feedback if you encounter any
problems with upgrading.
:::
:::::

:::::::: sect2
### Configuration changes {#_configuration_changes}

::: paragraph
The configuration for specifying directory for loading XML routes have
been consolidation into a single option (supporting all kinds):
:::

::: paragraph
For Spring Boot users:
:::

::: paragraph
`camel.springboot.xml-routes` to
`camel.springboot.routes-include-pattern`
`camel.springboot.xml-route-templates` to
`camel.springboot.routes-include-pattern` `camel.springboot.xml-rests`
to `camel.springboot.routes-include-pattern`
:::

::: paragraph
For Camel Main users:
:::

::: paragraph
`camel.main.xml-routes` to `camel.springboot.routes-include-pattern`
`camel.main.xml-route-templates` to
`camel.springboot.routes-include-pattern` `camel.main.xml-rests` to
`camel.springboot.routes-include-pattern`
:::
::::::::

:::: sect2
### camel-jackson {#_camel_jackson}

::: paragraph
In the XML DSL `jsonView` has been renamed to `jsonViewTypeName` and
made general available in the model and for the lightweight
`camel-xml-io` route parser.
:::
::::

::::: sect2
### camel-caffeine-lrucache {#_camel_caffeine_lrucache}

::: paragraph
This LRUCache implementation is using an algorithm where elements that
are removed may not be in strict order, and therefore not ideal for LRU
caches assuming ordering.
:::

::: paragraph
The implementation is not needed anymore in Camel 3, as we are using a
simpler default implementation internally. This component was
deprecated, and has been removed as Maven dependency in `camel-core`
pom.xml file.
:::
:::::

:::: sect2
### camel-activemq and camel-jms {#_camel_activemq_and_camel_jms}

::: paragraph
The JMS and ActiveMQ components now support the optimized toD EIP
pattern by using a single endpoint/producer for dynamic destination
names.
:::
::::

::::: sect2
### camel-sjms and camel-sjms2 {#_camel_sjms_and_camel_sjms2}

::: paragraph
These two components have been overhauled and re-written with the goal
of being more feature complete with the Spring JMS component. They no
longer uses their own connection pooling, but let you use the existing
3rd party pooling for `ConnectionFactory` which is common practice. The
components are now reactive and non-blocking, and support the optimized
toD EIP pattern by using a single endpoint/producer for dynamic
destination names.
:::

::: paragraph
Many of the previous features and configuration options have been
removed/renamed. The sjms-batch sub component has been removed and is
scheduled to be reimplemented in the future. To migrate you need to read
their documentation and see what options they now offer.
:::
:::::

:::: sect2
### camel-aws2-sns {#_camel_aws2_sns}

::: paragraph
The policy option now expects a file, since the policy is going to be
complex. It can be from classpath:, http: or file: etc.
:::
::::

:::: sect2
### camel-aws2-sqs {#_camel_aws2_sqs}

::: paragraph
The policy option now expects a file, since the policy is going to be
complex. It can be from classpath:, http: or file: etc.
:::
::::

::::::::::: sect2
### camel-github {#_camel_github}

::: paragraph
The Camel Github Commit consumer has been changed a bit.
:::

::: paragraph
For each exchange now in the body you'll get the commit full message as
a String and not the Commit Object like before.
:::

::: paragraph
Other information has been stored in headers declared in
`GitHubConstants` class:
:::

::: ulist
- GITHUB_COMMIT_AUTHOR - `CamelGitHubCommitAuthor` - The commit Author

- GITHUB_COMMIT_COMMITTER - `CamelGitHubCommitCommitter` - The committer
  name

- GITHUB_COMMIT_SHA - `CamelGitHubCommitSha` - The commit sha

- GITHUB_COMMIT_URL - `CamelGitHubCommitUrl` - The commit url
:::

::: paragraph
The Camel Github Events consumer has been changed a bit.
:::

::: paragraph
For each exchange now in the body you'll get the event type as a String
and not the Event Object like before.
:::

::: paragraph
Other information has been stored in headers declared in GitHubConstants
class:
:::

::: ulist
- GITHUB_EVENT_PAYLOAD - `CamelGitHubEventPayload` - The event payload
:::
:::::::::::

::::::: sect2
### camel-infinispan {#_camel_infinispan}

::: paragraph
There are now two components for Infinispan:
:::

::: ulist
- **camel-infinispan** to integrate with remote caches through the Hot
  Rod protocol (scheme: **infinispan**).

- **camel-infinispan-embedded** to integrate with local/embedded caches
  (scheme: **infinispan-embedded**).
:::

::: paragraph
As consequence of the refactor:
:::

::: paragraph
The remote and embedded endpoints provide support the same capabilities,
as example queries were only possible on a remote cache and now they are
supported on both remote and local/embedded caches. The configuration
options for the endpoint are now specific to the context which remove
the possibility to mix unrelated properties. Some classes have been
relocated (such as indempotent and aggregation repositories) have been
moved from `org.apache.camel.component.infinispan.processor.*` to
`org.apache.camel.component.infinispan.embedded` or
`org.apache.camel.component.infinispan.remote`: -
`org.apache.camel.component.infinispan.embedded.InfinispanEmbeddedAggregationRepository` -
`org.apache.camel.component.infinispan.embedded.InfinispanEmbeddedIdempotentRepository` -
`org.apache.camel.component.infinispan.remote.InfinispanRemoteAggregationRepository` -
`org.apache.camel.component.infinispan.remote.InfinispanRemoteIdempotentRepository`
:::
:::::::

:::: sect2
### camel-aws {#_camel_aws}

::: paragraph
All the camel-aws components except camel-aws-xray have been deprecated.
We suggest migrating to camel-aws2-\* components, because in future
releases the AWS components will be removed and with the next LTS
release (3.10 probably) camel-aws2 components will be renamed to
camel-aws.
:::
::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
