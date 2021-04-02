# Promitor

[Promitor](https://promitor.io/) is an Azure Monitor scraper which makes
 the metrics available for metric systems such as Atlassian Statuspage,
  Prometheus and StatsD.

## TL;DR

```console
helm repo add promitor https://charts.promitor.io/
helm repo update
helm install promitor-agent-resource-discovery promitor/promitor-agent-resource-discovery
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

To install the chart with the release name `promitor-agent-resource-discovery`:

```console
$ helm install promitor-agent-resource-discovery promitor/promitor-agent-resource-discovery \
               --set azureAuthentication.identity.id='<azure-ad-app-id>' \
               --set azureAuthentication.identity.key='<azure-ad-app-key>' \
               --values /path/to/metric-declaration.yaml
```

The command deploys Prometheus on the Kubernetes cluster with the specified metrics
declaration, for more information see [our documentation](https://promitor.io/deployment/#using-our-helm-chart).

## Uninstalling the Chart

To uninstall/delete the `promitor-agent-resource-discovery` deployment:

```console
helm uninstall promitor-agent-resource-discovery
```

The command removes all the Kubernetes components associated with the chart and
deletes the release.

## Configuration

The following table lists the configurable parameters of the Promitor chart and
their default values.

| Parameter                  | Description              | Default              |
|:---------------------------|:-------------------------|:---------------------|
| `image.repository`  | Repository which provides the image | `ghcr.io/tomkerkhove/promitor-agent-resource-discovery` |
| `image.tag`  | Tag of image to use | None, chart app version is used by default            |
| `image.pullPolicy`  | Policy to pull image | `Always`            |
| `azureLandscape.cloud`  | Azure Cloud to discover resources in. Options are `Global` (default), `China`, `UsGov` & `Germany` | `Global`            |
| `azureLandscape.tenantId`  | Id of Azure tenant to discover resources in |             |
| `azureLandscape.subscriptions`  | List of Azure subscription ids to discover resources in | `[]`            |
| `resourceDiscoveryGroups`  | List of resource discovery groups to configured following the [resource discovery declaration docs](https://promitor.io/configuration/v2.x/resource-discovery) |        |
| `azureAuthentication.appId`  | [Deprecated] Use `azureAuthentication.identity.id` instead |             |
| `azureAuthentication.appKey`  | [Deprecated] Use `azureAuthentication.identity.key` instead |             |
| `azureAuthentication.mode`  | Authentication mode option for Azure authentication. Options are `ServicePrincipal` (default), `UserAssignedManagedIdentity` or `SystemAssignedManagedIdentity` |             |
| `azureAuthentication.identity.id`  | Id of the Azure AD entity to authenticate with |             |
| `azureAuthentication.identity.key`  | Secret of the Azure AD entity to authenticate with, when using mode `ServicePrincipal` (default) or `UserAssignedManagedIdentity` |             |
| `azureAuthentication.identity.binding`  | Aad Pod Identity name, when using `UserAssignedManagedIdentity` or `SystemAssignedManagedIdentity` as mode |             |
| `cache.enabled`  | Indication whether or not discovered resources should be cached in-memory to avoid Azure throttling | `true`            |
| `cache.durationInMinutes`  | Amount of minutes to cache discovered resources | `5`            |
| `telemetry.applicationInsights.enabled`  | Indication whether or not to send telemetry to Azure Application Insights | `false`            |
| `telemetry.applicationInsights.logLevel`  | Minimum level of logging for Azure Application Insights |             |
| `telemetry.applicationInsights.key`  | Application Insights instrumentation key |             |
| `telemetry.containerLogs.enabled`  | Indication whether or not to send telemetry to container logs | `true`            |
| `telemetry.containerLogs.logLevel`  | Minimum level of logging for container logs |  |
| `telemetry.defaultLogLevel`  | Minimum level of logging for all telemetry sinks, unless specified otherwise | `Error`            |
| `rbac.create` | If true, create & use RBAC resources | `true` |
| `rbac.podSecurityPolicyEnabled` | Create pod security policy resources | `false` |
| `rbac.serviceAccount.create` | Create service account resource | `true` |
| `rbac.serviceAccount.name` | Service account name to use if create is false. If create is true, a name is generated using the fullname template | `promitor-resource-discovery` |
| `rbac.serviceAccount.annotations` | Service account annotations| `{}` |
| `health.readiness.enabled`  | Indication if readiness probes should be used | `true`            |   |
| `health.readiness.verifyDependencies`  | Indication if readiness probes should verify if Promitor can interat with external dependencies. Do note that this will contact all dependencies which can have performance impact, cause throttling or cascading failures when consumed very often. | `false`            |   |
| `health.readiness.delay`  | Amount of seconds to wait before probing the container to verify if it's ready | `5`            |   |
| `health.readiness.interval`  | Amount of seconds to wait before probing the container again to verify if it's ready after the last attempt | `5`            |   |
| `health.readiness.thresholds.failure`  | Number of retries after first failed check before container will be marked `Unready`  | `3` |
| `health.readiness.thresholds.success`  | Minimum consecutive successes for the probe to be considered successful after having failed | `1` |
| `health.readiness.timeoutSeconds`  | Amount of seconds after which the probe times out | `1`  |
| `health.liveness.enabled`  | Indication if liveness probes should be used | `true`            |   |
| `health.liveness.verifyDependencies`  | Indication if liveness probes should verify if Promitor can interat with external dependencies. Do note that this will contact all dependencies which can have performance impact, cause throttling or cascading failures when consumed very often. | `false`            |   |
| `health.liveness.delay`  | Amount of seconds to wait before probing the container to verify if it's still alive | `5`            |   |
| `health.liveness.interval`  | Amount of seconds to wait before probing the container again to verify if it's still alive after the last attempt | `30`            |   |
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
| `secrets.createSecret`  | Indication if you want to bring your own secret level of logging | `true`            |   |
| `secrets.appKeySecret`  | Name of the secret for Azure AD identity secret | `azure-app-key`            |
| `service.loadbalancer.enabled`  | Indication whether or not to expose service externally through a load balancer | `false`            |
| `service.loadbalancer.azure.dnsPrefix`  | Prefix for DNS name to expose the service on using `<name>.<location>.cloudapp.azure.com` format. This setting is specific to Azure Kubernetes Service ([docs](https://docs.microsoft.com/en-us/azure/aks/static-ip#apply-a-dns-label-to-the-service)) | ``            |
| `service.loadbalancer.azure.exposeInternally`  | To restrict access to Promitor by exposing it through an internal load balancer. This setting is specific to Azure Kubernetes Service ([docs](https://docs.microsoft.com/en-us/azure/aks/internal-lb)) | `false`            |
| `service.port`  | Port on service for other pods to talk to | `8889`            |
| `service.targetPort`  | Port on container to serve traffic | `88`            |

Specify each parameter using the `--set key=value[,key=value]` argument to
`helm install`. For example:

```console
$ helm install promitor-agent-resource-discovery promitor/promitor-agent-resource-discovery \
               --set azureAuthentication.identity.id='<azure-ad-app-id>' \
               --set azureAuthentication.identity.key='<azure-ad-app-key>' \
               --set azureLandscape.tenantId='<azure-tenant-id>' \
               --set azureLandscape.subscriptionId='<azure-subscription-id>' \
               --values C:\Promitor\metric-declaration.yaml
```

Alternatively, a YAML file that specifies the values for the above parameters can
be provided while installing the chart. For example,

```console
helm install promitor-agent-resource-discovery promitor/promitor-agent-resource-discovery -f values.yaml
```
