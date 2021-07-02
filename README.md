# dispatch-on-event

A GitHub Action to dispatch a notification to another repo when _some event_ (e.g. a deployment) happens.

When this action is included in your repository's workflow, the specified target repo will recieve a payload describing the event. This can then be used in subsequent workflows. For example, at DAZN we use this action to notify a downstream module that it's data source has changed so it pulls those changes and republishes itself.

A list of events that may be used to trigger the action can be found [here](https://docs.github.com/en/actions/reference/events-that-trigger-workflows).

Example: Notify repo b when repo a is deployed

In your repository:

```yaml
name: trigger a dispatch
on: deployment # this bit is important - its the event that you want to be the trigger. see above for other options
jobs:
  dispatch-on-event:
    name: dispatch on deployment
    runs-on: ubuntu-latest
    timeout-minutes: 5
    steps:
      - uses: sugarandmagic/dispatch-on-event@latest
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
        echo "GitHub Event: ${{ github.event_name }} ${{ github.event.pull_request.head.sha }}" 
        # ...do stuff with your payload
```
