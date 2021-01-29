# Integrating with Pulse

Pulse displays metrics that provide insights into the current and historic performance of your software delivery process. To calculate these metrics, Pulse must collect information from key events of your particular software development workflow.

Currently, Pulse provides a push-based integration with your workflow using "[one-click integrations](one-click-integrations.md)" and the Pulse CLI.

## Before you begin

Consider the following before integrating your workflow with Pulse:

-   When reporting events to Pulse you should use the field `system` to associate each event with the **most granular unit** that you will use to filter data on the Pulse dashboards, such as by application or service, product, team, or any other entity or group of entities in your organization.

    !!! important
        Typically, the value of `system` should be the name of the CVS repository corresponding to the event.

        However, if you're using a monorepo the value of `system` should be the name of the component in the repository instead.

    Although the field `system` is optional, if you don't report this information you won't be able to filter the data on the Pulse dashboards.

-   In some scenarios, it may not be feasible to use the CLI to send data to Pulse, such as when reporting changes or incidents, or if you are reporting events from providers that only support webhooks. For these situations, you can call an HTTP POST webhook instead. For example:

    ```text
    https://ingestion.pulse.codacy.com/v1/ingestion/<PROVIDER>?api_key=<API KEY>
    ```

    !!! important
        **Please let us know if you are planning on reporting events to Pulse using the webhook** so we can give you more detailed instructions on how to use it.

The next sections include detailed instructions on how to set up the integration with Pulse.

<!-- TODO Check everything below -->

### Using PagerDuty

If you're using PagerDuty as an incident management tool, [use our PagerDuty integration](one-click-integrations.md#pagerduty) to send the necessary incident data to Pulse.
