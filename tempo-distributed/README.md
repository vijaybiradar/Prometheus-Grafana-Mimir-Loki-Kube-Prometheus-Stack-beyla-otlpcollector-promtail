# Tempo Distributed Helm Chart

This repository contains the Helm chart to deploy Tempo Distributed version `1.18.1` on a Kubernetes cluster.

## Prerequisites

- Kubernetes 1.22+
- Helm 3.9+
- An S3 bucket for trace storage (or another supported storage backend)

## Install the Tempo Distributed Helm Chart

### Add the Grafana Helm Repository

Before installing Tempo Distributed, add the Grafana Helm repository and update your Helm repositories:

```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
```
Pull the Tempo Distributed Helm Chart
You can pull the Helm chart to review or customize the values.yaml configuration locally:

```
helm pull grafana/tempo-distributed --version 1.18.1 --untar
```
Install Tempo Distributed
Use the customized override.yaml file to install the Tempo Distributed Helm chart in your Kubernetes cluster:

```
helm install tempo-distributed grafana/tempo-distributed \
  --version 1.18.1 \
  --namespace tempo-distributed \
  --values override.yaml \
  --create-namespace
```
Verify Installation
After installation, verify the Tempo Distributed deployment:

```
kubectl get pods -n tempo-distributed
```
