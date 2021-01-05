# Integrating with PagerDuty

Pulse can integrate directly with PagerDuty to receive Webhooks that will be ingested as incidents.

## Requirements

- Pulse API key for an organization

## Setup

1. Inside your PagerDuty account navigate to `Services` > `Service Directory` > `Choose a service` > `Integrations Tab` > `Add or manage extensions` > `New Extension`
2. Fill out the inputs.
   In the `Extension Type` field choose the `Generic V2 Webhook`.
   The `Name` can be anything, `Pulse` is a good default.
   The `Details` section should include the URL of Pulse's ingestion api (`https://ingestion.pulse.codacy.com/v1/ingestion/pagerduty?system=<MY-SYSTEM>&api_key=<API-KEY>`),
   including the API key and system to where you are sending the data.
   PagerDuty already has a concept of a service which can be a good starting point for the system field in the Pulse URL.

![Creating a PagerDuty Webhook integration](assets/pagerduty-webhook.png)
