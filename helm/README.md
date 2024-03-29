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

## Writing Helm Charts

Creating structure to be customized

```bash
helm create example-chart
```

Checking for errors in the chart

```bash
helm lint ./example-chart
```

Checking for errors in templating

```bash
helm template ./example-chart
helm template release-name ./example-chart
helm template release-name ./example-chart --debug
```

Doing a Dry Run for checking for errors in K8s

```bash
helm install release-name ./example-chart --dry-run
```

## Functions

### String functions

```bash
# .Values.image.repository == my-image
{{ upper .Values.image.repository}} # MY-IMAGE
{{ quote .Values.image.repository}} # "my-image"
{{ replace "-" "_" .Values.image.repository}} # my_image
{{ shuffle .Values.image.repository}} # "ema-gmyi"
```

More: 

[Template Function List](https://helm.sh/docs/chart_template_guide/function_list/#string-functions)

### More function types

- [Cryptographic and Security](https://helm.sh/docs/chart_template_guide/function_list/#cryptographic-and-security-functions)
- [Date](https://helm.sh/docs/chart_template_guide/function_list/#date-functions)
- [Dictionaries](https://helm.sh/docs/chart_template_guide/function_list/#dictionaries-and-dict-functions)
- [Encoding](https://helm.sh/docs/chart_template_guide/function_list/#encoding-functions)
- [File Path](https://helm.sh/docs/chart_template_guide/function_list/#file-path-functions)
- [Kubernetes and Chart](https://helm.sh/docs/chart_template_guide/function_list/#kubernetes-and-chart-functions)
- [Logic and Flow Control](https://helm.sh/docs/chart_template_guide/function_list/#logic-and-flow-control-functions)
- [Lists](https://helm.sh/docs/chart_template_guide/function_list/#lists-and-list-functions)
- [Math](https://helm.sh/docs/chart_template_guide/function_list/#math-functions)
- [Network](https://helm.sh/docs/chart_template_guide/function_list/#network-functions)
- [Reflection](https://helm.sh/docs/chart_template_guide/function_list/#reflection-functions)
- [Regular Expressions](https://helm.sh/docs/chart_template_guide/function_list/#regular-expressions)
- [Semantic Versions](https://helm.sh/docs/chart_template_guide/function_list/#semantic-version-functions)
- [String](https://helm.sh/docs/chart_template_guide/function_list/#string-functions)
- [Type Conversion](https://helm.sh/docs/chart_template_guide/function_list/#type-conversion-functions)
- [URL](https://helm.sh/docs/chart_template_guide/function_list/#url-functions)
- [UUID](https://helm.sh/docs/chart_template_guide/function_list/#uuid-functions)

full list: [https://helm.sh/docs/chart_template_guide/function_list/](https://helm.sh/docs/chart_template_guide/function_list/)

### Default Value

If the value is not present in the values.yaml fals  into a defult value

```bash
{{ default "default-image" .Values.image.repository}}
```

## Pipelines

Pipe functions

```bash
# .Values.image.repository == my-image
{{ .Values.image.repository | upper | quote}} # "MY-IMAGE"
```

## Conditionals

template

```yaml
apiVersion: v1
kind : Service
metadata
	name: {{ .Release.Name }}-nginx
	{{- if .Values.orgLabel }}
	labels:
		org: {{ .Values .orgLabel }}
{{- end }}
...
```

output if {{ .Values .orgLabel }} exisits:

```yaml
apiVersion: v1
kind : Service
metadata
	name: RELEASE-NAME-nginx
	labels:
		org: payroll
...
```

output if {{ .Values .orgLabel }} does not exisits:

```yaml

apiVersion: v1
kind : Service
metadata
	name: RELEASE-NAME-nginx
...
```

if/if else…/else

```yaml
apiVersion: v1
kind : Service
metadata
	name: {{ .Release.Name }}-nginx
	{{- if empty .Values.orgLabel }}
	labels:
		org: default-org
	{{- else if eq .Values.orgLabel "eng" }}
	labels:
		org: engineering
	{{- else }}
	labels:
		org: {{ .Values.orgLabel }}
{{- end }}
...
```

| Function | Purpose |
| --- | --- |
| eq | equal |
| ne | not equal |
| lt | less than |
| le | less than or equal to |
| gt | greater than |
| not | negation |
| empty | value is empty |

## Scopes and Loops

Scopes uses With Blocks

```yaml
{{- with .Values.serviceAccount.create }}
apiVersion: v1
kind: ServiceAccount
metadata:
  name: {{ .Values.serviceAccount.name }}
  labels:
    app: webapp-color
{{- end }}
```

Loops

```yaml
{{- with .Values.serviceAccount.create }}
apiVersion: v1
kind: ServiceAccount
metadata:
  name: {{ $.Values.serviceAccount.name }}
  labels:
    {{- range $.Values.serviceAccount.labels }}
    tier: {{ . }}
    {{- end }}
    app: webapp-color
{{- end }}
```

## Named Template

![Untitled](images/Untitled.png)

![Untitled](images/Untitled%201.png)

## Chart Hooks

Normal flow in helm

![Untitled](images/Untitled%202.png)

pre-upgrade hook

![Untitled](images/Untitled%203.png)

another hooks

![Untitled](images/Untitled%204.png)

Creating Hooks

![Untitled](images/Untitled%205.png)

Ordering Hooks in phase with weight

![Untitled](images/Untitled%206.png)

Policies according hook status

![Untitled](images/Untitled%207.png)