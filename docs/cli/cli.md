---
description: Use the Pulse CLI to send information about changes, deployments, and incidents whenever they happen in the software delivery workflow of your primary application or service.
---

# Using the Pulse CLI

To measure the performance of your team you must send information to Pulse about changes, deployments, and incidents whenever they happen in the software delivery workflow of your primary application or service.

To push information about these key events to Pulse using the CLI you must complete these main steps:

1.  **Installing the Pulse CLI**

    Download and set up the latest version of the Pulse CLI binary.

1.  **Pushing changes and deployments**

    Push information about changes and deployments when they happen on your software delivery workflow.

1.  **Pushing incidents**

    Push information about incidents when they're solved.

!!! note
    The Pulse CLI is a simplified abstraction over the Pulse Ingestion API. If the CLI requirements below don't fit your use case, explore the [Ingestion API docs](https://ingestion.pulse.codacy.com/v1/api-docs) for interacting directly with the API.

## Before you begin

Consider the following before setting up the integration using the Pulse CLI:

-   [**Make sure that you have an API key**](https://app.pulse.codacy.com/integrations/cli){: target="_blank"} provided by Pulse to identify your organization and authorize you to send data to Pulse.

-   When reporting events to Pulse you should use the field `system` to associate each event with the **most granular unit** that you will use to filter data on the Pulse dashboards, such as by application or service, product, or any other entity or group of entities in your organization.

    !!! important
        Typically, the value of `system` should be the name of the CVS repository corresponding to the event.

        However, if you're using a monorepo the value of `system` should be the name of the component in the repository instead.

    Although the field `system` is optional, if you don't report this information you won't be able to filter the data on the Pulse dashboards. Events without a specified `system` will be grouped under a special system called `_unknown_`.

-   When reporting deployments to Pulse you should use the field `teams` to associate each deployment with the list of teams that contributed with changes included in the deployment.

## 1. Installing the Pulse CLI

Download the latest version of the Pulse CLI for your operating system and make sure that you're able to run the binary.

!!! tip
    **If you're using GitHub but our [one-click integration](../one-click-integrations/github-integration.md) doesn't fit your requirements** we recommend that you use our [Pulse GitHub Action](https://github.com/codacy/pulse-action) as a convenient way to report events directly from your GitHub Actions workflows.

    The Pulse GitHub Action acts as a wrapper for the Pulse CLI and accepts the same fields as the Pulse CLI.

1.  Run the command to download the correct binary for your operating system:

    === "Linux"
        On 32-bit operating systems:

        ```sh
        curl -fsSL -o pulse-event-cli https://artifacts.codacy.com/codacy/pulse/event-cli/latest/pulse-event-cli_linux_386/pulse-event-cli && \
        chmod +x pulse-event-cli
        ```

        On 64-bit operating systems:

        ```sh
        curl -fsSL -o pulse-event-cli https://artifacts.codacy.com/codacy/pulse/event-cli/latest/pulse-event-cli_linux_amd64/pulse-event-cli && \
        chmod +x pulse-event-cli
        ```

    === "macOS"
        ```sh
        curl -fsSL -o pulse-event-cli https://artifacts.codacy.com/codacy/pulse/event-cli/latest/pulse-event-cli_darwin_amd64/pulse-event-cli && \
        chmod +x pulse-event-cli
        ```

    === "Windows"
        On 32-bit operating systems:

        ```
        curl -fsSL -o pulse-event-cli.exe https://artifacts.codacy.com/codacy/pulse/event-cli/latest/pulse-event-cli_windows_386/pulse-event-cli.exe
        ```

        On 64-bit operating systems:

        ```
        curl -fsSL -o pulse-event-cli.exe https://artifacts.codacy.com/codacy/pulse/event-cli/latest/pulse-event-cli_windows_amd64/pulse-event-cli.exe
        ```

1.  Test if you can run the CLI:

    === "Linux and macOS"
        ```sh
        ./pulse-event-cli help
        ```

    === "Windows"
        ```sh
        pulse-event-cli.exe help
        ```

If you need a specific Pulse CLI use the commands above but replace `latest` with the [version](https://github.com/codacy/pulse-event-cli/tags) you want.

## 2. Pushing changes and deployments

Report an event to Pulse whenever your team deploys code to production, including the list of code changes included in that deployment:

-   **For SaaS applications,** report the event on each deployment to your production environment.
-   **For self-hosted applications,** a better option is to report the event each time you make an artifact available to any user of your application, such as when you release new binaries or upload a new version to an app store.

Pulse uses these reports to calculate the metrics [Lead time for changes](../metrics/accelerate.md#lead-time-for-changes) and [Deployment frequency](../metrics/accelerate.md#deployment-frequency).

### Using Git

If you're using Git, send the following information when reporting **changes and deployments** to Pulse:

| Field                   | Description                                                                             | Format                                       |
| ----------------------- | --------------------------------------------------------------------------------------- | -------------------------------------------- |
| previous-deployment-ref | Git reference of the previous deployment.<br/>This can be a tag or a commit identifier. | String                                       |
| identifier              | Version number or another unique identifier of the deployment.                          | String                                       |
| timestamp               | Time when the deployment finished.                                                      | Number<br/>(Unix epoch timestamp in seconds) |
| system                  | Optional. Repository or component to assign to this event.                              | String                                       |
| teams                   | Optional. Teams responsible for the changes in the deployment.                          | String<br/>(comma-separated list)            |

Run the following command to report a deployment and its changes:

=== "Linux and macOS"
    ```sh
    git clone <Git repository URL>
    cd <local Git repository directory>
    ./pulse-event-cli push git deployment \
        --api-key "<API key>" \
        --previous-deployment-ref "<previous deployment ref>" \
        --identifier "<deployment identifier>" \
        --timestamp "$(date +%s)" \
        [--system "<system>"] \
        [--teams <comma-separated list of teams>]
    ```

=== "Windows"
    ```sh
    git clone <Git repository URL>
    cd <local Git repository directory>
    event-cli.exe push git deployment \
        --api-key "<API key>" \
        --previous-deployment-ref "<previous deployment ref>" \
        --identifier "<deployment identifier>" \
        --timestamp "<timestamp>" \
        [--system "<system>"] \
        [--teams <comma-separated list of teams>]
    ```
The command automatically reports all commits done between the previous deployment and the `HEAD` of the Git repository as changes that belong to the deployment being reported.

### Without using Git

If you don't use Git or prefer to have more fine-grained control over the information that you send when reporting changes and deployments, send separate reports for each change and deployment as described below.

!!! tip
    If you're using Git, it's simpler to [report changes and deployments](#using-git) together.

1.  Send the following information when reporting **changes** to Pulse:

    | Field      | Description                                                | Format                                       |
    | ---------- | ---------------------------------------------------------- | -------------------------------------------- |
    | identifier | The commit identifier.                                     | String                                       |
    | timestamp  | Time when the commit was first pushed to the repository.   | Number<br/>(Unix epoch timestamp in seconds) |
    | system     | Optional. Repository or component to assign to this event. | String                                       |

    Run the following command to report each change:

    === "Linux and macOS"
        ```sh
        ./pulse-event-cli push change \
            --api-key "<API key>" \
            --identifier "<change identifier>" \
            --timestamp "$(date +%s)" \
            [--system "<system>"]
        ```

    === "Windows"
        ```sh
        event-cli.exe push change \
            --api-key "<API key>" \
            --identifier "<change identifier>" \
            --timestamp "<timestamp>" \
            [--system "<system>"]
        ```

1.  Send the following information when reporting **deployments** to Pulse:

    | Field      | Description                                                    | Format                                       |
    | ---------- | -------------------------------------------------------------- | -------------------------------------------- |
    | identifier | Version number or another unique identifier of the deployment. | String                                       |
    | timestamp  | Time when the deployment finished.                             | Number<br/>(Unix epoch timestamp in seconds) |
    | system     | Optional. Repository or component to assign to this event.     | String                                       |
    | teams      | Optional. Teams responsible for the changes in the deployment. | String<br/>(comma-separated list)            |
    |            | Commit identifiers included in the deployment.                 | String<br/>(space-separated list)            |

    Run the following command to report each deployment:

    === "Linux and macOS"
        ```sh
        ./pulse-event-cli push deployment \
            --api-key "<API key>" \
            --identifier "<deployment identifier>" \
            --timestamp "$(date +%s)" \
            [--system "<system>"] \
            [--teams <comma-separated list of teams>] \
            <space-separated list of commit identifiers>
        ```

    === "Windows"
        ```sh
        event-cli.exe push deployment \
            --api-key "<API key>" \
            --identifier "<deployment identifier>" \
            --timestamp "<timestamp>" \
            [--system "<system>"] \
            [--teams <comma-separated list of teams>] \
            <space-separated list of commit identifiers>
        ```

## 3. Pushing incidents

Report an event to Pulse whenever an incident resulting from a release or infrastructure configuration change to production is solved. Incidents are any form of degraded service that require remediation:

-   The incident is **created** when you detect a service impairment or service outage in production.
-   The incident is **resolved** when you apply a hotfix or patch, or when you rollback the changes to restore the service in production.

Typically, it's possible to keep track of this information using your monitoring infrastructure or your ticketing system.

Pulse uses these reports to calculate the metrics [Time to recover](../metrics/accelerate.md#time-to-recover) and [Change failure rate](../metrics/accelerate.md#change-failure-rate).

Send the following information when reporting **incidents** to Pulse:

| Field             | Description                                                | Format                                       |
| ----------------- | ---------------------------------------------------------- | -------------------------------------------- |
| identifier        | A unique identifier of the incident.                       | String                                       |
| timestampCreated  | Time when the incident started or was detected.            | Number<br/>(Unix epoch timestamp in seconds) |
| timestampResolved | Time when the incident was resolved.                       | Number<br/>(Unix epoch timestamp in seconds) |
| system            | Optional. Repository or component to assign to this event. | String                                       |

Run the following command to report each incident:

=== "Linux and macOS"
    ```sh
    ./pulse-event-cli push incident \
        --api-key "<API key>" \
        --identifier "<incident identifier>" \
        --timestampCreated "<timestampCreated>" \
        --timestampResolved "$(date +%s)" \
        [--system "<system>"]
    ```

=== "Windows"
    ```sh
    pulse-event-cli.exe push incident \
        --api-key "<API key>" \
        --identifier "<incident identifier>" \
        --timestampCreated "<timestampCreated>" \
        --timestampResolved "<timestampResolved>" \
        [--system "<system>"]
    ```

## See also

-   [Accelerate metrics](../metrics/accelerate.md)
-   [Changes and reviews metrics](../metrics/accelerate-changes.md)
