Contributing to Promitor Helm Charts
===

Thanks for helping making Promitor better!

## Shipping a new version

You can easily release a new Helm chart version:

1. Update the version of the Helm chart in `Chart.yaml`
2. Package the Helm chart
```shell
$ helm package promitor-agent-scraper
Successfully packaged chart and saved it to: C:\Code\GitHub\charts\promitor-agent-scraper-0.1.0.tgz
```

3. Move the new chart to the docs folder
```shell
$ mv promitor-agent-scraper-*.tgz docs
```

4. Re-index the Helm repo to add our new version
```shell
$ helm repo index docs --url https://charts.promitor.io
```