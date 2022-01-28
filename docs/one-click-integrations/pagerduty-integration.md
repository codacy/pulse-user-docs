# PagerDuty integration

Pulse integrates directly with PagerDuty to receive data about incidents, necessary to calculate the metrics [Median time to recover](../metrics/accelerate.md#median-time-to-recover) and [Change failure rate](../metrics/accelerate.md#change-failure-rate).

## Setting up the PagerDuty integration

To set up the PagerDuty integration:

1.  On Pulse, [expand **Integrations** and select **PagerDuty**](https://app.pulse.codacy.com/integrations/pagerduty){: target="_blank"}.

    ![PagerDuty integration](images/pagerduty.png)

1.  Fill in the necessary details:

    -   **PagerDuty subdomain:** Your custom PagerDuty subdomain. For example, `mysubdomain` if you use the URL `https://mysubdomain.pagerduty.com` to access PagerDuty.
    -   **Pulse system name:** Name of the [system](../cli/cli.md#before-you-begin) to associate with the incidents reported by this integration.

1.  Follow the instructions on the Pulse UI to create a new webhook on PagerDuty using the provided URL. For example:

    ![PagerDuty webhook](images/pagerduty-webhook.png)

## Collected data

The table below lists the data that the PagerDuty integration collects from your PagerDuty webhooks, together with:

-   The mapping between the data collected from PagerDuty and the [Pulse data model](https://ingestion.pulse.codacy.com/v1/api-docs#tocs_event)
-   The metrics that Pulse calculates from the data to display on the dashboards

<table>
<thead>
<tr>
<th><strong>Data collected from PagerDuty</strong></th>
<th><strong>Mapping to Pulse data model</strong></th>
<th><strong>Used in</strong></th>
</tr>
</thead>
<tbody>
<tr>
    <td>Incident</td>
    <td>
        <p>Incident:</p>
        <ul>
            <li><code>identifier</code>: <code>id</code> (incident ID)</li>
            <li><code>timestamp_created</code>: <code>created_at</code> (date when the incident was first triggered)</li>
            <li><code>timestamp_resolved</code>: <code>last_status_change_at</code> (date of the last status change of the incident)</li>
            <li><code>system</code>: <i>defined during the configuration process</i></li>
        </ul>
    </td>
    <td>Median time to recover and Change failure rate on the <a href="../../metrics/accelerate/">Accelerate Overview dashboard</a></td>
</tr>
</table>
