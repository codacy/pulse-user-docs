# Lead and cycle time metrics

Pulse calculates lead and cycle time based on issue state changes in Jira.

![Lead time versus cycle time](images/lead-cycle-time.png)

-   [Lead time](#lead-time)
-   [Cycle time](#cycle-time)

<!--TODO Some things to take into consideration into the definition of the metrics:

* what happens when an issue is reopened?
* what happens when an issue changes type?
* what happens when an issue changes project?
-->

## Lead time

Time between creating an issue in the backlog and completing the issue.

```text
issue completed timestamp - issue created timestamp
```

## Cycle time

Time between starting to work on an issue and completing the issue. Cycle time is a subpart of lead time.

```text
issue completed timestamp - issue in progress timestamp
```
