# Bitbucket integration

!!! note
    (CUSTOMER FACING MESSAGE NEEDED) We have launched Bitbucket integration to receive your feedback. Some metrics are not available yet.

Pulse integrates with Bitbucket Cloud to receive data about changes and deployments, necessary to calculate the metrics:

-   [Lead time for changes](../metrics/accelerate.md#lead-time-for-changes)

-   [Deployment frequency](../metrics/accelerate.md#deployment-frequency)

## Setting up the Bitbucket integration

!!! note
    -   The Bitbucket integration connects to a specific [workspace](https://support.atlassian.com/bitbucket-cloud/docs/what-is-a-workspace/). You must be the **owner** of that Bitbucket workspace to proceed with the integration setup.
    -   [See below](#bb-permissions) the permissions that Pulse requires from your Bitbucket account.  

To set up the Bitbucket integration:

1.  On Pulse, [expand **Integrations** and select **Bitbucket**](https://app.pulse.codacy.com/integrations/bitbucket){: target="_blank"}.

1.  Click **Install Bitbucket App** and follow the instructions on the Bitbucket UI to install the app on your account.

    (IMAGE)

1.  Wait until you get a confirmation that Pulse successfully created the integration and the webhook on Bitbucket.
            
    (IMAGE)

    If there was an error please [contact support](mailto:pulsesupport@codacy.com).

1.  Choose the workspace that you want to connect the integration. You will only be able to select a workspace that you are the owner.

    (IMAGE)

    If there was an error please [contact support](mailto:pulsesupport@codacy.com).

1.  Click **Complete setup**.

## Automatic deployment detection using merged pull requests {: id="deployment-detection-merged-pr"}

!!! note
    Pulse will use your merged pull requests to automatically detect your deployments. Other strategies to detect deployments will be available with a future version of Pulse.

-   Pulse considers a deployment every merged pull request that **targets the default branch** of the repository.
-   The deployment date is the timestamp when the corresponding pull request is merged.
-   The set of changes in a deployment is the list of commits in the corresponding pull request. Pulse correctly tracks your changes even if you [squash or fast-forward the commits when merging the pull request](https://support.atlassian.com/bitbucket-cloud/docs/merge-a-pull-request/#Merge-strategies), since Pulse processes all the original commits before any changes to the Git history.
-   Pulse associates all Bitbucket user groups of the author of a merged pull request with the corresponding deployment, excluding user groups with less than two members. Pulse only takes changes to Bitbucket teams into account on pull requests merged after those changes.

## Collected data

The table below lists the data that the Bitbucket integration collects from your Bitbucket workspace, together with:

-   The mapping between the data collected from Bitbucket and the [Pulse data model](https://ingestion.pulse.codacy.com/v1/api-docs#tocs_event)
-   The metrics that Pulse calculates from the data to display on the dashboards

<table>
<thead>
<tr>
<th><strong>Data collected from Bitbucket</strong></th>
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
    <td>Lead time for changes on the <a href="../../metrics/accelerate/">Accelerate Overview dashboard</a></td>
</tr>
<tr>
    <td>Pull requests</td>
    <td>
        <p>Deployments:</p>
        <ul>
            <li><code>deploy_id</code>: pull request ID</li>
            <li><code>system</code>: repository name</li>
        </ul>
    </td>
    <td>Deployment frequency and Change failure rate on the <a href="../../metrics/accelerate/">Accelerate Overview dashboard</a></td>
</tr>
<tr>
    <td>Teams</td>
    <td>
        <p>Deployments:</p>
        <ul>
            <li><code>teams</code>: Bitbucket teams of the author of a deployment<sup><a href="#deployment-teams">2</a></sup></li>
        </ul>
    </td>
    <td>Filters the <a href="../../metrics/accelerate/">Accelerate Overview dashboard</a> by the contributions made by the team</td>
</tr>
</table>

<sup><span id="commit-author-date">1</span></sup>: Pulse uses the commit author's date since it is more accurate. The committer date can be changed (e.g.: fast-forward) and stop reflecting the real creation date of the change.

<sup><span id="deployment-teams">2</span></sup>: Adding a new team or changing the composition of a team on Bitbucket only affects new data from that moment onwards and doesn't have an immediate impact on the dashboards. Also, deleted teams on Bitbucket will continue to show in Pulse.

## Which permissions does Pulse need from Bitbucket? {: id="bb-permissions"}

Pulse requests only the necessary permissions from Bitbucket to collect changes and deployment data from the repositories in your organization and [keeps your information secure](https://security.codacy.com/). See below the detailed list of permissions.

<table>
  <colgroup>
    <col width="20%"/>
    <col width="20%"/>
    <col width="60%"/>
  </colgroup>
  <thead>
    <tr>
      <th>Scope</th>
      <th>Permissions</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td colspan="3"><strong>Repository permissions:</strong></td>
    </tr>
    <tr>
      <td>Pull requests</td>
      <td>Read</td>
      <td>Pulse retrieves pull request information to calculate several metrics presented on the dashboards. <a href="#collected-data">See the details here.</a></td>
    </tr>
    <tr>
      <td>Issues</td>
      <td>Read</td>
      <td>?Pulse retrieves issue information to get the top-level comments of pull requests.?</td>
    </tr>
    <tr>
      <td>Webhooks</td>
      <td>Read & Write</td>
      <td>Pulse creates webhooks to track new or deleted repositories, and the status of the integration.<br/>Pulse also creates webhooks subscribed to the following repository events as a trigger to gather the corresponding data in real time:
      <ul>
        <li><code>pull_request</code>: pull requests creation, edition, and deletion</li>
      </ul></td>
    </tr>
    <tr>
      <td colspan="3"><strong>Workspace permissions:</strong></td>
    </tr>
    <tr>
      <td>Projects</td>
      <td>Read</td>
      <td>Pulse retrieves the settings and the repositories of the projects within your workspaces to ....</td>
    </tr>
    <tr>
      <td>Teams</td>
      <td>Read</td>
      <td>Pulse retrieves information about teams membership to filter metrics and for billing purposes.</td>
    </tr>
    <tr>
      <td colspan="3"><strong>Account permissions:</strong></td>
    </tr>
    <tr>
      <td>?Account information?</td>
      <td>Read</td>
      <td>Pulse retrives ...</td>
    </tr>
  </tbody>
</table>

## See also

-   [Accelerate metrics](../metrics/accelerate.md)
