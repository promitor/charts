---
name: release-process
description: Releases Promitor Helm charts. Use when publishing or updating a chart version in this repository.
---

# Release process

Follow these steps for every chart being released:

1. Update both `version` and `appVersion` in the chart's `Chart.yaml`.
2. Lint the chart:
   - Scraper: `helm lint promitor-agent-scraper`
   - Resource Discovery: `helm lint promitor-agent-resource-discovery --values examples/promitor-agent-resource-discovery.config.yaml`
3. Package the chart from the repository root with `helm package <chart-directory>`.
4. Move the generated `.tgz` archive into `docs/`.
5. Regenerate the repository index with `helm repo index docs --url https://charts.promitor.io`.
6. Verify that `docs/index.yaml` contains the new release and preserves previous releases.
7. Create a pull request containing the chart metadata, packaged archive, and regenerated index. Use a semantic pull request title such as `feat: update appVersion to <version> for <chart>`.
8. After the pull request is merged, create a GitHub release using the template in `CONTRIBUTING.md`.

Never edit `docs/index.yaml` manually. Do not replace or remove existing packaged chart releases.
