# Changes and reviews metrics

Changes and reviews metrics provide an extra level of detail about the performance of your team workflows.

!!! important
    You must install the [GitHub integration](../one-click-integrations/github-integration.md) or the [Bitbucket integration](../one-click-integrations/bitbucket-integration.md) for Pulse to calculate the changes and reviews metrics.

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

An estimate of the time it takes for a change or feature to be developed:

$$
t_{open} - t_{firstCommit}
$$

where:

-   $t_{open}$ is the timestamp at which the pull request is open
-   $t_{firstCommit}$ is the timestamp of the first commit to the pull request branch

### Time to merge

The time it takes for a change or feature to be reviewed:

$$
t_{merge} - t_{open}
$$

where:

-   $t_{merge}$ is the timestamp at which the pull request is merged
-   $t_{open}$ is the timestamp at which the pull request is open

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

The time it takes for a pull request to receive the first review:

$$
t_{firstReview} - t_{open}
$$

where:

-   $t_{firstReview}$ is the timestamp of the first review
-   $t_{open}$ is the timestamp at which the pull request is open

### Time to first approval

The time it takes for a pull request to receive the first approval:

$$
t_{firstApproval} - t_{open}
$$

where:

-   $t_{firstApproval}$ is the timestamp of the first approval
-   $t_{open}$ is the timestamp at which the pull request is open

### Time from first to last approval

The time between the first and last approvals on a pull request:

$$
t_{lastApproval} - t_{firstApproval}
$$

where:

-   $t_{lastApproval}$ is the timestamp of the last approval
-   $t_{firstApproval}$ is the timestamp of the first approval

### Time from last approval to merge

The time it takes for a change or feature to be reviewed:

$$
t_{merge} - t_{lastApproval}
$$

where:

-   $t_{merge}$ is the timestamp at which the pull request is merged
-   $t_{lastApproval}$ is the timestamp of the last approval

### Outlier review details

Pulse displays the following details for each pull request in the chart **Review time explorer**:

| Name           | Description                                                               |
| -------------- | ------------------------------------------------------------------------- |
| Time to review | Time to merge a pull request                                              |
| Changes        | Number of lines of code changed in a pull request                         |
| Commits        | Number of commits in a pull request, even if the pull request is squashed |
| Comments       | Number of comments and review comments in a pull request                  |

## See also

-   Report data to Pulse using the [GitHub integration](../one-click-integrations/github-integration.md) or the [Bitbucket integration](../one-click-integrations/bitbucket-integration.md)
