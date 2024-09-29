# Grafana Beyla Helm Chart

## Overview

**Grafana Beyla** is a powerful observability tool designed for collecting metrics, logs, and traces from your applications and infrastructure. This Helm chart simplifies the deployment of Beyla in a Kubernetes cluster, enabling you to monitor your applications efficiently.

## Prerequisites

- **Kubernetes Cluster**: Ensure you have a running Kubernetes cluster (v1.16+ recommended).
- **Helm**: Install [Helm](https://helm.sh/docs/intro/install/) (v3.0+).
- **kubectl**: Install [kubectl](https://kubernetes.io/docs/tasks/tools/) to interact with your Kubernetes cluster.

## Quick Start

1. **Add the Grafana Helm Repository**:
 ```
   helm repo add grafana https://grafana.github.io/helm-charts
   helm repo update
```
Download and Untar the Chart:

```
helm pull grafana/beyla --version 1.8.0 --untar
```

install or Upgrade Beyla:Deploy Beyla using the following command:

```
helm upgrade --install beyla grafana/beyla --version 1.8.0 -f values.yaml
```
