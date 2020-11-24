# Integrating with Pulse

Pulse displays metrics that provide insights about the current and historic performance of your software delivery process. To calculate these metrics, Pulse must collect information from key events of your particular software development workflow.

## Before you begin

Currently, Pulse supports a push-based integration with your workflow and the recommended way of sending the necessary data to Pulse is by using the Pulse CLI.

However, in some scenarios it may not be feasible to use the CLI to send data to Pulse, such as when reporting changes or incidents, or if you are sending data from providers that only support webhooks. For these situations, you can call an HTTP POST webhook instead. For example:

`https://ingestion.acceleratedevops.net/v1/ingestion/<PROVIDER>?api_key=<API KEY>`

!!! important
    **Please let us know if you are planning on pushing events to Pulse using the webhook** so we can give you more detailed instructions on how to use it.

<!-- TODO Best place to introduce and describe the concept of system? -->
A system is a partition of your organization. Depending on your use case it can be a service, a repository or any other entity or group of entities in your organization. Currently Pulse does not use this information, but as we develop new features this will be used to show scoped views of your organization's data.

The next sections include detailed instructions on how to complete the integration setup process.

## 1. Installing the Pulse CLI

Download the latest version of the CLI for your operating system and make sure that you're able to run the binary.

1.  Take note of the latest version of the CLI:

    [![Download](https://api.bintray.com/packages/codacy/pulse/event-cli/images/download.svg)](https://bintray.com/codacy/pulse/event-cli/_latestVersion)

1.  Run the command to download the correct binary for your operating system, changing `<VERSION>` to the latest version obtained on the previous step:

    === "Linux"
        On 32-bit operating systems:

        ```sh
        curl -fsSL -o event-cli https://dl.bintray.com/codacy/pulse/event-cli/<VERSION>/pulse-event-cli_linux_386/pulse-event-cli && \
        chmod +x event-cli
        ```

        On 64-bit operating systems:
    
        ```sh
        curl -fsSL -o event-cli https://dl.bintray.com/codacy/pulse/event-cli/<VERSION>/pulse-event-cli_linux_amd64/pulse-event-cli && \
        chmod +x event-cli
        ```

    === "macOS"
        ```sh
        curl -fsSL -o event-cli https://dl.bintray.com/codacy/pulse/event-cli/<VERSION>/pulse-event-cli_darwin_amd64/pulse-event-cli && \
        chmod +x event-cli
        ```

    === "Windows"
        On 32-bit operating systems:

        ```
        curl -fsSL -o event-cli.exe https://dl.bintray.com/codacy/pulse/event-cli/<VERSION>/pulse-event-cli_windows_386/pulse-event-cli.exe
        ```
    
        On 64-bit operating systems:

        ```
        curl -fsSL -o event-cli.exe https://dl.bintray.com/codacy/pulse/event-cli/<VERSION>/pulse-event-cli_windows_amd64/pulse-event-cli.exe
        ```

1.  Test if you can run the CLI:

    === "Linux and macOS"
        ```sh
        ./event-cli help
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
| system                  | Optional. Name of the system the data refers to.                                        | String                                       |
| previous-deployment-ref | Git reference of the previous deployment.<br/>This can be a tag or a commit identifier. | String                                       |
| identifier              | Version number or another unique identifier of the deployment.                          | String                                       |
| timestamp               | Time when the deployment finished.                                                      | Number<br/>(Unix epoch timestamp in seconds) |

Run the following command to report a deployment and its changes:

=== "Linux and macOS"
    ```sh
    git clone <Git repository URL>
    cd <local Git repository directory>
    ./event-cli push git deployment \
        --api-key "<API key>" \
        [--system "<name of the system the data refers to>"] \
        --previous-deployment-ref "<previous deployment ref>" \
        --identifier "<deployment identifier>" \
        --timestamp "$(date +%s)"
    ```

=== "Windows"
    ```sh
    git clone <Git repository URL>
    cd <local Git repository directory>
    event-cli.exe push git deployment \
        --api-key "<API key>" \
        [--system "<name of the system the data refers to>"] \
        --previous-deployment-ref "<previous deployment ref>" \
        --identifier "<deployment identifier>" \
        --timestamp "<timestamp>"
    ```
The command automatically reports all commits done between the previous deployment and the `HEAD` of the Git repository as changes that belong to the deployment being reported.

#### Without using Git

If you don't use Git or prefer to have more fine-grained control over the information that you send when reporting changes and deployments, send separate reports for each change and deployment as described below.

!!! tip
    If you're using Git, it's simpler to [report changes and deployments](#using-git) together.

1.  Send the following information when reporting **changes** to Pulse:

    | Field      | Description                                              | Format                                       |
    | ---------- | -------------------------------------------------------- | -------------------------------------------- |
    | system     | Optional. Name of the system the data refers to.         | String                                       |
    | identifier | The commit identifier.                                   | String                                       |
    | timestamp  | Time when the commit was first pushed to the repository. | Number<br/>(Unix epoch timestamp in seconds) |

    Run the following command to report each change:

    === "Linux and macOS"
        ```sh
        ./event-cli push change \
            --api-key "<API key>" \
            [--system "<name of the system the data refers to>"] \
            --identifier "<change identifier>" \
            --timestamp "$(date +%s)"
        ```

    === "Windows"
        ```sh
        event-cli.exe push change \
            --api-key "<API key>" \
            [--system "<name of the system the data refers to>"] \
            --identifier "<change identifier>" \
            --timestamp "<timestamp>"
        ```

1.  Send the following information when reporting **deployments** to Pulse:

    | Field      | Description                                                    | Format                                       |
    | ---------- | -------------------------------------------------------------- | -------------------------------------------- |
    | system     | Optional. Name of the system the data refers to.               | String                                       |
    | identifier | Version number or another unique identifier of the deployment. | String                                       |
    | timestamp  | Time when the deployment finished.                             | Number<br/>(Unix epoch timestamp in seconds) |
    |            | Commit identifiers included in the deployment.                 | String<br/>(space-separated list)            |

    Run the following command to report each deployment:

    === "Linux and macOS"
        ```sh
        ./event-cli push deployment \
            --api-key "<API key>" \
            [--system "<name of the system the data refers to>"] \
            --identifier "<deployment identifier>" \
            --timestamp "$(date +%s)" \
            <space-separated list of commit identifiers>
        ```

    === "Windows"
        ```sh
        event-cli.exe push deployment \
            --api-key "<API key>" \
            [--system "<name of the system the data refers to>"] \
            --identifier "<deployment identifier>" \
            --timestamp "<timestamp>" \
            <space-separated list of commit identifiers>
        ```

### Incidents

Report an event to Pulse whenever an incident resulting from a release or infrastructure configuration change to production is solved. Incidents are any form of degraded service that require remediation:

-   The incident is **created** when you detect a service impairment or service outage in production.
-   The incident is **resolved** when you apply a hotfix or patch, or when you rollback the changes to restore the service in production.

Typically, it's possible to keep track of this information using your monitoring infrastructure or your ticketing system.

Pulse uses these reports to calculate the metrics [Median time to recovery](metrics.md#median-time-to-recover) and [Change failure rate](metrics.md#change-failure-rate).

Send the following information when reporting **incidents** to Pulse:

| Field             | Description                                      | Format                                       |
| ----------------- | ------------------------------------------------ | -------------------------------------------- |
| system            | Optional. Name of the system the data refers to. | String                                       |
| identifier        | A unique identifier of the incident.             | String                                       |
| timestampCreated  | Time when the incident started or was detected.  | Number<br/>(Unix epoch timestamp in seconds) |
| timestampResolved | Time when the incident was resolved.             | Number<br/>(Unix epoch timestamp in seconds) |

Run the following command to report each incident:

=== "Linux and macOS"
    ```sh
    ./event-cli push incident \
        --api-key "<API key>" \
        [--system "<name of the system the data refers to>"] \
        --identifier "<incident identifier>" \
        --timestampCreated "<timestampCreated>" \
        --timestampResolved "$(date +%s)"
    ```

=== "Windows"
    ```sh
    event-cli.exe push incident \
        --api-key "<API key>" \
        [--system "<name of the system the data refers to>"] \
        --identifier "<incident identifier>" \
        --timestampCreated "<timestampCreated>" \
        --timestampResolved "<timestampResolved>"
    ```

## Examples

Some examples that we used to quickly populate our dashboard with historical data.

### Using the CLI to push historic changes and deployments

In this example we used the CLI to push past changes and deployments from a git repository into Pulse. Deployments are identified using semantic version git tags. Changes are the commits included in those deployments.

```sh
# example.sh

#!/usr/bin/env bash

set -e

CREDENTIALS="xxx"

workdir=$(mktemp -d -t website-dora-XXXXXXXXXX)
# workdir=$(pwd)/website

function clean {
    rm -rf ${workdir}
}
trap clean EXIT

git clone git@bitbucket.org:qamine/codacy-website "$workdir"
cd ${workdir}
mapfile -t deployments < <( git for-each-ref --sort=creatordate --format '%(refname) %(objectname)' refs/tags | grep -E '^refs\/tags\/[0-9]+\.[0-9]+\.[0-9]+\s' | awk -F"\/" '{print $3}' )
previous_deployment=""
current_deployment=""
for deployment in "${deployments[@]}"
do
    deployment_id=$(echo "${deployment}" | awk '{print $1}')
    deployment_sha=$(echo "${deployment}" | awk '{print $2}')
    deployment_date=$(git log --format="%at" ${deployment_sha} | head -n 1)

    # Skip deployments before date
    # If you are on MacOS you need to use $(date -jf "%b %d %Y" "Oct 01 2020" +%s) instead
    if [ ${deployment_date} -lt $(date -d "Oct 01 2020" +%s) ]
    then
        continue
    fi

    if [ -z "${previous_deployment}" ]
    then
        previous_deployment="${deployment_sha}"
        continue
    else
        current_deployment="${deployment_sha}"

        deployment_changes=()
        mapfile -t deployment_changes < <( git log --format="%H" ${previous_deployment}...${current_deployment} )

        if [ ${#deployment_changes[@]} -gt 0 ]
        then
            echo "Pushing deployment ${deployment_id} with changes [${deployment_changes[@]}]"
            ./event-cli push deployment \
                --api-key "${CREDENTIALS}" \
                --identifier "${deployment_id}" \
                --timestamp "${deployment_date}" \
                ${deployment_changes[@]}

            for deployment_change in "${deployment_changes[@]}"
            do
                deployment_change_date=$(git log --format="%at" ${deployment_change} --max-count=1)

                echo "Pushing changes ${deployment_change} with date ${deployment_change_date}"
                ./event-cli push change \
                    --api-key "${CREDENTIALS}" \
                    --identifier "${deployment_change}" \
                    --timestamp "${deployment_change_date}"
            done
        fi

        previous_deployment="${deployment_sha}"
    fi
done
```

### Using the CLI to push historic incidents

In this example we used the CLI to push past incidents from the Codacy status page API into Pulse.

```py
# incidents.py

import requests
import subprocess
from datetime import datetime, timezone

url = 'https://status.codacy.com/api/incidents'

resp = requests.get(url=url)
data = resp.json()

def convert_to_unix_epoch(date_time_str):
    return str(int(datetime.strptime(date_time_str, '%Y-%m-%dT%H:%M:%S.%fZ').replace(tzinfo=timezone.utc).timestamp()))

for incident in data:
    id = incident['incidentID']
    created = convert_to_unix_epoch(incident['createdAt'])
    updated = convert_to_unix_epoch(incident['updatedAt'])

    if incident['status'] == 'Resolved':
        print(f'Pushing incident {id} created in {created} and resolved at {updated}')
        print(created)
        print(updated)
        bashCmd = ["./event-cli", "push", "incident",
        "--api-key", "xxx",
        "--identifier", id,
        "--timestampCreated", created,
        "--timestampResolved", updated
        ]
        process = subprocess.Popen(bashCmd, stdout=subprocess.PIPE)
        output, error = process.communicate()
    else:
        print(f"Skipping incident {id} . Because it was not resolved")
```
