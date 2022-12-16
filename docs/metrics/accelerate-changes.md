# Changes and reviews metrics

Changes and reviews metrics provide an extra level of detail about the performance of your team workflows.

## Changes metrics

The following metrics directly influence **Lead time for changes**, and can help you track in more detail what needs to be improved in your workflow:

-   [Time to open](#time-to-open)
-   [Time to merge](#time-to-merge)

Pulse aggregates these metrics by week and presents values for the average and the 75% and 95% percentiles.

!!! note
    Pulse calculates these metrics based on pull request data and:

    -   Includes pull requests targeting all branches
    -   Includes draft pull requests, meaning that the lifetime of pull requests includes the time marked as draft
    -   Excludes open and declined pull requests
    -   Excludes pull requests without commits and force pushed pull requests where the first commit was authored after the pull request was open

### Time to open

An approximation of how long it takes for a change or feature to be developed:

```text
pull request open timestamp - first commit in pull request branch timestamp
```

### Time to merge

How long does the code review process take for a change or feature:

```text
pull request merge timestamp - pull request open timestamp
```

## Reviews metrics

The following metrics provide insights into the level of engagement and efficiency of your code review process:

-   [Time to first review](#time-to-first-review)
-   [Time to first approval](#time-to-first-approval)
-   [Time from first to last approval](#time-from-first-to-last-approval)
-   [Time from last approval to merge](#time-from-last-approval-to-merge)
-   [Outlier review details](#outlier-review-details)

!!! note
    Pulse calculates these metrics based on pull request data and:

    -   Considers that reviews are approvals, change requests, and inline pull request comments, but not pull request conversation comments
    -   Excludes reviews in a pending state
    -   Excludes reviews and approvals that are done after the pull requests are merged

### Time to first review

How long does it take to have the first review on a pull request:

```text
first review timestamp - pull request open timestamp
```

### Time to first approval

How long does it take to have the first approval on a pull request:

```text
first approval timestamp - pull request open timestamp
```

### Time from first to last approval

How long does it take between the first and last approvals on a pull request:

```text
last approval timestamp - first approval timestamp
```

### Time from last approval to merge

How long does it take to merge a pull request after the last approval:

```text
pull request merge timestamp - last approval timestamp
```

### Outlier review details

Pulse displays the following details for each pull request in the chart **Review time explorer**:

| Name           | Description                                                               |
| -------------- | ------------------------------------------------------------------------- |
| Time to review | Time to merge a pull request                                              |
| Changes        | Number of lines of code changed in a pull request                         |
| Commits        | Number of commits in a pull request, even if the pull request is squashed |
| Comments       | Number of comments and review comments in a pull request                  |

## See also

Report data to Pulse using the following integrations:

-   [GitHub integration](../one-click-integrations/github-integration.md)
