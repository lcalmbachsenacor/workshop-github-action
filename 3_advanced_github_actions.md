# Advanced Github action configurations

## Optimizing Workflows with Caching

Content such as node modules or other build system can be cached between different executions to run the workflows faster.
Github provides actions to cache dependency based on configurations

The following action caches node modules based on the hash value of the `package-lock.json`. 
When a new dependency or dependency version is referenced, the lock file gets updates.
This invalidates the cache and all dependencies are installed again.

Usage:
```yaml
- name: Cache dependencies
  uses: actions/cache@v4
  with:
      path: node_modules
      key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
      restore-keys: |
        ${{ runner.os }}-node-
```

## Use Variables between steps

You define a container image tage or another variable. This variable needs to be used in another step.
To achieve the usage of a variable in different steps, you put the variable in the Github output and reference it afterwards.

```yaml
  - name: Create timestamp
    id: create_timestamp
    run: |
      CURRENT_DATE=$(date +'%Y-%m-%d')
      echo "current date $CURRENT_DATE"
      echo "current_timestamp=$CURRENT_DATE" >> "$GITHUB_OUTPUT"
  - name: Create image
    run: echo "Create docker image with tag ${{ steps.create_timestamp.outputs.current_timestamp }}"
  - name: Deploy tag
    run: echo "Deploy tag ${{ steps.create_timestamp.outputs.current_timestamp }} to environment"
```

## Workflow Triggers and Contexts

Currently only the ``push`` trigger were used. Github provides additional triggers to run workflows.

Example:
```yaml
on:
  issue_comment:
jobs:
  deploy:
    if: contains(github.event.comment.body, '/deploy')
    runs-on: ubuntu-latest
    steps:
      - name: Run on deploy comment
        run: |
          echo "Deploy to a certain environment"
```

[next >>](./4_composite_actions.md)
