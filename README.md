# dispatch-action

A GitHub Action to dispatch a notification to another repo when a *some event* (e.g. a deployment) happens.

When this action is included in your repository's workflow, the specified target repo will recieve a payload describing the event. This can then be used in subsequent workflows. For example, at DAZN we use this action to notify a downstream module that it's data source has changed so it pulls those changes and republishes itself.

Example:

In your repository:

```yaml
name: trigger a dispatch
on: deployment # this bit is important - its the event that you want to be the trigger
jobs:
  deployment-notify:
    name: dispatch on deployment
    runs-on: ubuntu-latest
    timeout-minutes: 5
    steps:
      - uses: sugarandmagic/dispatch-action@main
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
    steps: 
      run: |
        echo "GitHub Event: ${{ github.event_name }} ${{ github.event.pull_request.head.sha }}" # ...do stuff with your payload
```
