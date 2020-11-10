# Integrating with Pulse

Pulse displays metrics that provide insights about the current and historic performance of your software delivery process. To calculate these metrics, Pulse must collect information from key events of your particular software development workflow.

Currently, the recommended way of integrating your workflow with Pulse is by using a CLI to send the necessary data to Pulse.

However, in some scenarios it may not be feasible to use the CLI to send data to Pulse, such as when reporting changes or incidents, or if you are sending data from providers that only support webhooks. For these situations, you can call an HTTP POST webhook instead. For example:

`https://ingestion.acceleratedevops.net/v1/ingestion/<PROVIDER>?api_key=<API KEY>`

!!! important
    **Please let us know if you are planning on pushing events to Pulse using the webhook** so we can give you more detailed instructions on how to use it.

The next sections include detailed instructions on how to complete the integration setup process.

## 1. Installing the Pulse CLI

Download the latest version of the CLI for your operating system and make sure that you're able to run the binary.

1.  Take note of the latest version of the CLI:

    [![Download](https://api.bintray.com/packages/codacy/pulse/event-cli/images/download.svg)](https://bintray.com/codacy/pulse/event-cli/_latestVersion)

1.  Run the command to download the correct binary for your operating system, changing `<VERSION>` to the latest version obtained on the previous step:

    | Operating system | Command to download the binary |
    | ---------------- | ------------------------------ |
    | macOS            | `curl -fsSL -o event-cli https://dl.bintray.com/codacy/pulse/event-cli/<VERSION>/pulse-event-cli_darwin_amd64/pulse-event-cli` |
    | Linux 32-bit     | `curl -fsSL -o event-cli https://dl.bintray.com/codacy/pulse/event-cli/<VERSION>/pulse-event-cli_linux_386/pulse-event-cli` |
    | Linux 64-bit     | `curl -fsSL -o event-cli https://dl.bintray.com/codacy/pulse/event-cli/<VERSION>/pulse-event-cli_linux_amd64/pulse-event-cli` |
    | Windows 32-bit   | `curl -fsSL -o event-cli.exe https://dl.bintray.com/codacy/pulse/event-cli/<VERSION>/pulse-event-cli_windows_386/pulse-event-cli.exe` |
    | Windows 64-bit   | `curl -fsSL -o event-cli.exe https://dl.bintray.com/codacy/pulse/event-cli/<VERSION>/pulse-event-cli_windows_amd64/pulse-event-cli.exe` |

1.  **On macOS and Linux,** make the binary executable and test if you can run the CLI:

    ```sh
    chmod +x event-cli && ./event-cli help
    ```

    **On Windows,** test if you can run the CLI:

    ```sh
    event-cli.exe help
    ```

## 2. Pushing data to Pulse

To measure the performance of your team, you must send information to Pulse about the following key events whenever they happen in your software delivery workflow:

-  [Changes](#changes)
-  [Deployments](#deployments)
-  [Incidents](#incidents)

!!! important
    Before setting up the integration with Pulse, make sure that you have an API key provided by Pulse to identify your organization and authorize you to send data to Pulse.

### Changes

Send information to Pulse whenever a commit is pushed to a repository.

| Field      | Description                                             | Format                                       |
| ---------- | ------------------------------------------------------- | -------------------------------------------- |
| identifier | The commit identifier                                   | String                                       |
| timestamp  | Time when the commit was first pushed to the repository | Number<br/>(Unix epoch timestamp in seconds) |

```sh
./event-cli push change \
    --api-key "<API KEY>" \
    --identifier "<commit identifier>" \
    --timestamp "<Unix epoch timestamp in seconds>"
```

### Deployments

<!-- TODO Relate each event to the metrics that they will help to calculate -->

**For SaaS applications,** send information to Pulse whenever you deploy to production.

**For self-hosted applications,** a better option might be to send information to Pulse whenever you have a valid artifact ready to be delivered to any user.

| Field      | Description                                            | Format                                       |
| ---------- | ------------------------------------------------------ | -------------------------------------------- |
| identifier | Version or another unique identifier of the deployment | String                                       |
| timestamp  | Time when the deployment finished                      | Number<br/>(Unix epoch timestamp in seconds) |
|            | Commit identifiers included in the deployment          | String<br/>(space-separated list)            |

```sh
./event-cli push deployment \
    --api-key "<API KEY>" \
    --identifier "<deployment identifier>" \
    --timestamp "$(date +%s)" \
    <space-separated list of commit identifiers>
```

<!-- IDEA
     Consider including example snippets for the webhook -->

### Incidents

Send information to Pulse whenever there is a change to production or a release to users that resulted in degraded service (e.g., service impairment or service outage) and subsequently required remediation (e.g., hotfix, rollback, fix forward, patch).

| Field             | Description                                    | Format                                       |
| ----------------- | ---------------------------------------------- | -------------------------------------------- |
| identifier        | A unique identifier of the incident            | String                                       |
| timestampCreated  | Time when the incident started or was detected | Number<br/>(Unix epoch timestamp in seconds) |
| timestampResolved | Time when the incident was resolved            | Number<br/>(Unix epoch timestamp in seconds) |

```sh
./event-cli push incident \
    --api-key "<API KEY>" \
    --identifier "<incident identifier>" \
    --timestampCreated "<Unix epoch timestamp in seconds>" \
    --timestampResolved "<Unix epoch timestamp in seconds>"
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
