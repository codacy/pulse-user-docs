# User Guide

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

Use the latest version to replace `<VERSION>`: ![Bintray](https://img.shields.io/bintray/v/codacy/pulse/event-cli?label=latest%20version)

Supported operating systems and architectures to replace `<ARCH>`:
- pulse-event-cli_darwin_amd64
- pulse-event-cli_linux_386
- pulse-event-cli_linux_amd64
- pulse-event-cli_windows_386
- pulse-event-cli_windows_amd64

Don't forget to make the binary executable and check that it works:

```sh
chmod +x event-cli
./event-cli help
```

#### Run

##### Deployments
For Cloud SaaS, make sure to push when deploying to production.
For Self-hosted, a better option might be to push when you have a valid artifact ready to be delivered to any user.

```sh
./event-cli push deployment \
    --api-key "<API-KEY>" \
    --identifier "<deployment identifier>" \
    --timestamp "<UNIX epoch timestamp in seconds>" \
    <space separated list of change identifiers>
```

##### Changes

```sh
./event-cli push change \
    --api-key "<API-KEY>" \
    --identifier "<change identifier>" \
    --timestamp "<UNIX epoch timestamp in seconds>"
```

##### Incidents

```sh
./event-cli push incident \
    --api-key "<API-KEY>" \
    --identifier "<incident identifier>" \
    --timestampCreated "<UNIX epoch timestamp in seconds>" \
    --timestampResolved "<UNIX epoch timestamp in seconds>"
```

### HTTP API

Since it might be difficult to use the CLI to send some types of events (like changes or incidents),
we also support pushing events to an HTTP POST endpoint.

This might be useful for sending data from providers that only support webhooks. You can set up your provider to send a webhook to the following URL:
`https://ingestion.acceleratedevops.net/v1/ingestion/<provider>?api_key=<API-KEY>`

There is no specified format for events sent to this endpoint. Although, make sure to include all the fields documented above. If you are planning to push events this way, please let us know. Data will not be immediately available in your dashboard as we'll need to process it on our side.
