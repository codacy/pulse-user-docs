# Calculated metrics

These metrics are calculated using strictly `changes`, `deployments` and `incidents` for a rolling window of the last 90 days.

## Deployment frequency

The most specific that applies:

| Deployment frequency | Value                                                                                                                                  |
| -------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| On-demand            | Median of more than one `deployments` per day (i.e. more than 50% of the days have more than one deployment.)                          |
| Daily                | Median of three or more days with `deployments` in a calendar week (i.e. more than 50% of weeks have 3 or more days with deployments.) |
| Weekly               | Median of one or more `deployments` in a calendar week (i.e. more than 50% of weeks have at least one deployment.)                     |
| Monthly              | Median of one or more `deployments` in a calendar month (i.e. more than 50% of months have at least one deployment.)                   |
| Yearly               | None of the above                                                                                                                      |

!!! note
    The value above is used to determine your Performance level. The numeric value displayed as Deployment Frequency is the number of `deployments` per day.

When we aggregate Deployment Frequency we use the average of a period of time.

| Performance level[^1] | Value                                                              |
| --------------------- | ------------------------------------------------------------------ |
| Low                   | Monthly or Yearly (Between once per month and once every 6 months) |
| Medium                | Weekly (Between once per week and once per month)                  |
| High                  | Daily (Between once per day and once per week)                     |
| Elite                 | On-demand (Multiple deployments per day)                           |

## Lead time for changes

Median of `deployments` time created - `changes` time created.

The time of `changes` is when code is actually checked into a repository.

When we aggregate Lead time for changes we use the maximum of a period of time.

| Performance level[^1] | Value                        |
| --------------------- | ---------------------------- |
| Low                   | Between 1 month and 6 months |
| Medium                | Between 1 week and 1 month   |
| High                  | Between 1 day and 1 week     |
| Elite                 | Less than 1 day              |

### Sub-metrics

There are several metrics that directly influence **Lead time for changes**,
and can help you track in more detail what needs to be improved in your workflow.
All the sub-metrics are aggregated by an interval (day, week, or month) and have values for
the average, median, and percentiles 5%, 20%, 25%, 75%, 80%, 95%.

These metrics are based on pull requests and:

-   Include draft pull requests: the lifetime of a pull request includes the time marked as draft.
-   Exclude open pull requests
-   Exclude declined pull requests
-   Exclude pull requests without commits
-   Exclude push-forced pull requests: where the first commit was authored after the pull request was open.

#### Time to Open

An approximation of the time a change takes to be developed. How long does it take for features to be developed?

This metric is calculated as the time between the first commit in the pull request's branch and the pull request being created.

#### Time to Review

The time spent in the code review process. Once a pull request is opened, how long does it take to merge?

This metric is calculated as the time between the pull request being created and the pull request being merged.

## Median time to recover

Median of `incidents` time resolved - `incidents` time created.

When we aggregate Median time to recover we use the maximum of a period of time.

| Performance level[^1] | Value                      |
| --------------------- | -------------------------- |
| Low                   | Between 1 week and 1 month |
| Medium                | Less than 1 day            |
| High                  | Less than 1 day            |
| Elite                 | Less than 1 hour           |

## Change failure rate

Number of `deployments` that caused `incidents` / Number of `deployments`.

`deployments` that caused `incidents` is defined as the closest deployment before the incident start. A deployment might be responsible for multiple incidents.

When we aggregate Change failure rate we use the average of a period of time.

!!! note
    We have opted to avoid requiring a relationship between `incidents` and `deployments` or `changes` to simplify the data model reported.

| Performance level[^1] | Value  |
| --------------------- | ------ |
| Low                   | 46-60% |
| Medium                | 0-15%  |
| High                  | 0-15%  |
| Elite                 | 0-15%  |

[^1]: Performance levels are based on the [State of DevOps 2019](https://services.google.com/fh/files/misc/state-of-devops-2019.pdf).
