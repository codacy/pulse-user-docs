# Integration examples

This page includes examples of scripts that we used to quickly populate our own Pulse dashboard with historical data.

You can use the examples to understand better how you can integrate the Pulse CLI with your existing workflows, or adapt these examples to populate your Pulse dashboard with historical data from your team.

## Pushing historic changes and deployments

In this example, we used the Pulse CLI to push past changes and deployments from a Git repository into Pulse.

Deployments are identified using semantic version Git tags, and the changes are the commits included in those deployments.

```sh
# example.sh

#!/usr/bin/env bash

set -e

CREDENTIALS="xxx"

workdir=$(mktemp -d -t website-dora-XXXXXXXXXX)

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
    # If you are on macOS you need to use $(date -jf "%b %d %Y" "Oct 01 2020" +%s) instead
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
            ./pulse-event-cli push deployment \
                --api-key "${CREDENTIALS}" \
                --identifier "${deployment_id}" \
                --timestamp "${deployment_date}" \
                ${deployment_changes[@]}

            for deployment_change in "${deployment_changes[@]}"
            do
                deployment_change_date=$(git log --format="%at" ${deployment_change} --max-count=1)

                echo "Pushing changes ${deployment_change} with date ${deployment_change_date}"
                ./pulse-event-cli push change \
                    --api-key "${CREDENTIALS}" \
                    --identifier "${deployment_change}" \
                    --timestamp "${deployment_change_date}"
            done
        fi

        previous_deployment="${deployment_sha}"
    fi
done
```

## Pushing historic incidents

In this example, we used the Pulse CLI to push past incidents from the Codacy status page API into Pulse.

```python
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
        bashCmd = ["./pulse-event-cli", "push", "incident",
        "--api-key", "xxx",
        "--identifier", id,
        "--timestampCreated", created,
        "--timestampResolved", updated
        ]
        process = subprocess.Popen(bashCmd, stdout=subprocess.PIPE)
        output, error = process.communicate()
    else:
        print(f"Skipping incident {id} because it was not resolved")
```
