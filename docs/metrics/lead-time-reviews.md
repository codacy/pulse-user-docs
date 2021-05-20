# Lead time and review metrics

## Lead time for changes sub-metrics

The following metrics directly influence **Lead time for changes**, and can help you track in more detail what needs to be improved in your workflow:

-   [Time to open](#time-to-open)
-   [Time to merge](#time-to-merge)

Pulse aggregates these metrics by week and presents values for the average and the 5%, 25%, 75%, and 95% percentiles.

!!! note
    Pulse calculates these metrics based on pull request data and:

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

## Review metrics

### Time to first/last approve

The time it takes to have approves. Once a pull request is opened, how long does it take to have an approve and how long does it take to the last approve?

These metrics are calculated as the time between the pull request being created and the first and last approves being submitted before the merge of the pull request.

### Time to first/last review

The time it takes to have reviews. Once a pull request is opened, how long does it take to have a review and how long does it take to the last review?

These metrics are calculated as the time between the pull request being created and the first and last reviews being submitted before the merge of the pull request.

Reviews include approves, change requests, and inline pull request comments, but don't include pull request conversation comments.

### Outlier review details

Pulse displays the following details for each pull request in the chart **Review time explorer**:

| Name           | Description                                                               |
| -------------- | ------------------------------------------------------------------------- |
| Time to review | Time to merge a pull request                                              |
| Changes        | Number of lines of code changed in a pull request                         |
| Commits        | Number of commits in a pull request, even if the pull request is squashed |
| Comments       | Number of comments and review comments in a pull request                  |
