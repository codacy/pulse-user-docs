# Integrating with Pulse

Pulse displays metrics that provide insights into the current and historic performance of your software delivery process. To calculate these metrics, Pulse must collect information from key events of your particular software development workflow.

Currently, Pulse provides a push-based integration with your workflow using either "one-click integrations" or the Pulse CLI:

-   **[GitHub integration](one-click-integrations.md#github)** (reports changes and deployments)
-   **[PagerDuty integration](one-click-integrations.md#pagerduty)** (reports incidents)
-   **[Pulse CLI](cli/installing-the-pulse-cli.md)** (reports changes, deployments, and incidents)

We recommend that you use the integrations that match your stack because they're faster to set up.

## Before you begin

When reporting events to Pulse you should use the field `system` to associate each event with the **most granular unit** that you will use to filter data on the Pulse dashboards, such as by application or service, product, team, or any other entity or group of entities in your organization.

!!! important
    Typically, the value of `system` should be the name of the CVS repository corresponding to the event.

    However, if you're using a monorepo the value of `system` should be the name of the component in the repository instead.

Although the field `system` is optional, if you don't report this information you won't be able to filter the data on the Pulse dashboards.
