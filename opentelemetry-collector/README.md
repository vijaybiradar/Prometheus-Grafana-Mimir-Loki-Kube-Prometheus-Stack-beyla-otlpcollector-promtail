# OpenTelemetry Collector Helm Chart Installation

This repository contains Helm charts for deploying the [OpenTelemetry Collector](https://github.com/open-telemetry/opentelemetry-collector) in a Kubernetes cluster. The OpenTelemetry Collector is a powerful tool used to collect telemetry data (metrics, logs, and traces) from applications and infrastructure.

## Prerequisites

Before deploying the OpenTelemetry Collector, ensure that your environment meets the following prerequisites:

- Kubernetes version: `1.24+`
- Helm version: `3.9+`

## Step 1: Add the OpenTelemetry Helm Repository

First, add the OpenTelemetry Helm repository to your Helm configuration and update it to fetch the latest charts:

```
helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts
helm repo update
```
Step 2: Pull the OpenTelemetry Collector Helm Chart
To review or customize the values.yaml configuration locally, pull the OpenTelemetry Collector Helm chart for version 0.107.0:

```
helm pull open-telemetry/opentelemetry-collector --version 0.107.0 --untar
```
This will download and extract the chart into your current working directory.

Step 3: Customize the Chart (Optional)
If you need to customize the configuration, you can modify the values.yaml file inside the extracted chart directory. This file allows you to set custom configurations for receivers, processors, exporters, and pipelines in the OpenTelemetry Collector.

Step 4: Install the OpenTelemetry Collector Using Helm
After customizing the chart (if necessary), install the OpenTelemetry Collector into your desired Kubernetes namespace. You can use a custom value.yaml file to override the default configuration.

```
helm install otel-collector open-telemetry/opentelemetry-collector \
  --version 0.107.0 \
  --namespace otel-collector \
  --values value.yaml \
  --create-namespace
```
