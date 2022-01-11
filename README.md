# ui-preview-deploy-action

## Inputs

- `project` (required) - ID of your project.
- `github-token` (required) - Determinate which user creates sticky comment on PRs, usually it's a bot.
- `s3-key-id` (required) - Key ID for uploading to S3.
- `s3-secret-key` (required) - Secret key for uploading to S3.

## Example

```yaml
name: Preview Deploy

on:
  workflow_run:
    workflows: ['Preview Build']
    types:
    - completed

jobs:
  deploy:
    name: Deploy
    if: >
      github.event.workflow_run.event == 'pull_request' &&
      github.event.workflow_run.conclusion == 'success'
    runs-on: ubuntu-latest
    steps:
    - uses: yandex-cloud/ui-preview-deploy-action@master
      with:
        project: uikit
        github-token: ${{ secrets.YC_UI_BOT_GITHUB_TOKEN }}
        s3-key-id: ${{ secrets.STORYBOOK_S3_KEY_ID }}
        s3-secret-key: ${{ secrets.STORYBOOK_S3_SECRET_KEY }}
```
