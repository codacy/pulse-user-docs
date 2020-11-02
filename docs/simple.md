# Getting Started

## 1. Decide what repository to instrument

The best way to get started is to identify one repository that you fell might be representative of your system.

If you can't decide choose one to start anyway.

## 2. Send your first event

querido rafael de segunda feira:
start with changes vs deploy?

### 2.1 Defining "prodution"

For Cloud SaaS, make sure to push when deploying to production. For Self-hosted, a better option might be to push when you have a valid artifact ready to be delivered to any user.

### 2.2 Add CLI
To start measuring Deployment Frequency, include the following script as a step in your production pipeline:

```sh
curl -fsSL "https://dl.bintray.com/codacy/pulse/event-cli/<VERSION>/<ARCH>/pulse-event-cli" -o event-cli && \
chmod +x event-cli && \
./event-cli push deployment \
    --api-key "<API-KEY>" \
    --identifier "<deployment identifier>" \
    --timestamp "<UNIX epoch timestamp in seconds>" \
    "keep this string as is"
```

Replace the variables with the appropriate values:

| Field      | Description                                            | Format                                                                                                                                                             |
| ---------- | ------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| VERSION    | CLI version                                            | ![Bintray](https://img.shields.io/bintray/v/codacy/pulse/event-cli?label=latest%20version)                                                                         |
| ARCH       | Operating system and architecture                      | One of: `pulse-event-cli_darwin_amd64`, `pulse-event-cli_linux_386`, `pulse-event-cli_linux_amd64`, `pulse-event-cli_windows_386`, `pulse-event-cli_windows_amd64` |
| identifier | A version or other unique identifier of the deployment | String                                                                                                                                                             |
| timestamp  | Time the deployment was completed                      | Number (UNIX epoch timestamp in seconds)                                                                                                                           |

If the event is sent successfuly you will see a message in the CLI output.

### 3.

querido rafael de segunda feira:
continue to next events?
