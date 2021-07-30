# Accelerate metrics

Pulse calculates the four key Accelerate metrics using strictly **changes**, **deployments**, and **incidents** data for a rolling window of the last 90 days:

-   [Deployment frequency](#deployment-frequency)
-   [Lead time for changes](#lead-time-for-changes)
-   [Median time to recover](#median-time-to-recover)
-   [Change failure rate](#change-failure-rate)

## Deployment frequency

How often your organization completes a deployment to production or releases code to end-users of your primary application or service:

```text
number of deployments per day
```

Pulse uses the average value over a period of time to display aggregate deployment frequencies.

Pulse determines your performance level for this metric as follows:

| Performance level[^1] | Deployment frequency                                                  |
| --------------------- | --------------------------------------------------------------------- |
| Elite                 | More than 50% of days have one or more **deployments**                |
| High                  | More than 50% of weeks have 3 or more days with a **deployment**      |
| Medium                | More than 50% of weeks have at least one **deployment**               |
| Low                   | More than 50% of months have at least one **deployment**              |

## Lead time for changes

How long it takes a commit to get into production on your primary application or service:

```text
median(deployment timestamp - changes timestamp)
```

!!! note
    The **changes** timestamp is when code is actually checked into a repository.

Pulse uses the maximum value over a period of time to display aggregate lead time for changes.

Pulse determines your performance level for this metric as follows:

| Performance level[^1] | Lead time for changes         |
| --------------------- | ----------------------------- |
| Elite                 | Less than 1 day               |
| High                  | Between 1 day and 1 week      |
| Medium                | Between 1 week and 1 month    |
| Low                   | More than or equal to 1 month |

## Median time to recover

How long it takes your organization to recover from a failure in production (e.g., service impairment or unplanned outage):

```text
median(incident resolved timestamp - incident created timestamp)
```

Pulse uses the maximum value over a period of time to display aggregate median times to recover.

Pulse determines your performance level for this metric as follows:

| Performance level[^1] | Median time to recover       |
| --------------------- | ---------------------------- |
| Elite                 | Less than 1 hour             |
| High, Medium [^2]     | Less than 1 day              |
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

Pulse determines your performance level for this metric as follows:

| Performance level[^1]    | Change failure rate  |
| ------------------------ | -------------------- |
| Elite, High, Medium [^2] | 0-15%                |
| Low                      | 15-100%              |

[^1]: Performance levels are based on the [State of DevOps 2019](https://services.google.com/fh/files/misc/state-of-devops-2019.pdf).
[^2]: Means are not significantly different based on Tukey's post hoc analysis ([State of DevOps 2019](https://services.google.com/fh/files/misc/state-of-devops-2019.pdf) pp. 18).