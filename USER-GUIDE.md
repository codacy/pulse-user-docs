# User Guide

## API Key

This is your organization's identification API key: `API-KEY`

It will allow you to push events to Pulse.

## Pushing Data

Currently we support two ways to push data.
A CLI and a HTTP POST request.

### CLI

#### Install

The CLI is distributed in a binary format (for multiple operating systems and architectures)
that you can download [here](https://dl.bintray.com/codacy/pulse/event-cli/).

Example for linux (replace version):

```sh
curl -fsSL "https://dl.bintray.com/codacy/pulse/event-cli/<VERSION>/pulse-event-cli_linux_amd64/:pulse-event-cli" -o event-cli
```

#### Run

##### Deployments

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

### HTTP POST

Since it might be difficult to use the CLI to send some types of events (like incidents),
we also support pushing events through an HTTP POST endpoint.

**We advise to only use this method of pushing events if the others are not possible.**
If you require to push events in this way, please contact us first so we can configure it with you.

This might be usefull for sending data from providers that only support webhooks.
You can setup you provider to send a webhook to the following URL:

`https://ingestion.acceleratedevops.net/v1/ingestion/<provider>?api_key=<API-KEY>`
