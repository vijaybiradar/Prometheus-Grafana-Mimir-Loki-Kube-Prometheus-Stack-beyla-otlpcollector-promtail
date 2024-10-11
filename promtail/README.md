# Promtail Deployment with Helm

This guide explains how to deploy Promtail using Helm and connect it to a locally deployed Loki server.

## Prerequisites

- Helm installed on your machine
- Loki instance already deployed in your Kubernetes cluster

## Steps to Deploy Promtail

### 1. Add the Grafana Helm repository

Run the following command to add the Grafana Helm charts repository:

```bash
helm repo add grafana https://grafana.github.io/helm-charts
2. Update the Helm repository
Make sure you have the latest version of the Grafana Helm charts by running:


helm repo update
3. Download and untar the Promtail chart
You can download and untar the Promtail Helm chart using the following command:


helm pull grafana/promtail --version 6.11.0 --untar
4. Create the values.yaml configuration file
Create a values.yaml file that contains the configuration needed to connect Promtail to your Loki instance. The example below demonstrates how to configure Promtail to push logs to a locally deployed Loki server.

config:
  clients:
    - url: http://loki-dev-loki-distributed-gateway.loki.svc.cluster.local/loki/api/v1/push
      tls_config:
        insecure_skip_verify: true
5. Install or Upgrade Promtail
Run the following command to install or upgrade Promtail using Helm and the custom configuration from values.yaml:


helm upgrade --install promtail grafana/promtail --version 6.11.0 -f values.yaml
Verifying the Installation
Once the installation is complete, you can check the status of the Promtail pods by running:

kubectl get pods -l app.kubernetes.io/name=promtail
