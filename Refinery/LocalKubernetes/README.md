# Setup for Collector and Refinary Running in Docker Desktop

## Install Pre-Reqs

```shell
brew tap carvel-dev/carvel
brew install colima helm kustomize colima kapp
```

## Colima
Start...
```shell
colima start --kubernetes --cpu 4 --memory 8
```

Stop...
```shell
colima stop
```

## Secrets
Create a file `02-Secrets.yaml` with values similar to the following:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mastercard-demo-secrets
  namespace: tx-simulator
type: Opaque
stringData:
  ANTHROPIC_API_KEY: <<your Anthropic API key>>
  DASHBOARD_PASSWORD: <<your dashboard password>>
  ADMIN_PASSWORD: <<your admin password>>
  PORT: "8080"
  MAX_LOGIN_ATTEMPTS: "1000"
  OTEL_EXPORTER_OTLP_ENDPOINT: "gateway-opentelemetry-collector.collector.svc.cluster.local:4317"
  OTEL_EXPORTER_OTLP_INSECURE: "true"
---
apiVersion: v1
kind: Secret
metadata:
  name: collector-secrets
  namespace: collector
type: Opaque
stringData:
  HONEYCOMB_TEAM: <<your Honeycomb team injest key>>
```

## Full Deploy with Kapp
This method uses Carvel Kapp to install and track everything with one command. Kapp is also configured to do things in
the correct order.

This method requires kustomize and kapp!

Install everything...
```shell
kustomize build --enable-helm . | kapp deploy -a demo-environment -y -f 00-kapp-config.yaml -f -
```

`00-kapp-config.yaml` orders the rollout: refinery first, then the collector
gateway, then the collector agent and the demo application in parallel. kapp
waits for each stage to become ready before starting the next. It is passed as a
separate `-f` (not listed in `kustomization.yaml`) because kapp consumes it as
configuration and never applies it to the cluster.

To preview without changing anything, swap `-y` for `--diff-run`.

Delete everything...
```shell
kapp delete -a demo-environment
```

## Manual Deploy

### Setup Helm
```shell
helm repo add honeycomb https://honeycombio.github.io/helm-charts
helm repo update honeycomb
helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts
helm repo update open-telemetry
```

### Initial Setup

```shell
kubectl apply -f 01-NameSpaces.yaml
kubectl apply -f 02-Secrets.yaml
kubectl apply -f 11-RulesConfigMap.yaml
```

### Refinery
Install...
```shell
helm install refinery honeycomb/refinery -f 12-refinery-values.yaml -n refinery
```

Uninstall...
```shell
helm uninstall refinery -n refinery
```

### Otel Collector as Gateway
Install...
```shell
helm install gateway open-telemetry/opentelemetry-collector -f 21-values-gateway.yaml -n collector
```

Uninstall...
```shell
helm uninstall gateway -n collector
```

### Otel Collector as Agent
Install...
```shell
helm install agent open-telemetry/opentelemetry-collector -f 31-values-agent.yaml -n collector
```

Uninstall...
```shell
helm uninstall agent -n collector
```

### Transaction Simulator Application

```shell
kubectl apply -f 41-Application.yaml
```
