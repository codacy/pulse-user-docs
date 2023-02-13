# Lead and cycle time metrics

Monitoring you team's lead time and cycle time allows you to understand if you're improving the ability to deliver value to customers. These productivity metrics indicate how long it takes for work to flow through the software development process:

-   **[Lead time](#lead-time):** the time it takes to go from a customer making a request to the request being satisfied. You can use lead time as an indication of your organization’s time to market.
-   **[Cycle time](#cycle-time):** the time it takes for your team to complete work items once they begin actively working on them.

![Lead time versus cycle time](images/lead-cycle-time.png)

Use these metrics to monitor the results of investing in DevOps practices and tackling technical debt, compare, and quantify the performance of your teams. These metrics enable you to objectively communicate to stakeholders how long your Engineering team takes to address customer requests or defects.

Read more on [how you can improve your time to market](https://blog.codacy.com/how-lead-time-can-improve-your-time-to-market/){: target="_blank"}.

!!! note
    Pulse calculates lead time and cycle time based on the state changes of issues **that are already closed** in Jira, independently of the resolution. This means that:

    -   **If you reopen** a completed issue, Pulse stops considering the issue while calculating the metrics. If the issue is completed again, Pulse takes into account the last completed timestamp.

    -   **If you change the type or the project** of a completed issue, Pulse reflects the change on the dashboards, transitioning the issue history to the new project if relevant.
    
        Note that this change doesn't affect the overall calculation of the lead time or cycle time metrics.

## Lead time

Time between creating an issue in the backlog and completing the issue.

Pulse calculates lead time for completed issues, taking into account that issues can transition from **Completed** back to another status.

The time it takes for a change or feature to be reviewed:

$$
t_{rev}=T_{LC} - T_C
$$

where:

-   $T_{LC}$ is the timestamp at which the issue is last marked as completed
-   $T_C$ is the timestamp at which the issue is created

## Cycle time

Time between committing to work on an issue (such as at the start of a sprint) and completing the issue. Cycle time is a subpart of lead time.

Pulse calculates cycle time for completed issues, taking into account that issues can transition from **In progress** back to another status besides **Completed**.

!!! note
    When calculating cycle time, Pulse considers that:

    -   Issues are in progress when they transition to any Jira status belonging to the **In Progress** status category (represented by the blue color in Jira).
    -   Issues that have a status belonging to the **To Do** status category (represented by the grey color in Jira) don't contribute to cycle time.

$$
\sum_n T_{Xn}-T_{Nn}
$$

where:

-   $T_{Xn}$ is the timestamp at which the issue exits the in progress status for the $n$-th time
-   $T_{Nn}$ is the timestamp at which the issue enters the in progress status for the $n$-th time

## See also

-   Report data to Pulse using the [Jira integration](../one-click-integrations/jira-integration.md)
