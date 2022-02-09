# Jira integration

Pulse integrates with Jira Cloud to receive data about issues, necessary to calculate the metrics [Lead time and Cycle time](../metrics/lead-cycle-time.md).

## Setting up the Jira integration

To set up the Jira integration:

1.  On Pulse, [expand **Integrations** and select **Jira**](https://app.pulse.codacy.com/integrations/jira){: target="_blank"}.

1.  Click **Install Jira** and follow the instructions on the Jira UI to install the app.

    ![Installing the Pulse Jira app](images/jira-installing.png)

1.  Wait until you get a confirmation that Pulse successfully created the integration and the necessary webhooks on Jira.

    If there was an error please [contact support](mailto:pulsesupport@codacy.com).

## Collected data

The table below lists the data that the Jira integration collects from your Jira issues, together with the metrics that Pulse calculates from the data to display on the dashboards.

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
            Issue data includes all issue status transitions.<br/><br/>
            When calculating Cycle time, Pulse considers that:
            <ul>
                <li>Issues are in progress when they transition to any Jira status belonging to the <strong>In Progress</strong> status category (represented by the blue color in Jira).</li>
                <li>Issues that have a status belonging to the <strong>To Do</strong> status category (represented by the grey color in Jira) don't contribute to Cycle time.</li>
            </ul>
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
