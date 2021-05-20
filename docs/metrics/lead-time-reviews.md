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

An approximation of the time a change takes to be developed. How long does it take for features to be developed?

This metric is calculated as the time between the first commit in the pull request's branch and the pull request being created.

### Time to merge

The time spent in the code review process. Once a pull request is opened, how long does it take to merge?

This metric is calculated as the time between the pull request being created and the pull request being merged.

## Review metrics

| Name     | Description                                                                |
| -------- | -------------------------------------------------------------------------- |
| Changes  | Number of lines of code changed in a pull request.                         |
| Commits  | Number of commits in a pull request, even if the pull request is squashed. |
| Reviews  | Number of reviews in a pull request.                                       |
| Comments | Number of comments and review comments in a pull request.                  |

### Time to first/last approve

The time it takes to have approves. Once a pull request is opened, how long does it take to have an approve and how long does it take to the last approve?

These metrics are calculated as the time between the pull request being created and the first and last approves being submitted before the merge of the pull request.

### Time to first/last review

The time it takes to have reviews. Once a pull request is opened, how long does it take to have a review and how long does it take to the last review?

These metrics are calculated as the time between the pull request being created and the first and last reviews being submitted before the merge of the pull request.

Reviews include approves, change requests, and inline pull request comments, but don't include pull request conversation comments.
