# Integration Guide

## API Key

The API key identifies your organization and authorizes you to push events to Pulse.

## Pushing Data

Currently, we support two ways to push data: a CLI and an HTTP API.

### CLI

#### Install

The CLI is distributed in a binary format (for multiple operating systems and architectures)
that you can download:

```sh
curl -fsSL "https://dl.bintray.com/codacy/pulse/event-cli/<VERSION>/<ARCH>/pulse-event-cli" -o event-cli
```

Use the latest version to replace `<VERSION>`: [ ![Download](https://api.bintray.com/packages/codacy/pulse/event-cli/images/download.svg)](https://bintray.com/codacy/pulse/event-cli/_latestVersion)

Supported operating systems and architectures to replace `<ARCH>`:

-  pulse-event-cli_darwin_amd64
-  pulse-event-cli_linux_386
-  pulse-event-cli_linux_amd64
-  pulse-event-cli_windows_386
-  pulse-event-cli_windows_amd64

Don't forget to make the binary executable and check that it works:

```sh
chmod +x event-cli && \
./event-cli help
```

#### Run

##### Deployments
For Cloud SaaS, make sure to push when deploying to production. For Self-hosted, a better option might be to push when you have a valid artifact ready to be delivered to any user.

| Field      | Description                                            | Format                                   |
| ---------- | ------------------------------------------------------ | ---------------------------------------- |
| identifier | A version or other unique identifier of the deployment | String                                   |
| timestamp  | Time the deployment was completed                      | Number (UNIX epoch timestamp in seconds) |
|            | Commits being deployed in the deployment               | String (space separated list)            |

```sh
./event-cli push deployment \
    --api-key "<API-KEY>" \
    --identifier "<deployment identifier>" \
    --timestamp "<UNIX epoch timestamp in seconds>" \
    <space separated list of change identifiers>
```

##### Changes
A commit pushed to a Git.

| Field      | Description                                        | Format                                   |
| ---------- | -------------------------------------------------- | ---------------------------------------- |
| identifier | The commit hash                                    | String                                   |
| timestamp  | Time the commit was first pushed to the repository | Number (UNIX epoch timestamp in seconds) |

```sh
./event-cli push change \
    --api-key "<API-KEY>" \
    --identifier "<change identifier>" \
    --timestamp "<UNIX epoch timestamp in seconds>"
```

##### Incidents
A change to production or release to users that resulted in degraded service (e.g., lead to service impairment or service outage) and subsequently require remediation (e.g., require a hotfix, rollback, fix forward, patch)

| Field             | Description                               | Format                                   |
| ----------------- | ----------------------------------------- | ---------------------------------------- |
| identifier        | A unique identifier of the incident       | String                                   |
| timestampCreated  | Time the incident started or was detected | Number (UNIX epoch timestamp in seconds) |
| timestampResolved | Time the incident was resolved            | Number (UNIX epoch timestamp in seconds) |

```sh
./event-cli push incident \
    --api-key "<API-KEY>" \
    --identifier "<incident identifier>" \
    --timestampCreated "<UNIX epoch timestamp in seconds>" \
    --timestampResolved "<UNIX epoch timestamp in seconds>"
```

!!! tip
    You can get the UNIX epoch timestamp in seconds with `date +%s`.

### HTTP API

Since it might be difficult to use the CLI to send some types of events (like changes or incidents),
we also support pushing events to an HTTP POST endpoint.

This might be useful for sending data from providers that only support webhooks. You can set up your provider to send a webhook to the following URL:
`https://ingestion.acceleratedevops.net/v1/ingestion/<provider>?api_key=<API-KEY>`

There is no specified format for events sent to this endpoint. Although, make sure to include all the fields documented above. If you are planning to push events this way, please let us know. Data will not be immediately available in your dashboard as we'll need to process it on our side.

## Examples
Some examples that we used to quickly populate our dashboard with historical data.

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
                deployment_change_date=$(git log --format="%at" ${deployment_change} | head -n 1)

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
