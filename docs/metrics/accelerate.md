# Accelerate metrics

Pulse calculates the four key Accelerate metrics using strictly **changes**, **deployments**, and **incidents** data for a rolling window of the last 90 days:

-   [Deployment frequency](#deployment-frequency)
-   [Lead time for changes](#lead-time-for-changes)
-   [Time to recover](#time-to-recover)
-   [Change failure rate](#change-failure-rate)

## Deployment frequency

How often your organization completes a deployment to production or releases code to end-users of your primary application or service:

```text
number of deployments per day
```

Pulse uses the average value over a period of time to display aggregate deployment frequencies.

!!! note
    Due to the way Pulse calculates deployment frequency, the absence of data will have an impact on reducing the value of this metric.

    For example, suppose that you started developing a project 3 months ago and your deployment frequency over this time period was 2 deployments/day. If you increase the observed time interval to 6 months to include a time period before there were deployments, Pulse displays a deployment frequency of only 1 deployment/day.

Pulse determines your performance level for this metric as follows:

| Performance level[^1] | Deployment frequency                                        |
| --------------------- | ----------------------------------------------------------- |
| Elite                 | More than 50% of days have one or more **deployments**      |
| High                  | More than 50% of months have at least one **deployment**    |
| Medium                | More than 50% of semesters have at least one **deployment** |
| Low                   | Less than 50% of semesters have at least one **deployment** |

## Lead time for changes

How long it takes a commit to get into production on your primary application or service:

```text
median(deployment timestamp - changes timestamp)
```

!!! note
    The **changes** timestamp is when code is actually checked into a repository.

Pulse uses the maximum value over a period of time to display aggregate lead time for changes.

Pulse determines your performance level for this metric as follows:

| Performance level[^1] | Lead time for changes          |
| --------------------- | ------------------------------ |
| Elite                 | Less than 1 hour               |
| High                  | Between 1 hour and 1 week      |
| Medium                | Between 1 week and 6 months    |
| Low                   | More than or equal to 6 months |

## Time to recover

How long it takes your organization to recover from a failure in production (e.g., service impairment or unplanned outage):

```text
average(incident resolved timestamp - incident created timestamp)
```

Pulse uses the maximum value over a period of time to display aggregate times to recover.

Pulse determines your performance level for this metric as follows:

| Performance level[^1] | Time to recover              |
| --------------------- | ---------------------------- |
| Elite                 | Less than 1 hour             |
| High                  | Less than 1 day              |
| Medium                | Less than 1 week             |
| Low                   | More than or equal to 1 week |

## Change failure rate

Percentage of deployments causing a failure in production (e.g., service impairment or unplanned outage) and that subsequently require remediation:

```text
number of deployments that caused incidents / total number of deployments
```

Pulse uses the average value over a period of time to display aggregate change failure rates.

!!! note
    We decided to avoid requiring a relationship between **incidents** and **deployments** or **changes** to simplify the data model reported.

    As such, Pulse considers the **deployment** that caused an **incident** the closest deployment before the start of that **incident**. The same deployment might be responsible for multiple incidents.

    Pulse calculates the performance metrics per system and later aggregates the metrics by time interval when displaying them. This means that you need both deployments and incidents for Pulse to correctly map the system between the two types of events and for the metrics to work.
Pulse determines your performance level for this metric as follows:

| Performance level[^1] | Change failure rate |
| --------------------- | ------------------- |
| Elite                 | 0-15%               |
| High                  | 15-30%              |
| Medium                | N/A[^2]             |
| Low                   | 30-100%             |

[^1]: Performance levels are based on the [Accelerate State of DevOps 2021](https://services.google.com/fh/files/misc/state-of-devops-2021.pdf) report.
[^2]: The [Accelerate State of DevOps 2021](https://services.google.com/fh/files/misc/state-of-devops-2021.pdf) report defines the same range of values for both Medium and Low performance levels, so we've opted to skip the Medium level.

## See also

Report data to Pulse using the following integrations:

-   [GitHub integration](../one-click-integrations/github-integration.md)
-   [PagerDuty integration](../one-click-integrations/pagerduty-integration.md)
-   [Pulse CLI](../cli/cli.md)
