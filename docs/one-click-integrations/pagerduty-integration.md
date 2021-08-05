# PagerDuty integration

Pulse integrates directly with PagerDuty to receive data about incidents, necessary to calculate the metrics [Median time to recover](../metrics/accelerate.md#median-time-to-recover) and [Change failure rate](../metrics/accelerate.md#change-failure-rate).

To set up the PagerDuty integration:

1.  On Pulse, [expand **Integrations** and select **PagerDuty**](https://app.pulse.codacy.com/integrations/pagerduty){: target="_blank"}.

    ![PagerDuty integration](images/pagerduty.png)

1.  Fill in the necessary details:

    -   **PagerDuty subdomain:** Your custom PagerDuty subdomain. For example, `mysubdomain` if you use the URL `https://mysubdomain.pagerduty.com` to access PagerDuty.
    -   **Pulse system name:** Name of the [system](https://docs.pulse.codacy.com/#before-you-begin) to associate with the incidents reported by this integration.

1.  Follow the instructions on the Pulse UI to create a new webhook on PagerDuty using the provided URL. For example:

    ![PagerDuty webhook](images/pagerduty-webhook.png)

## Collected data

After being configured, the integration, PagerDuty will push data and it will be converted into [Pulse's data model](https://ingestion.pulse.codacy.com/v1/api-docs#tocs_event) to calculate the metrics shown on the UI. In the following table you can see how data from PagerDuty is used to calculate each of the metrics:

| Pulse Event | Source     | Fields                                              | Used in                                          |
| ---------------- | ------------- | --------------------------------------------------- | ------------------------------------------------ |
| Incident       | pagerduty | identifier → id                                    | [Accelerate dashboard](../metrics/accelerate.md) |
|                    |                  | timestampCreated → created_at                              |                                                  |
|                    |                  | timestampResolved → last_status_change_at                             |                                                  |
|                                       |         | system → *defined during the configuration process* |                                                  |
