# Work in progress metrics

Pulse calculates the work in progress metrics based on the status of all pull requests targeting any base branch in your repositories.

!!! important
    You must install the [GitHub integration](../one-click-integrations/github-integration.md) or the [Bitbucket integration](../one-click-integrations/bitbucket-integration.md) for Pulse to calculate the work in progress metrics.

## Work in progress

To provide you with an high-level view of the current work in progress of your teams, Pulse groups pull requests that are currently open by the following phases:

-   **Open:** the pull request is open but the team hasn't reviewed it.
-   **Reviewed:** the pull request received at least one review but it isn't approved yet. These reviews include change requests and inline pull request comments, but don't include conversation comments.
-   **Approved:** the pull request received at least one approval.

## Work in progress details

The following metrics for each open pull request allow you to understand which work items are about to become or have become blockers and may need an action to move forward:

-   **Time in progress:** time since that pull request is in progress.
-   **Phase:** current phase of the pull request, can be one of **Open**, **Reviewed**, or **Approved**.
-   **Reviews:** number of reviews of the pull request. Reviews include approvals, change requests, and inline pull request comments, but don't include conversation comments.
-   **Comments:** number of comments of the pull request. Comments include inline pull request comments and conversation comments.
-   **Time stale:** number of days since the pull request had the last interaction.
-   **Changes:** number of lines of code changed by the pull request.

## See also

-   Report data to Pulse using the [GitHub integration](../one-click-integrations/github-integration.md) or the [Bitbucket integration](../one-click-integrations/bitbucket-integration.md)
