# One-click integrations

Pulse is developing "one-click integrations" for the most popular Git providers, CI/CD platforms, and incident management tools. These integrations simplify the process of setting up your workflows to send data to Pulse.

## PagerDuty

Pulse integrates directly with PagerDuty to receive data about incidents.

To set up the PagerDuty integration:

1.  On Pulse, click **Integrations** and select **PagerDuty**.

    ![PagerDuty integration](images/pagerduty.png)

1.  Fill in the necessary details:

    - **PagerDuty subdomain:** Your custom PagerDuty subdomain. For example, `mysubdomain` if you use the URL `https://mysubdomain.pagerduty.com` to access PagerDuty.
    - **Pulse system name:** Name of the [system](https://docs.pulse.codacy.com/#before-you-begin) to associate with the incidents reported by this integration.

1.  Follow the instructions on the Pulse UI to create a new webhook on PagerDuty using the provided URL. For example:

    ![PagerDuty webhook](images/pagerduty-webhook.png)

## GitHub

Pulse integrates directly with GitHub to receive data about deployments and changes.

To set up the GitHub integration:

1.  On Pulse, click **Integrations** and select **GitHub**.

    ![GitHub integration](images/ghi-setup.png)

1.  Click **Install GitHub App** and follow the instructions on the GitHub UI to install the app in the desired organization.

    ![GitHub webhook](images/ghi-github-install.png)

1.  Wait while Pulse creates the integration and the webhook on GitHub for you.

    ![GitHub webhook](images/ghi-creating.png)

In case of success you should be redirected to a page like:

![GitHub webhook](images/ghi-ok.png)

If there is an error as displayed below please [contact support](mailto:pulsesupport@codacy.com).

![GitHub webhook](images/ghi-error.png)
