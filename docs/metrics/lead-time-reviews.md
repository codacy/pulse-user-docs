# Lead time sub-metrics & Reviews metrics

## Lead time for changes sub-metrics
There are several metrics that directly influence **Lead time for changes**, and can help you track in more detail what needs to be improved in your workflow.
All the sub-metrics are aggregated by an interval (day, week, or month) and have values for the average, median, and percentiles 5%, 20%, 25%, 75%, 80%, 95%.

These metrics are based on pull requests and:

-   Include draft pull requests: the lifetime of a pull request includes the time marked as draft;
-   Exclude open pull requests;
-   Exclude declined pull requests;
-   Exclude pull requests without commits;
-   Exclude push-forced pull requests: where the first commit was authored after the pull request was open.

### Time to open

An approximation of the time a change takes to be developed. How long does it take for features to be developed?

This metric is calculated as the time between the first commit in the pull request's branch and the pull request being created.

### Time to review

The time spent in the code review process. Once a pull request is opened, how long does it take to merge?

This metric is calculated as the time between the pull request being created and the pull request being merged.

## Reviews metrics

| Name     | Description                                                                |
| -------- | -------------------------------------------------------------------------- |
| Changes  | The number of lines of code changed in a pull request.                     |
| Commits  | The number of commits in a pull request. Even if pull request is squashed. |
| Reviews  | The number of reviews in a pull request.                                   |
| Comments | The number of comments and review comments in a pull request.              |
