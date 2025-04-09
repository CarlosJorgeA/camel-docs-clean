::: {#header}
# Apache Camel 4.x Upgrade Guide
:::

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#content}
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

::::::::::::::: sect1
## Upgrading Camel 4.6 to 4.7 {#_upgrading_camel_4_6_to_4_7}

:::::::::::::: sectionbody
::::: sect2
### API changes {#_api_changes}

::: paragraph
The class `org.apache.camel.impl.engine.ValidatorKey` moved to
`org.apache.camel.spi.ValidatorKey`. The class
`org.apache.camel.impl.engine.TransformerKey` moved to
`org.apache.camel.spi.TransformerKey`.
:::

::: paragraph
The `org.apache.camel.spi.RestRegistry` method `addRestService` has
added `boolean contractFirst` parameter to define if the Rest service is
*contract-first* or *code-first*.
:::
:::::

:::::: sect2
### camel-core {#_camel_core}

::: paragraph
Add default values to `ThrottlingExceptionRoutePolicy` route policy.
:::

::: paragraph
The `EndpointRegistry` interface has been slightly changed to now
directly extends `Map<NormalizedEndpointUri, Endpoint>` instead of being
a parameterized type. This may cause some compilation failures if the
code is declaring a variable for the registry.
:::

::: paragraph
The statistics collector in the registry has been made immutable. As
such, enabling collection of statistics has to be done prior to creating
the type converter registry
:::
::::::

:::::: sect2
### camel-health {#_camel_health}

::: paragraph
Routes that have been set to **not** auto-startup are reported as UP in
health-checks. We have now implemented similar logic for the consumer
health check as well. In this case, the route and consumer health check
are linked together.
:::

::: paragraph
Previously a route that has not been started due to `auto-startup=false`
would still have its consumer health-check being executed, which could
lead to DOWN due to consumer failing the check.
:::

::: paragraph
The `SupervisingRouteController` has now been pre-configured to report
`DOWN` during restart attempts, and also when giving up starting a
route. It is the `UnhealthyOnExhausted` and `UnhealthyOnRestarting`
options that have been changed to be default `true`. To have previous
behavior, you can set these options to `false`.
:::
::::::
::::::::::::::
:::::::::::::::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sect1
## camel-cloud {#_camel_cloud}

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: sectionbody
::: paragraph
The camel-cloud component has been deprecated.
:::

:::: sect2
### camel-crypto {#_camel_crypto}

::: paragraph
PGP dataformat functionality was extracted from `camel-crypto` JAR and
placed into `camel-crypto-pgp`.
:::
::::

::::::::::: sect2
### DSL {#_dsl}

::: paragraph
The WireTap EIP in copy mode, will now do a deep-copy if the message
body is of `StreamCache` type to make access to the message body
thread-safe.
:::

::: paragraph
The Load Balancer EIP has aligned naming and the following balancers has
been renamed in XML and YAML DSL:
:::

+-----------------------------------+-----------------------------------+
| **Old Name**                      | **New Name**                      |
+===================================+===================================+
| failOver                          | failOverLoadBalancer              |
+-----------------------------------+-----------------------------------+
| random                            | randomLoadBalancer                |
+-----------------------------------+-----------------------------------+
| roundRobin                        | roundRobinLoadBalancer            |
+-----------------------------------+-----------------------------------+
| sticky                            | stickyLoadBalancer                |
+-----------------------------------+-----------------------------------+
| topic                             | topicLoadBalancer                 |
+-----------------------------------+-----------------------------------+
| weighted                          | weightedLoadBalancer              |
+-----------------------------------+-----------------------------------+

::: paragraph
For example in XML, you need to change from:
:::

:::: listingblock
::: content
``` highlight
<route>
  <from uri="direct:start"/>
  <loadBalance>
      <failover>
          <exception>java.io.IOException</exception>
      </failover>
      <to uri="direct:x"/>
      <to uri="direct:y"/>
      <to uri="direct:z"/>
  </loadBalance>
</route>
```
:::
::::

::: paragraph
To:
:::

:::: listingblock
::: content
``` highlight
<route>
  <from uri="direct:start"/>
  <loadBalance>
      <failoverLoadBalancer>
          <exception>java.io.IOException</exception>
      </failoverLoadBalancer>
      <to uri="direct:x"/>
      <to uri="direct:y"/>
      <to uri="direct:z"/>
  </loadBalance>
</route>
```
:::
::::
:::::::::::

:::: sect2
### camel-file {#_camel_file}

::: paragraph
When using `idempotent=true` then the file consumer will now run in
eager mode, and `add` the file to the repository before processing, and
call `confirm` when done. Setting `idempotentEager=false` will use the
old behaviour.
:::
::::

::::::: sect2
### camel-jbang {#_camel_jbang}

::: paragraph
The `generate` commands has been moved into separate plugin which you
need to install first to be able to use.
:::

:::: listingblock
::: content
``` highlight
camel plugin add generate
```
:::
::::

::: {.admonitionblock .tip}
+-----------------------------------+-----------------------------------+
| ::: title                         | You can see the list of available |
| Tip                               | plugins using                     |
| :::                               | `camel plugin get --all`.         |
+-----------------------------------+-----------------------------------+
:::
:::::::

:::::::::: sect2
### camel-jetty / camel-servlet / camel-undertow {#_camel_jetty_camel_servlet_camel_undertow}

::: paragraph
When using embedded HTTP server (consumer) then the headers
`CamelHttpServletRequest` and `CamelHttpServletResponse` has been
removed.
:::

::: paragraph
To gain access to these, then you need to use the `HttpMessage` API
instead as shown below:
:::

:::: listingblock
::: content
``` highlight
ServletRequest request = exchange.getIn().getHeader(Exchange.HTTP_SERVLET_REQUEST, ServletRequest.class);
ServletResponse request = exchange.getIn().getHeader(Exchange.HTTP_SERVLET_RESPONSE, ServletResponse.class);
```
:::
::::

::: paragraph
Should be changed to:
:::

:::: listingblock
::: content
``` highlight
ServletRequest request = exchange.getMessage(HttpMessage.class).getRequest();
ServletResponse response = exchange.getMessage(HttpMessage.class).getResponse();
```
:::
::::
::::::::::

:::: sect2
### camel-seda / camel-disruptor {#_camel_seda_camel_disruptor}

::: paragraph
When using `InOnly` exchange pattern then the producer makes a copy of
the message to be added to the queue. Camel will now do a deep-copy if
the message body is of `StreamCache` type to make access to the message
body thread-safe.
:::
::::

::::: sect2
### camel-debug {#_camel_debug}

::: paragraph
The debugger (using `org.apache.camel.spi.BacklogDebugger`) used for
tooling such as IDEA and Visual Studio, is fixed to work better
out-of-the-box by just having `camel-debug` JAR or `camel-debug-starter`
(for Spring Boot) on the classpath.
:::

::: paragraph
An internal change is that the MBean operation
`messageHistoryOnBreakpointAsXml` on `ManagedBacklogDebugger` now
includes the current node as last message history, which was expected by
IDEA tooling, to make it function again.
:::
:::::

::::::::::: sect2
### camel-spring-security {#_camel_spring_security}

::: paragraph
The `camel-spring-security` component has been updated to improve
readiness for Spring Security 7.x. Since Spring Security 5.8 the
`AccessDecisionManager` interface and the related cooperating classes
have been deprecated in favor of `AuthorizationManager` based patterns.
If you are creating Spring Security route policies in your code, you
must now refactor them to be based on an `AuthorizationManager`.
:::

::: paragraph
For example, you might have a route policy defined as follows:
:::

:::: listingblock
::: content
``` highlight
SpringSecurityAuthorizationPolicy authorizationPolicy = new SpringSecurityAuthorizationPolicy();
authorizationPolicy.setAuthenticationManager(authenticationManager);
authorizationPolicy.setSpringSecurityAccessPolicy(new SpringSecurityAccessPolicy("ROLE_ADMIN"));
authorizationPolicy.setAccessDecisionManager(new AffirmativeBased(Collections.singletonList(new RoleVoter())));
```
:::
::::

::: paragraph
With the changes implemented in this release, that must be refactored
to:
:::

:::: listingblock
::: content
``` highlight
SpringSecurityAuthorizationPolicy authorizationPolicy = new SpringSecurityAuthorizationPolicy();
authorizationPolicy.setAuthenticationManager(authenticationManager);
authorizationPolicy.setAuthorizationManager(AuthorityAuthorizationManager.hasRole("ADMIN"));
```
:::
::::

::: paragraph
This new pattern supports a more expressive language to define your own
authorization rules, exposing the full power of the Spring Security
framework to Camel route policies. See the [spring
documentation](https://docs.spring.io/spring-security/reference/5.8/migration/servlet/authorization.html#servlet-replace-permissionevaluator-bean-with-methodsecurityexpression-handler)
for further details on how to migrate your custom code from
`AccessDecisionManager` to `AuthorizationManager`.
:::
:::::::::::

:::: sect2
### camel-cloudevents {#_camel_cloudevents}

::: paragraph
Moved the `camel-cloudevents` api into `camel-api` and removed the
`camel-cloudevents` dependency from all components that provide
CloudEvent transformers.
:::
::::

:::::::: sect2
### camel-hashicorp-vault {#_camel_hashicorp_vault}

::: paragraph
The `HashicorpVaultPropertiesFunction` from the hashicorp vault has been
changed to avoid declaring statically the Vault Engine.
:::

::: paragraph
This means the `camel.vault.hashicorp.engine` property and the support
for `CAMEL_HASHICORP_VAULT_ENGINE` environment variable have been
removed.
:::

::: paragraph
You can now use the following syntax:
:::

::: paragraph
`hashicorp:engine:secret`
:::

::: paragraph
Where engine will be the Hashicorp Vault Engine to be used. This means
you'll be able to use multiple engines at the same time. More details at
CAMEL-20775 issue.
:::
::::::::

:::: sect2
### camel-test {#_camel_test}

::: paragraph
As part of CAMEL-20785, we have started to rework the `CamelTestSupport`
class. At this point, it should be highly compatible with previous
versions, as we are laying down the foundations for greater cleanups in
the future. However, several methods have been marked as deprecated.
Users of this class are advised to look at the deprecation notices and
adjust the code accordingly.
:::
::::

:::: sect2
### camel-pubnub {#_camel_pubnub}

::: paragraph
Upgraded PubNub client from v6 to v9 and the `wherenow` operation is
removed due to no longer present in the client.
:::
::::

::::::::: sect2
### camel-as2 {#_camel_as2}

::: paragraph
The `camel-as2` component has been updated so that the client can
compress a MIME body before signing or compress a MIME body before
signing and encrypting as described in sections
[3.2](https://datatracker.ietf.org/doc/html/rfc5402/#section-3.2) and
[3.5](https://datatracker.ietf.org/doc/html/rfc5402/#section-3.5) of
[rfc 5402](https://datatracker.ietf.org/doc/html/rfc5402/).
:::

::: paragraph
When the AS2 server is configured with a decryption key, all received
messages require encryption. Otherwise, the server will return an
\'insufficient-security\' error disposition. Only messages with valid
encryption will be successfully processed, for instance, \'encrypted\',
\'signed-encrypted\', \'encrypted-compressed\',
\'encrypted-compressed-signed\' and \'encrypted-signed-compressed\'
message types.
:::

::: paragraph
Messages that cannot be successfully decrypted will return a
\'decryption-failed\' error disposition. This includes messages
encrypted with a invalid key or if the server receives encrypted
messages but is not configured with a decryption key.
:::

::: paragraph
When the AS2 server is configured with a message signature validation
certificate chain, all received messages require a signature. Otherwise,
the server will return an \'insufficient-security\' error disposition.
Only messages with a valid signature will be processed, for instance,
\'signed\', \'compressed-signed\', and \'signed-compressed\' message
types.
:::

::: paragraph
The server will return an \'authentication-failure\' error when a
message fails signature validation.
:::

::: paragraph
When the AS2 server is configured with a message signature validation
certificate chain and a decryption key, all received messages require
encryption and a signature. Otherwise, the server will return an
\'insufficient-security\' error disposition. Only messages with a valid
signature and encryption will be processed, for instance,
\'signed-encrypted\', \'encrypted-compressed-signed\', and
\'encrypted-signed-compressed\'.
:::

+-------------+-------------+-------------+-------------+-------------+
| **signing   | no          | yes         | no          | yes         |
| cert**      |             |             |             |             |
+-------------+-------------+-------------+-------------+-------------+
| *           | no          | no          | yes         | yes         |
| *decryption |             |             |             |             |
| key**       |             |             |             |             |
+-------------+-------------+-------------+-------------+-------------+
| **plain**   |             | insufficie  | insufficie  | insufficie  |
|             |             | nt-security | nt-security | nt-security |
+-------------+-------------+-------------+-------------+-------------+
| **signed**  |             |             | insufficie  | insufficie  |
|             |             |             | nt-security | nt-security |
+-------------+-------------+-------------+-------------+-------------+
| **          | decrypt     | decrypt     |             | insufficie  |
| encrypted** | ion-failure | ion-failure |             | nt-security |
+-------------+-------------+-------------+-------------+-------------+
| **signed-   | decrypt     | decrypt     |             |             |
| encrypted** | ion-failure | ion-failure |             |             |
+-------------+-------------+-------------+-------------+-------------+
| **plain-c   |             | insufficie  | insufficie  | insufficie  |
| ompressed** |             | nt-security | nt-security | nt-security |
+-------------+-------------+-------------+-------------+-------------+
| **compress  |             |             | insufficie  | insufficie  |
| ed-signed** |             |             | nt-security | nt-security |
+-------------+-------------+-------------+-------------+-------------+
| **signed-c  |             |             | insufficie  | insufficie  |
| ompressed** |             |             | nt-security | nt-security |
+-------------+-------------+-------------+-------------+-------------+
| **          | decrypt     | decrypt     |             | insufficie  |
| encrypted-c | ion-failure | ion-failure |             | nt-security |
| ompressed** |             |             |             |             |
+-------------+-------------+-------------+-------------+-------------+
| **encrypt   | decrypt     | decrypt     |             |             |
| ed-compress | ion-failure | ion-failure |             |             |
| ed-signed** |             |             |             |             |
+-------------+-------------+-------------+-------------+-------------+
| **encrypt   | decrypt     | decrypt     |             |             |
| ed-signed-c | ion-failure | ion-failure |             |             |
| ompressed** |             |             |             |             |
+-------------+-------------+-------------+-------------+-------------+
:::::::::

:::::: sect2
### Camel Spring Boot {#_camel_spring_boot}

::::: sect3
#### camel-debug-starter {#_camel_debug_starter}

::: paragraph
Using camel debugger with Spring Boot is now moved from
`camel-spring-boot` into `camel-debug-starter` where you can configure
the debugger via `camel.debug.` options in `application.properties`.
:::

::: paragraph
The `camel-debug-starter` now has `camel.debug.enabled=true` by default
to let the debugger be installed out of the box, by having the JAR on
the classpath (as intended). You can turn this off via
`camel.debug.enabled=false`.
:::
:::::
::::::

:::: sect2
### Camel Kotlin deprecation {#_camel_kotlin_deprecation}

::: paragraph
Camel Kotlin DSL is deprecated.
:::
::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
