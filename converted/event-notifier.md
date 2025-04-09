::: {#header}
# EventNotifier
:::

:::::::::::::::::::::::::::::::::: {#content}
::::: {#preamble}
:::: sectionbody
::: paragraph
The event notifier `org.apache.camel.spi.EventNotifier` is used to get
notified about events within the CamelContext. For example, when a route
is started or stopped. The events are collected independently from the
CamelContext (routes, services, exchanges, and so on). This means that
the notifier doesn't interfere with the rest of Camel, and the events
can be processed independently for logging, data offloading or other
type of actions.
:::
::::
:::::

::::::: sect1
## Type of Events {#_type_of_events}

:::::: sectionbody
::: paragraph
The following CamelEvents are available:
:::

::: ulist
- CamelEvent

  ::: ulist
  - CamelContextEvent

    ::: ulist
    - CamelContextInitializedEvent

    - CamelContextInitializingEvent

    - CamelContextReloadedEvent

    - CamelContextReloadFailureEvent

    - CamelContextReloadingEvent

    - CamelContextResumedEvent

    - CamelContextResumeFailureEvent

    - CamelContextResumingEvent

    - CamelContextRoutesStartedEvent

    - CamelContextRoutesStartingEvent

    - CamelContextRoutesStoppedEvent

    - CamelContextRoutesStoppingEvent

    - CamelContextStartedEvent

    - CamelContextStartingEvent

    - CamelContextStartupFailureEvent

    - CamelContextStopFailureEvent

    - CamelContextStoppedEvent

    - CamelContextStoppingEvent

    - CamelContextSuspendedEvent

    - CamelContextSuspendingEvent
    :::

  - ExchangeEvent

    ::: ulist
    - ExchangeAsyncProcessingStartedEvent

    - ExchangeCompletedEvent

    - ExchangeCreatedEvent

    - ExchangeFailedEvent

    - ExchangeFailureEvent

    - ExchangeFailureHandledEvent

    - ExchangeFailureHandlingEvent

    - ExchangeRedeliveryEvent

    - ExchangeSendingEvent

    - ExchangeSentEvent
    :::

  - FailureEvent

  - RouteEvent

    ::: ulist
    - RouteAddedEvent

    - RouteReloadedEvent

    - RouteRemovedEvent

    - RouteStartedEvent

    - RouteStartingEvent

    - RouteStoppedEvent

    - RouteStoppingEvent
    :::

  - ServiceEvent

    ::: ulist
    - ServiceStartupFailureEvent

    - ServiceStopFailureEvent
    :::

  - StepEvent

    ::: ulist
    - StepCompletedEvent

    - StepFailedEvent

    - StepStartedEvent
    :::
  :::
:::

::: paragraph
See the Javadoc of the `org.apache.camel.spi.CamelEvent` for more
details.
:::
::::::
:::::::

:::::::::: sect1
## Collect Events {#_collect_events}

::::::::: sectionbody
::: paragraph
To collect events a class is needed that extends the
EventNotifierSupport class.
:::

:::: listingblock
::: content
``` highlight
public class MyCollector extends EventNotifierSupport {

    protected Logger log = LoggerFactory.getLogger(getClass());

    @Override
    public void notify(CamelEvent event) throws Exception {

        log.info("Event: " event.getType().name());

    }

}
```
:::
::::

::: paragraph
Then you can enable the notifier:
:::

:::: listingblock
::: content
``` highlight
context.getManagementStrategy().addEventNotifier(new MyCollector());
```
:::
::::
:::::::::
::::::::::

::::::::::: sect1
## Collect Specific Events {#_collect_specific_events}

:::::::::: sectionbody
::: paragraph
By default you get notified by all events. It's however possible to
exclude certain groups of events to get only the group of event you are
interest in.
:::

::: paragraph
In the following example we collect only StepEvents:
:::

:::: listingblock
::: content
``` highlight
public class StepCollector extends EventNotifierSupport {
    protected Logger log = LoggerFactory.getLogger(getClass());

    @Override
    public void notify(CamelEvent event) throws Exception {

        // Cast CamelEvent to StepEvent
        CamelEvent.StepEvent stepEvent = (CamelEvent.StepEvent) event;

        // Get detailed information from the StepEvent
        String eventType = stepEvent.getType().name();
        String stepId = stepEvent.getStepId();
        String body = stepEvent.getExchange().getMessage().getBody(String.class);

        log.info("Event Type:\t\t" + eventType);
        log.info("Step ID:\t\t\t" + stepId);
        log.info("Message Body:\t\t " + body);

    }

}
```
:::
::::

::: paragraph
Then you can enable the notifier for steps by excluding other groups:
:::

:::: listingblock
::: content
``` highlight
//Create eventNotifier that only collects stepEvents
StepCollector stepCollector = new StepCollector();
stepCollector.setIgnoreCamelContextEvents(true);
stepCollector.setIgnoreCamelContextInitEvents(true);
stepCollector.setIgnoreExchangeEvents(true);
stepCollector.setIgnoreRouteEvents(true);
stepCollector.setIgnoreServiceEvents(true);
stepCollector.setIgnoreStepEvents(false);

//Add the Event Notifier to the Camel Context
context.getManagementStrategy().addEventNotifier(stepCollector);
```
:::
::::
::::::::::
:::::::::::

:::::::: sect1
## Event Timestamps {#_event_timestamps}

::::::: sectionbody
::: paragraph
By default, event timestamps are not included and the getTimestamp()
method returns 0.
:::

::: paragraph
Timestamps can be enabled from the CamelContext as follows:
:::

:::: listingblock
::: content
``` highlight
context.getManagementStrategy().getEventFactory().setTimestampEnabled(true);
```
:::
::::
:::::::
::::::::
::::::::::::::::::::::::::::::::::

:::: {#footer}
::: {#footer-text}
Last updated 2025-04-09 11:54:07 +0200
:::
::::
