::: {#header}
# ScheduledRoutePolicy
:::

::::::::::::: {#content}
::::::::: {#preamble}
:::::::: sectionbody
::: paragraph
A scheduled [Route Policy](route-policy.html)
`org.apache.camel.routepolicy.quartz.ScheduledRoutePolicy` is an
extensible abstract policy that is used to provide Camel routes
scheduling capabilities at runtime.
:::

::: paragraph
Scheduling of routes typically involves the following capabilities:
:::

::: ulist
- *Route activation* - Starting a route a given start time if the route
  is in a stopped state awaiting activation.

- *Route de-activation* - Shutting down an otherwise active and started
  route at a given time.

- *Route suspension* - Simply de-activating the route consumer endpoint
  URI declared on the `from(…​)` section of the route from listening on a
  given port. The route is still considered as started, however, clients
  will not be able to send requests along the route.

- *Route resumption* - Resuming the listener on a formerly suspended
  route consumer endpoint URI. This route is ready to accept requests
  following route resumption and client requests will be accepted by the
  route consumer to be forwarded along the route.
:::

::: paragraph
Camel offers two such concrete policies that offer scheduled route
policy support:
:::

::: ulist
- [SimpleScheduledRoutePolicy](simplescheduledroutepolicy.html) - An
  ability to offer route scheduling services using a Simple
  [Quartz](components::quartz-component.html) trigger.

- [CronScheduledRoutePolicy](cronscheduledroutepolicy.html) - An ability
  to offer route scheduling services using a Cron based
  [Quartz](components::quartz-component.html) trigger.
:::
::::::::
:::::::::

::::: sect1
## More Information {#_more_information}

:::: sectionbody
::: paragraph
See [Route Policy](route-policy.html)
:::
::::
:::::
:::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
