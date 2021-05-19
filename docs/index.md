# Integrating with Pulse

Pulse displays metrics that provide insights into the current and historic performance of your software delivery process. To calculate these metrics, Pulse must collect information from key events of your particular software development workflow.

Currently, Pulse provides a push-based integration with your workflow using either "one-click integrations" or the Pulse CLI:

-   **[GitHub integration](one-click-integrations.md#github)** (reports changes and deployments)
-   **[PagerDuty integration](one-click-integrations.md#pagerduty)** (reports incidents)
-   **[Pulse CLI](cli/installing-the-pulse-cli.md)** (reports changes, deployments, and incidents)

We recommend that you use the integrations that match your stack because they're faster to set up.

## Creating an organization

You must create your own organization on Pulse to start the onboarding. Click the link in the welcome banner at the top of the page and follow the instructions.

![Adding a new organization on Pulse](images/organization-add.png)

To add more organizations, click the name of the current organization on the top left-hand corner and select **New Organization**.
