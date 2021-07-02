# deployment-dispatch-action

A GitHub Action to dispatch a notification to another repo when a deployment happens.

When this action is included in your repository's workflow, the specified target repo will recieve a payload describing the deployment. This can then be used in subsequent workflows. For example, we use this action to notify a downstream module that it's data source has changed so it pulls those changes and republishes itself.

Example:

In your repository:

```yaml
name: trigger a dispatch
on: deployment # this bit is important
jobs:
  deployment-notify:
    name: dispatch on deployment
    runs-on: ubuntu-latest
    timeout-minutes: 5
    steps:
      - uses: sugarandmagic/deployment-dispatch-action@v3
        with:
          organisation-name: name-of-your-org
          repository-name-to-trigger: name-of-your-repo
          private-github-token: ${{ secrets.YOUR_GITHUB_TOKEN }}
```

Then in your downstream repo you can do something with it:

```yaml
name: update on deployment
on: repository_dispatch # this is how you hook onto the dispatched event
jobs:
  update-on-deployment:
    runs-on: ubuntu-latest
    steps: ...etc
```
