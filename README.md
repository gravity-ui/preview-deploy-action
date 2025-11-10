# preview-deploy-action

A GitHub action that deploys Storybook's static files to S3.

This action is a wrapper around the generic `deploy-to-s3-action` that is preconfigured for deploying Storybook builds to S3 and creating PR comments with preview links.

It's the second action of the sequence "Build -> Deploy".

## Inputs

- `project` (required) - ID of your project.
- `github-token` (required) - PAT of a GitHub user which maintains PR's deploy comment, usually it's a bot.
- `s3-key-id` (required) - S3 access key ID for uploading to S3.
- `s3-secret-key` (required) - S3 secret access key for uploading to S3.

## Usage Example

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
      - uses: gravity-ui/preview-deploy-action@v1
        with:
          project: uikit
          github-token: ${{ secrets.YC_UI_BOT_GITHUB_TOKEN }}
          s3-key-id: ${{ secrets.STORYBOOK_S3_KEY_ID }}
          s3-secret-key: ${{ secrets.STORYBOOK_S3_SECRET_KEY }}
```

## How It Works

1. Downloads the `static` artifact from the triggering workflow
2. Extracts the PR number from the artifacts
3. Uploads the Storybook to S3 at `s3://storybook-static/{project}/pulls/{pr-id}/`
4. Creates a comment on the PR with a link to the preview

## Notes

This action uses the `deploy-to-s3-action` with the following preconfigured values:
- Source path: `static`
- Bucket: `storybook-static`
- Endpoint URL: `https://storage.yandexcloud.net`
- Preview base URL: `https://preview.gravity-ui.com`
- Preview URL path: `{project}/{pr}/` (default, no "pulls" in URL path)
- Region: `ru-central1`
- Comment message: `[Preview]({url}) is ready.`
- Final URL format: `https://preview.gravity-ui.com/{project}/{pr-id}/`

## Related Actions

- `deploy-to-s3-action` - Generic deploy action (used internally)
- `preview-upload-to-s3-action` - S3 upload wrapper for Storybook

## License

MIT
