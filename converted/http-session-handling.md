::: {#header}
# HTTP Session Handling
:::

:::::::::::::::::::::::::::::::::::::: {#content}
::::: {#preamble}
:::: sectionbody
::: paragraph
Several Camel components can use HTTP as the underlying transport
protocol. In general HTTP calls are stateless in nature, however some
servers allow maintaining state via cookies. Cookies are often used to
maintain a server session (e.g. via a session cookie called
\"JSESSIONID\" with servers implementing the JEE Servlet specification).
:::
::::
:::::

::::::::::::: sect1
## Session Scope {#_session_scope}

:::::::::::: sectionbody
::: paragraph
If a Camel route intends to implement some kind of HTTP session handling
the scope of this session should be considered.
:::

::: paragraph
Independently of the session scope the implementation must honor the
domain of the handled cookies.
:::

:::: sect2
### Route/Context Scope {#_routecontext_scope}

::: paragraph
It might be desirable to have a single session for a route or a
CamelContext. This essentially means that all calls to a server issued
from a route or CamelContext share a single HTTP session.
:::
::::

:::: sect2
### Endpoint Scope {#_endpoint_scope}

::: paragraph
It is also possible to have a session on an Endpoint entity. This would
mean that all invocations of an HTTP call issued by a single Endpoint
share a session, whereas different Endpoints never share sessions, even
if the call is sent to the same server.
:::
::::

::::: sect2
### Exchange Scope {#_exchange_scope}

::: paragraph
The third option to define a session scope is on Exchange level. This is
particularly useful for scenarios where the server session is really
maintaining state.
:::

::: paragraph
In this case the route could e.g. first do a login call, then some
update calls and finally a logout call. If the session handling would be
defined on route or CamelContext scopes this would seem to run, however
under load parallel invocations of the route would share a **single**
session, which could cause issues. If the session is defined on exchange
scope, each invocation of the route will get a separate session, and the
server can maintain a separate state for the different parallel
invocations.
:::
:::::
::::::::::::
:::::::::::::

::::::::: sect1
## Usage {#_usage}

:::::::: sectionbody
::: paragraph
If you are a Camel user, you see that several Camel components support
the cookieHandler parameter on endpoint level. All you need to do is to
instantiate a cookie handler appropriate for your use case and reference
it in the cookieHandler parameter for all endpoints that are supposed to
participate in the HTTP session.
:::

::: paragraph
There are two pre-implemented cookie handlers:
:::

::: ulist
- `org.apache.camel.http.common.cookie.InstanceCookieHandler`

- `org.apache.camel.http.common.cookie.ExchangeCookieHandler`
:::

::: paragraph
The `InstanceCookieHandler` stores cookies in an instance of itself. You
can compare that to a browser instance that is shared between all the
endpoints that use it (and will be used for all invocations of these
endpoints). If you want to maintain separate sessions for different
endpoints or groups of endpoints you may have multiple instances of the
`InstanceCookieHandler`.
:::

::: paragraph
The `ExchangeCookieHandler` stores the session in the exchange. With the
browser analogy this means that each Exchange will get its own browser
instance (so sessions are separated). As the `ExchangeCookieHandler`
does not store any state it is generally not useful to have multiple
`ExchangeCookieHandler` instances (as they would access the same data,
anyway).
:::
::::::::
:::::::::

:::::::: sect1
## Example {#_example}

::::::: sectionbody
::: paragraph
The following three routes will each do two invocations of an echo REST
service. In the first route (without a cookie handler) each invocation
will get a new session. For the second route all invocations will share
a session. For the third route the first, and the second invocation
within the route share a session, but different (even parallel)
invocations of the route will not share a session.
:::

:::: listingblock
::: content
``` highlight
  <cxf:rsClient id="rsClientProxy" address="http://127.0.0.1:8080/CxfRsProducerSessionTest/"
    serviceClass="org.apache.camel.component.cxf.jaxrs.testbean.EchoService"
    loggingFeatureEnabled="true" />

  <camelContext id="camel" xmlns="http://camel.apache.org/schema/spring">
    <route>
       <from uri="direct://proxy"/>
       <to uri="cxfrs://bean://rsClientProxy"/>
       <convertBodyTo type="java.lang.String"/>
       <to uri="cxfrs://bean://rsClientProxy"/>
    </route>
    <route>
       <from uri="direct://proxyinstance"/>
       <to uri="cxfrs://bean://rsClientProxy?cookieHandler=#instanceCookieHandler"/>
       <convertBodyTo type="java.lang.String"/>
       <to uri="cxfrs://bean://rsClientProxy?cookieHandler=#instanceCookieHandler"/>
    </route>
    <route>
       <from uri="direct://proxyexchange"/>
       <to uri="cxfrs://bean://rsClientProxy?cookieHandler=#exchangeCookieHandler"/>
       <convertBodyTo type="java.lang.String"/>
       <to uri="cxfrs://bean://rsClientProxy?cookieHandler=#exchangeCookieHandler"/>
    </route>
  </camelContext>

  <bean id="instanceCookieHandler" class="org.apache.camel.http.common.cookie.InstanceCookieHandler"/>
  <bean id="exchangeCookieHandler" class="org.apache.camel.http.common.cookie.ExchangeCookieHandler"/>
```
:::
::::

::: paragraph
Both `CookieHandler` implementations support setting a CookiePolicy to
control the policy for storing cookies. Default is
`CookiePolicy.ACCEPT_ORIGINAL_SERVER`.
:::
:::::::
::::::::

::::: sect1
## Cookies and EIPs {#_cookies_and_eips}

:::: sectionbody
::: paragraph
Some EIPs like [Multicast](components:eips:multicast-eip.html) or
[Split](components:eips:split-eip.html) create multiple exchanges from a
single one. If no
`org.apache.camel.http.common.cookie.ExchangeCookieHandler` is used
before this, each multicast or splitter branch will have its own cookie
store. This will not be the case if the first invocation of one of the
endpoints using the cookie handler is before the multicast, because in
this case the cookie store will be attached to the original exchange,
and the exchanges created by the multicast will copy the reference to
this cookie store so there is effectively a shared cookie store across
branches. As a workaround, you can call a
`cookieHandler.getCookieStore()` e.g. by setting this to some dummy
header.
:::
::::
:::::

::::::: sect1
## Component Developers {#_component_developers}

:::::: sectionbody
::: paragraph
If you want to develop a HTTP based component that is supposed to
participate in a session you have to add the following parts to your
code:
:::

::: {.olist .arabic}
1.  Include a build reference to camel-http-common (if it is not already
    there)

2.  Add a cookieHandler parameter to the endpoint class (together with
    getter and setter)

3.  Before your code does the HTTP call, if a cookie handler is set on
    the endpoint perform a `cookieHandler.loadCookies(exchange, uri)`
    call. It will return a `Map<String, List<String>>` containing the
    headers that need to be sent to the server. The details how you need
    to send these headers to the server depend on the underlying HTTP
    API you are using.

4.  After your code does receive the HTTP response if a cookie handler
    is set on the endpoint perform a
    `cookieHandler.storeCookies(exchange, uri, m)` call. `m` is a
    `Map<String, List<String>>` containing the HTTP headers returned
    from the server.
:::

::: {.admonitionblock .note}
+-----------------------------------+-----------------------------------+
| ::: title                         | Some APIs provide more direct     |
| Note                              | support for cookie handling. In   |
| :::                               | this case it might be easier to   |
|                                   | get the underlying                |
|                                   | `java.net.CookieStore` with a     |
|                                   | `cooke                            |
|                                   | Manager.getCookieStore(exchange)` |
|                                   | call and handle the cookies using |
|                                   | the cookie interface provided by  |
|                                   | the underlying library.           |
+-----------------------------------+-----------------------------------+
:::
::::::
:::::::
::::::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
