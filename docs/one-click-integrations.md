# One-click integrations

Pulse is developing "one-click integrations" for the most popular Git providers, CI/CD platforms, and incident management tools. These integrations simplify the process of setting up your workflows to send data to Pulse.

## PagerDuty

Pulse integrates directly with PagerDuty to receive data about incidents.

To set up the PagerDuty integration:

1.  On Pulse, click **Integrations** and select **PagerDuty**.

    ![PagerDuty integratio](images/pagerduty.png)

1.  Fill in the necessary details:

    -    **PagerDuty subdomain:** Your custom PagerDuty subdomain. For example, `mysubdomain` if you use the URL `https://mysubdomain.pagerduty.com` to access PagerDuty.
    -    **Pulse system name:** Name of the [system](https://docs.pulse.codacy.com/#before-you-begin) to associate with the incidents reported by this integration.

1.  Follow the instructions on the Pulse UI to create a new webhook on PagerDuty using the provided URL. For example:

    ![PagerDuty webhook](images/pagerduty-webhook.png)
