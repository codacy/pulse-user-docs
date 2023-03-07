# Jira integration

Pulse integrates with Jira Cloud to receive:

-   Data about **incidents**, necessary to calculate the following Accelerate metrics:

    -   [Time to recover](../metrics/accelerate.md#time-to-recover)

    -   [Change failure rate](../metrics/accelerate.md#change-failure-rate)

-   Data bout **issues**, necessary to calculate [Lead time and Cycle time](../metrics/lead-cycle-time.md).

## Setting up the Jira integration

!!! note
    -   The Jira integration must be set up by a **Jira administrator**, as Pulse will only have access to Jira resources that the user setting up the integration has access to.
    -   You can only integrate one Pulse organization with each Jira instance.
    -   [See below](#jira-permissions) the permissions that Pulse requires from your Jira instance.

To set up the Jira integration:

1.  On Pulse, [expand **Integrations** and select **Jira**](https://app.pulse.codacy.com/integrations/jira){: target="_blank"}.

1.  Click **Install Jira App** and follow the instructions on the Jira UI to install the app. You must be a Jira administrator to proceed with this step.

    ![Installing the Pulse Jira app](images/jira-installing.png)

1.  Confirm that Pulse successfully created the integration and the necessary webhooks on Jira. If there was an error please [contact support](mailto:pulsesupport@codacy.com).

<!--TODO Add screenshot for successful installation-->

1.  Choose the strategy to detect incidents that best fits your workflows. See the [section below](#incident-detection-strategy) for a detailed description of each option.

<!--TODO Add screenshot for incident strategy-->

1.  Click **Complete setup**.

Your Jira integration is now complete. Pulse will start loading your data for the last 90 days.

<!--TODO Add screenshot for setup ok-->

## Automatic incident detection strategies {: id="incident-detection-strategy"}

The Pulse Jira integration can detect **incidents** automatically using [issues assigned with the label "Incident"](#jira-incident-label).

You can also choose [not to detect incidents via Jira](#jira-incident-not-detect).

### Use issues assigned with the label "Incident" {: id="jira-incident-label"}

-   <!--TODO Add detection details-->

### Don't detect incidents via Jira {: id="jira-incident-not-detect"}

-   Pulse doesn't detect incidents automatically using Jira events.

    Choose this option if you want to send to Pulse the information about your **incidents** using another Pulse integration - GitHub or Bitbucket integration, [PagerDuty one-click integration](pagerduty-integration.md), [Pulse CLI](../cli/cli.md), or [Ingestion API](https://ingestion.pulse.codacy.com/v1/api-docs) - or if you don't want Pulse to track incidents data.

## Collected data
<!--TODO Update this section-->

The table below lists the data that the Jira integration collects from your Jira instance, together with:

-   The mapping between the data collected from Jira and the [Pulse data model](https://ingestion.pulse.codacy.com/v1/api-docs#tocs_event)
-   The metrics that Pulse displays on the dashboards, calculated using the collected data

<table>
<thead>
<tr>
<th><strong>Data collected from Jira</strong></th>
<th><strong>Details</strong></th>
<th><strong>Used in</strong></th>
</tr>
</thead>
<tbody>
    <tr>
        <td>Issues</td>
        <td>
            Issue data includes all issue status transitions.
        </td>
        <td>Lead time and Cycle time metrics on the <a href="../../metrics/lead-cycle-time/">Lead & Cycle time dashboard</a></td>
    </tr>
    <tr>
        <td>Projects</td>
        <td>
            Project data includes the name of the Jira projects.
        </td>
        <td>Filters on the <a href="../../metrics/lead-cycle-time/">Lead & Cycle time dashboard</a></td>
    </tr>
    <tr>
        <td>Issue types</td>
        <td>
            Pulse groups issue types per name since in Jira you can have the same issue type name in different projects.<br/><br/>
            This means that, for example, the Pulse UI will group different issue types named <strong>Subtask</strong> and <strong>Sub-task</strong> under the name of the issue type that was processed first. For all effects, these issue types are the same for Pulse.
        </td>
        <td>Filters on the <a href="../../metrics/lead-cycle-time/">Lead & Cycle time dashboard</a></td>
    </tr>
</table>

## Which permissions does Pulse need from Jira? {: id="jira-permissions"}

Pulse requests only the necessary permissions from Jira to collect issues data from your Jira instance and [keeps your information secure](https://security.codacy.com/). See below the detailed list of permissions.

<!--TODO Add permissions table-->

## See also

-   [Time to recover](../metrics/accelerate.md#time-to-recover)
-   [Change failure rate](../metrics/accelerate.md#change-failure-rate)
-   [Lead and Cycle time metrics](../metrics/lead-cycle-time.md)
