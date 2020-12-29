# Integrating with Pulse

Pulse displays metrics that provide insights about the current and historic performance of your software delivery process. To calculate these metrics, Pulse must collect information from key events of your particular software development workflow.

Currently, Pulse supports a push-based integration with your workflow and the recommended way of reporting the necessary events to Pulse is by using the Pulse CLI.

## Before you begin

Consider the following before integrating your workflow with Pulse:

-   When reporting events to Pulse you should use the field `system` to associate each event with the **most granular unit** that you will use to filter data on the Pulse dashboards, such as by application or service, product, team, or any other entity or group of entities in your organization.

    !!! important
        Typically, the value of `system` should be the name of the CVS repository corresponding to the event.

        However, if you're using a monorepo the value of `system` should be the name of the component in the repository instead.

    Although the field `system` is optional, if you don't report this information you won't be able to filter the data on the Pulse dashboards.

-   In some scenarios, it may not be feasible to use the CLI to send data to Pulse, such as when reporting changes or incidents, or if you are reporting events from providers that only support webhooks. For these situations, you can call an HTTP POST webhook instead. For example:

    ```test
    https://ingestion.pulse.codacy.com/v1/ingestion/<PROVIDER>?api_key=<API KEY>
    ```

    !!! important
        **Please let us know if you are planning on reporting events to Pulse using the webhook** so we can give you more detailed instructions on how to use it.

The next sections include detailed instructions on how to set up the integration with Pulse.

## 1. Installing the Pulse CLI

Download the latest version of the CLI for your operating system and make sure that you're able to run the binary.

1.  Take note of the latest version of the CLI:

    [![Download](https://api.bintray.com/packages/codacy/pulse/event-cli/images/download.svg)](https://bintray.com/codacy/pulse/event-cli/_latestVersion)

1.  Run the command to download the correct binary for your operating system, changing `<VERSION>` to the latest version obtained on the previous step:

    === "Linux"
        On 32-bit operating systems:

        ```sh
        curl -fsSL -o pulse-event-cli https://dl.bintray.com/codacy/pulse/event-cli/<VERSION>/pulse-event-cli_linux_386/pulse-event-cli && \
        chmod +x pulse-event-cli
        ```

        On 64-bit operating systems:
    
        ```sh
        curl -fsSL -o pulse-event-cli https://dl.bintray.com/codacy/pulse/event-cli/<VERSION>/pulse-event-cli_linux_amd64/pulse-event-cli && \
        chmod +x pulse-event-cli
        ```

    === "macOS"
        ```sh
        curl -fsSL -o pulse-event-cli https://dl.bintray.com/codacy/pulse/event-cli/<VERSION>/pulse-event-cli_darwin_amd64/pulse-event-cli && \
        chmod +x pulse-event-cli
        ```

    === "Windows"
        On 32-bit operating systems:

        ```
        curl -fsSL -o pulse-event-cli.exe https://dl.bintray.com/codacy/pulse/event-cli/<VERSION>/pulse-event-cli_windows_386/pulse-event-cli.exe
        ```
    
        On 64-bit operating systems:

        ```
        curl -fsSL -o pulse-event-cli.exe https://dl.bintray.com/codacy/pulse/event-cli/<VERSION>/pulse-event-cli_windows_amd64/pulse-event-cli.exe
        ```

1.  Test if you can run the CLI:

    === "Linux and macOS"
        ```sh
        ./pulse-event-cli help
        ```

    === "Windows"
        ```sh
        event-cli.exe help
        ```

## 2. Pushing data to Pulse

To measure the performance of your team, you must send information to Pulse about the following key events whenever they happen in the software delivery workflow of your primary application or service:

-   [Changes and deployments](#changes-and-deployments)
-   [Incidents](#incidents)

!!! important
    Before setting up the integration with Pulse, make sure that you have an API key provided by Pulse to identify your organization and authorize you to send data to Pulse.

### Changes and deployments

Report an event to Pulse whenever your team deploys code to production, including the list of code changes included in that deployment:

-   **For SaaS applications,** report the event on each deployment to your production environment.
-   **For self-hosted applications,** a better option is to report the event each time you make an artifact available to any user of your application, such as when you release new binaries or upload a new version to an app store.

Pulse uses these reports to calculate the metrics [Lead time for changes](metrics.md#lead-time-for-changes) and [Deployment frequency](metrics.md#deployment-frequency).

#### Using Git

If you're using Git, send the following information when reporting **changes and deployments** to Pulse:

| Field                   | Description                                                                             | Format                                       |
| ----------------------- | --------------------------------------------------------------------------------------- | -------------------------------------------- |
| previous-deployment-ref | Git reference of the previous deployment.<br/>This can be a tag or a commit identifier. | String                                       |
| identifier              | Version number or another unique identifier of the deployment.                          | String                                       |
| timestamp               | Time when the deployment finished.                                                      | Number<br/>(Unix epoch timestamp in seconds) |
| system                  | Optional. Repository or component to assign to this event.                              | String                                       |

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
        [--system "<system>"]
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
        [--system "<system>"]
    ```
The command automatically reports all commits done between the previous deployment and the `HEAD` of the Git repository as changes that belong to the deployment being reported.

#### Without using Git

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
    |            | Commit identifiers included in the deployment.                 | String<br/>(space-separated list)            |

    Run the following command to report each deployment:

    === "Linux and macOS"
        ```sh
        ./pulse-event-cli push deployment \
            --api-key "<API key>" \
            --identifier "<deployment identifier>" \
            --timestamp "$(date +%s)" \
            [--system "<system>"] \
            <space-separated list of commit identifiers>
        ```

    === "Windows"
        ```sh
        event-cli.exe push deployment \
            --api-key "<API key>" \
            --identifier "<deployment identifier>" \
            --timestamp "<timestamp>" \
            [--system "<system>"] \
            <space-separated list of commit identifiers>
        ```

### Incidents

Report an event to Pulse whenever an incident resulting from a release or infrastructure configuration change to production is solved. Incidents are any form of degraded service that require remediation:

-   The incident is **created** when you detect a service impairment or service outage in production.
-   The incident is **resolved** when you apply a hotfix or patch, or when you rollback the changes to restore the service in production.

Typically, it's possible to keep track of this information using your monitoring infrastructure or your ticketing system.

Pulse uses these reports to calculate the metrics [Median time to recovery](metrics.md#median-time-to-recover) and [Change failure rate](metrics.md#change-failure-rate).

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
    event-cli.exe push incident \
        --api-key "<API key>" \
        --identifier "<incident identifier>" \
        --timestampCreated "<timestampCreated>" \
        --timestampResolved "<timestampResolved>" \
        [--system "<system>"]
    ```
