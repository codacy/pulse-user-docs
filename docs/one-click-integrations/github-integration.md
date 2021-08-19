# GitHub integration

Pulse integrates directly with GitHub to receive data about changes and deployments, necessary to calculate the metrics:

-   [Lead time for changes](../metrics/accelerate.md#lead-time-for-changes), including the following drill-down metrics:

    -   [Lead time for changes sub-metrics](../metrics/lead-time-reviews.md#lead-time-for-changes-sub-metrics)

    -   [Review metrics](../metrics/lead-time-reviews.md#review-metrics)

    -   [Work in progress metrics](../metrics/work-in-progress.md)

-   [Deployment frequency](../metrics/accelerate.md#deployment-frequency)

## Setting up the GitHub integration

To set up the GitHub integration:

1.  On Pulse, [expand **Integrations** and select **GitHub**](https://app.pulse.codacy.com/integrations/github){: target="_blank"}.

1.  Click **Install GitHub App** and follow the instructions on the GitHub UI to install the app on your organization.

    !!! important
        You can only install the Pulse GitHub App on an organization and not on your personal account.

    ![Installing the Pulse GitHub App](images/ghi-installing.png)

1.  Wait until you get a confirmation that Pulse successfully created the integration and the webhook on GitHub.

    ![Pulse GitHub integration set up successfully](images/ghi-ok.png)

    If there was an error please [contact support](mailto:pulsesupport@codacy.com).

## Configuring how Pulse detects deployments {: id="deployment-detection-strategy"}

Optionally, the Pulse GitHub integration can use one of two strategies to automatically detect and measure deployments in your repositories:

-   **Pull requests merged to default branch** (default strategy)

    -   Pulse considers a deployment every pull request that **targets the default branch** of the repository.
    -   The deployment date is the timestamp when the corresponding pull request is merged.
    -   The set of changes in a deployment is the list of commits in the corresponding pull request. Pulse correctly tracks your changes even if you squash the commits when merging the pull request.

-   **Git tags following the SemVer specification**

    -   Pulse considers a deployment every Git tag that follows the [SemVer](https://semver.org) convention, excluding pre-release versions but allowing release prefixes. For exmple, the following are valid tags: `1.0.0`, `v2.3.4`.

        To use this strategy, make sure that you're creating Git tags on your repositories for each successful deployment to production, or whenever you make a new release available to any user of your application:

        ```bash
        git tag -a MAJOR.MINOR.PATCH -m "<Deployment or release message>"
        ```

        Where `MAJOR.MINOR.PATCH` must be a valid SemVer version without pre-release information.

    -   The deployment date is either the creation date of [annotated tags](https://git-scm.com/book/en/v2/Git-Basics-Tagging#_annotated_tags) or the timestamp when Pulse receives the webhook calls for [lightweight tags](https://git-scm.com/book/en/v2/Git-Basics-Tagging#_lightweight_tags).

        Keep in mind that since webhook calls can be delayed, the deployment date on Pulse could be imprecise and impact the metric **Lead time for changes**.

    -   The set of changes that belong to a deployment is the list of commits between the tag of that deployment and the previous tag. Because of this, Pulse discards:

        -   The first SemVer tag in the repository since there is no previous tag to compare with.
        -   Any tag that does not have a common ancestor (commit) with its previous tag, since Pulse cannot obtain the changes between them.

If the automatic deployment detection is turned off, Pulse doesn't detect deployments using GitHub events and you must report deployments using the Pulse CLI or the API. This is useful if none of the automatic deployment detection strategies match your workflow and you must have control over the way Pulse tracks your deployments.

To configure the strategy that Pulse uses to detect deployments:

1.  On Pulse, [expand **Integrations** and select **GitHub**](https://app.pulse.codacy.com/integrations/github){: target="_blank"}.

    Make sure that you have already installed the GitHub App.

1.  Choose the strategy that fits best your workflows, or turn off the automatic deployment detection.

    ![Choosing a deployment triggering strategy](images/ghi-strategy.png)

## Collected data

The table below lists the data that the GitHub integration collects from your GitHub organization, together with:

-   The mapping between the data collected from GitHub and the [Pulse data model](https://ingestion.pulse.codacy.com/v1/api-docs#tocs_event)
-   The metrics that Pulse calculates from the data to display on the dashboards

<table>
<thead>
<tr>
<th><strong>Data collected from GitHub</strong></th>
<th><strong>Mapping to Pulse data model</strong></th>
<th><strong>Used in</strong></th>
</tr>
</thead>
<tbody>
<tr>
    <td>Pull request commits</td>
    <td>
        <p>Changes:</p>
        <ul>
            <li><code>change_id</code>: commit UUID</li>
            <li><code>time_created</code>: commit author date<sup><a href="#commit-author-date">1</a></sup></li>
            <li><code>system</code>: repository name</li>
        </ul>
    </td>
    <td>Lead time for changes on the <a href="../../metrics/accelerate/">Accelerate dashboard</a></td>
</tr>
<tr>
    <td>Pull requests, git tags, or none (<a href="#deployment-detection-strategy">configurable</a>)</td>
    <td>
        <p>Deployments:</p>
        <ul>
            <li><code>deploy_id</code>: pull request ID</li>
            <li><code>system</code>: repository name</li>
        </ul>
    </td>
    <td>Deployment frequency and Change failure rate on the <a href="../../metrics/accelerate/">Accelerate dashboard</a></td>
</tr>
<tr>
    <td>Pull requests</td>
    <td>
        -
    </td>
    <td><a href="../../metrics/lead-time-reviews/">Lead time and reviews dashboard</a>,<br/><a href="../../metrics/work-in-progress/">Work in progress dashboard</a></td>
</tr>
<tr>
    <td>Teams</td>
    <td>
        <p>Deployments:</p>
        <ul>
            <li><code>teams</code>: GitHub teams responsible for the changes in the deployment</li>
        </ul>
    </td>
    <td>Filters for the <a href="../../metrics/accelerate/">Accelerate dashboard</a>, <a href="../../metrics/lead-time-reviews/">Lead time and reviews dashboard</a>, and <a href="../../metrics/work-in-progress/">Work in progress dashboard</a></td>
</tr>
</table>

<sup><span id="commit-author-date">1</span></sup>: Pulse uses the commit author's date since it is more accurate. The committer date can be changed (e.g.: rebases) and stop reflecting the real creation date of the change.
