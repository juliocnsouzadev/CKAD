# Helm

## Installation

```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```

## Components

- Online Charts Repo → [artifacthub.io](http://artifacthub.io)
- CLI
    - State Metadata → saved in the k8s cluster as a secret
- Releases → k8s cluster
    - Revisions

```bash
helm install [release-name][chart-name]
```

## Charts

- templates → k8s components: service.yaml, deployment.yaml, etc as templates (variable values {{…}} )
- values → yaml file containing template values
- chart → yaml file application config

## Helm CLI

The Kubernetes package manager
Common actions for Helm:

- helm search: search for charts
- helm pull: download a chart to your local directory to view
- helm install: upload the chart to Kubernetes
- helm list: list releases of charts
Usage:
helm [command]
Available Commands:
completion → generate autocompletion scripts for the specified shell
create → create a new chart with the given name
dependency → manage a chart's dependencies
env → helm client environment information
get → download extended information of a named release
help → Help about any command
history → fetch release history

## Customizing Parameters

```bash
helm install my-release bitnami/wordpress 
--set wordpressBlogName="Helm Tutorials"
--set wordpressEmail="john@example.com"

helm install --values custom-values.yaml my-release bitnami/wordpress
```