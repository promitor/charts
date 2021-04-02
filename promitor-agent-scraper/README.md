# Promitor

[Promitor](https://promitor.io/) is an Azure Monitor scraper which makes
 the metrics available for metric systems such as Atlassian Statuspage,
  Prometheus and StatsD.

## TL;DR

```console
helm repo add promitor https://charts.promitor.io/
helm repo update
helm install promitor-agent-scraper promitor/promitor-agent-scraper
```

## Introduction

This chart bootstraps a **Promitor Scraper Agent** deployment on a Kubernetes cluster
using the Helm package manager. It will provide the scraper agent with a Kubernetes
Service so that other Pods can consume it.

## Prerequisites

- Kubernetes v1.9 or above
- Azure Subscription
- Azure AD Application to authenticate with ([docs](https://promitor.io/configuration/#authentication-with-azure-monitor))

## Installing the Chart

To install the chart with the release name `promitor-agent-scraper`:

```console
$ helm install promitor-agent-scraper promitor/promitor-agent-scraper \
               --set azureAuthentication.identity.id='<azure-ad-app-id>' \
               --set azureAuthentication.identity.key='<azure-ad-app-key>' \
               --values /path/to/metric-declaration.yaml
```

The command deploys Prometheus on the Kubernetes cluster with the specified metrics
declaration, for more information see [our documentation](https://promitor.io/deployment/#using-our-helm-chart).

## Uninstalling the Chart

To uninstall/delete the `promitor-agent-scraper` deployment:

```console
helm uninstall promitor-agent-scraper
```

The command removes all the Kubernetes components associated with the chart and
deletes the release.

## Configuration

The following table lists the configurable parameters of the Promitor chart and
their default values.

| Parameter                  | Description              | Default              |
|:---------------------------|:-------------------------|:---------------------|
| `image.repository`  | Repository which provides the image | `ghcr.io/tomkerkhove/promitor-agent-scraper` |
| `image.tag`  | Tag of image to use | None, chart app version is used by default            |
| `image.pullPolicy`  | Policy to pull image | `Always`            |
| `image.pullSecrets`  | ImagePullSecrets for the pod | `[]`            |
| `azureAuthentication.appId`  | [Deprecated] Use `azureAuthentication.identity.id` instead |             |
| `azureAuthentication.appKey`  | [Deprecated] User `azureAuthentication.identity.key` instead |             |
| `azureAuthentication.mode`  | Authentication type to use to authenticate. Options are `ServicePrincipal` (default), `UserAssignedManagedIdentity` or `SystemAssignedManagedIdentity` |             |
| `azureAuthentication.identity.id`  | Id of the Azure AD entity to authenticate with |             |
| `azureAuthentication.identity.key`  | Secret of the Azure AD entity to authenticate with |             |
| `azureAuthentication.identity.binding`  | Aad Pod Identity name, when using `UserAssignedManagedIdentity` or `SystemAssignedManagedIdentity` as mode |             |
| `resourceDiscovery.enabled`  | Indication whether or not resource discovery is required | `false`            |
| `resourceDiscovery.host`  | DNS name or IP address of the Promitor Resource Discovery agent |             |
| `resourceDiscovery.port`  | Port (UDP) address of the Promitor Resource Discovery agent | `80`            |
| `metricSinks.atlassianStatuspage.enabled` | Indication whether or not metrics should be emitted to a StatsD server | `false`|
| `metricSinks.atlassianStatuspage.pageId`  | Id of Atlassian Statuspage page |             |
| `metricSinks.atlassianStatuspage.apiKey`  | API key of Atlassian Statuspage page |             |
| `metricSinks.atlassianStatuspage.systemMetricMapping`  | Mapping of the Atlassian Statuspage system metrics and Promitor metrics. Learn how to configure it [below](#atlassian-statuspage).| None            |
| `metricSinks.prometheusScrapingEndpoint.enabled`  | Indication whether or not metrics should be exposed as a Prometheus scraping endpoint | `true`|
| `metricSinks.prometheusScrapingEndpoint.baseUriPath`  | Path where the scraping endpoint for Prometheus is being exposed | `/metrics`            |
| `metricSinks.prometheusScrapingEndpoint.enableMetricTimestamps`  | Indication whether or not to include timestamp | `true`            |
| `metricSinks.prometheusScrapingEndpoint.metricUnavailableValue`  | Value to report in Prometheus when no metric was found whether or not to include timestamp | `NaN`            |
| `metricSinks.prometheusScrapingEndpoint.enableServiceDiscovery`  | Indication whether or not service discovery with annotations should be enabled ([docs](https://github.com/helm/charts/tree/master/stable/prometheus#scraping-pod-metrics-via-annotations)) | `true`            |
| `metricSinks.prometheusScrapingEndpoint.serviceMonitor.enabled`  | Indication whether or not to create a ServiceMonitor object for [Prometheus Operator](https://github.com/prometheus-operator/prometheus-operator#customresourcedefinitions) | `false`            |
| `metricSinks.prometheusScrapingEndpoint.serviceMonitor.namespace`  | The namespace where the ServiceMonitor should be created |                  |
| `metricSinks.prometheusScrapingEndpoint.serviceMonitor.labels`  | Labels for the ServiceMonitor |       `{}`           |
| `metricSinks.prometheusScrapingEndpoint.serviceMonitor.interval`  | How often Prometheus should scrape  | `60s`            |
| `metricSinks.prometheusScrapingEndpoint.serviceMonitor.timeout`  | Timeout for the scraping operation | `10s`            |
| `metricSinks.statsd.enabled`  | Indication whether or not metrics should be emitted to a StatsD server | `false`|
| `metricSinks.statsd.host`  | DNS name or IP address of StatsD server |             |
| `metricSinks.statsd.port`  | Port (UDP) address of StatsD server | `8125`            |
| `metricSinks.statsd.metricPrefix`  | Prefix that will be added to every metric defined in the metric declaration |             |
| `prometheusRule.enabled`          | Indication whether or not to create PrometheusRule object(s) for [Prometheus operator](https://github.com/prometheus-operator/prometheus-operator#customresourcedefinitions)      | `false`                     |
| `prometheusRule.additionalLabels` | Additional labels that can be used so prometheusRules will be discovered by Prometheus  | `{}`        |
| `prometheusRule.namespace`        | namespace where prometheusRules object should be created          |                             |
| `prometheusRule.rules`            | [rules](https://prometheus.io/docs/prometheus/latest/configuration/alerting_rules/) to be created | `[]` 
| `telemetry.applicationInsights.enabled`  | Indication whether or not to send telemetry to Azure Application Insights | `false`            |
| `telemetry.applicationInsights.logLevel`  | Minimum level of logging for Azure Application Insights |             |
| `telemetry.applicationInsights.key`  | Application Insights instrumentation key |             |
| `telemetry.containerLogs.enabled`  | Indication whether or not to send telemetry to container logs | `true`            |
| `telemetry.containerLogs.logLevel`  | Minimum level of logging for container logs |  |
| `telemetry.defaultLogLevel`  | Minimum level of logging for all telemetry sinks, unless specified otherwise | `Error`            |
| `azureMetadata.tenantId`  | Id of Azure tenant |             |
| `azureMetadata.subscriptionId`  | Id of Azure subscription |             |
| `azureMetadata.resourceGroupName`  | Name of resource group | `promitor`            |
| `metricDefaults.aggregation.interval`  | Default interval which defines over what period measurements of a metric should be aggregated | `00:05:00`            |
| `metricDefaults.scraping.schedule`  | Cron expression that controls the fequency in which all the configured metrics will be scraped from Azure Monitor | `*/5 * * * *`            |
| `metrics`  | List of metrics to scrape configured following the [metric declaration docs](https://promitor.io/configuration/metrics/) |        |
| `rbac.create` | If true, create & use RBAC resources | `true` |
| `rbac.podSecurityPolicyEnabled` | Create pod security policy resources | `false` |
| `rbac.serviceAccount.create` | Create service account resource | `true` |
| `rbac.serviceAccount.name` | Service account name to use if create is false. If create is true, a name is generated using the fullname template | `promitor-scraper` |
| `rbac.serviceAccount.annotations` | Service account annotations| `{}` |
| `health.readiness.enabled`  | Indication if readiness probes should be used | `true`            |   |
| `health.readiness.verifyDependencies`  | Indication if readiness probes should verify if Promitor can interat with external dependencies. Do note that this will contact all dependencies which can have performance impact, cause throttling or cascading failures when consumed very often. | `false`            |   |
| `health.readiness.delay`  | Amount of seconds to wait before probing the container to verify if it's ready | `5` |
| `health.readiness.interval`  | Amount of seconds to wait before probing the container again to verify if it's ready after the last attempt | `5`            |
| `health.readiness.thresholds.failure`  | Number of retries after first failed check before container will be marked `Unready`  | `3` |
| `health.readiness.thresholds.success`  | Minimum consecutive successes for the probe to be considered successful after having failed | `1` |
| `health.readiness.timeoutSeconds`  | Amount of seconds after which the probe times out | `1`  |
| `health.liveness.enabled`  | Indication if liveness probes should be used | `true`            |   |
| `health.liveness.verifyDependencies`  | Indication if liveness probes should verify if Promitor can interat with external dependencies. Do note that this will contact all dependencies which can have performance impact, cause throttling or cascading failures when consumed very often. | `false`            |   |
| `health.liveness.delay`  | Amount of seconds to wait before probing the container to verify if it's still alive | `5`            |   |
| `health.liveness.interval`  | Amount of seconds to wait before probing the container again to verify if it's still alive after the last attempt | `30`            |
| `health.liveness.thresholds.failure`  | Number of retries after first failed check before container restart  | `3` |
| `health.liveness.thresholds.success`  | Minimum consecutive successes for the probe to be considered successful after having failed | `1` |
| `health.liveness.timeoutSeconds`  | Amount of seconds after which the probe times out | `1`  |
| `affinity`  | Affinity settings ([docs](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#affinity-and-anti-affinity)) |    `{}`    |
| `nodeSelector` | Node labels for pod assignment | `{}` |
| `podLabels`  | Additional pod labels to include |    `{}`    |
| `priorityClassName`  | Priority class to be used by pod ([docs](https://kubernetes.io/docs/concepts/configuration/pod-priority-preemption/)) |    `""`    |
| `securityContext.*`  | Custom security context object for pod ([docs](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)) | `{}`            |
| `securityContext.enabled`  | Whether to include custom security context for pod or not | `false`            |
| `tolerations` | Tolerations for pod assignment | `[]` |
| `resources`  | Pod resource requests & limits |    `{}`    |
| `secrets.createSecret`  | Indication if you want to bring your own secret level of logging | `true`            |
| `secrets.appKeySecret`  | Name of the secret for Azure AD identity secret | `azure-app-key`            |
| `service.port`  | Port on service for other pods to talk to | `8888`            |
| `service.targetPort`  | Port on container to serve traffic | `88`            |
| `service.loadbalancer.enabled`  | Indication whether or not to expose service externally through a load balancer | `false`            |
| `service.loadbalancer.azure.dnsPrefix`  | **[Azure Kubernetes Service only]** Prefix for DNS name to expose the service on using `<name>.<location>.cloudapp.azure.com` format. ([docs](https://docs.microsoft.com/en-us/azure/aks/static-ip#apply-a-dns-label-to-the-service)) | ``            |
| `service.loadbalancer.azure.exposeInternally`  | To restrict access to Promitor by exposing it through an internal load balancer. This setting is specific to Azure Kubernetes Service ([docs](https://docs.microsoft.com/en-us/azure/aks/internal-lb)) | `false`            |

Specify each parameter using the `--set key=value[,key=value]` argument to
`helm install`. For example:

```console
$ helm install promitor-agent-scraper promitor/promitor-agent-scraper \
               --set azureAuthentication.identity.id='<azure-ad-app-id>' \
               --set azureAuthentication.identity.key='<azure-ad-app-key>' \
               --set azureMetadata.tenantId='<azure-tenant-id>' \
               --set azureMetadata.subscriptionId='<azure-subscription-id>' \
               --values C:\Promitor\metric-declaration.yaml
```

Alternatively, a YAML file that specifies the values for the above parameters can
be provided while installing the chart. For example,

```console
helm install promitor-agent-scraper promitor/promitor-agent-scraper -f values.yaml
```

### Atlassian Statuspage

You can easily provide system metric mapping between Atlassian Statuspage system metrics and Promitor metrics.

```yaml
systemMetricMapping: []
- id: <atlassian-statuspage-system-metric-id>
  promitorMetricName: <promitor-metric-name>
```

This defines which Promitor metric should be reported as a given Atlassian Statuspage system metrics.
