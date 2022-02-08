# Lead and cycle time metrics

Monitoring you team's lead time and cycle time allows you to understand if you're improving the ability to deliver value to customers.

![Lead time versus cycle time](images/lead-cycle-time.png)

-   [Lead time](#lead-time)
-   [Cycle time](#cycle-time)

!!! note
    Pulse calculates lead time and cycle time based on the state changes of issues **that are already closed** in Jira, independently of the resolution. This means that:

    -   **If you reopen** a completed issue, Pulse stops considering the issue while calculating the metrics. If the issue is completed again, Pulse takes into account the last completed timestamp.

    -   **If you change the type or the project** of a completed issue, Pulse reflects the change on the dashboards, transitioning the issue history to the new project if relevant.
    
        Note that this change doesn't affect the overall calculation of the lead time or cycle time metrics.

## Lead time

Time between creating an issue in the backlog and completing the issue.

```text
last issue completed timestamp - issue created timestamp
```

## Cycle time

Time between commiting to work on an issue (such as at the start of a sprint) and completing the issue. Cycle time is a subpart of lead time.

```text
last issue completed timestamp - issue in progress timestamp
```
