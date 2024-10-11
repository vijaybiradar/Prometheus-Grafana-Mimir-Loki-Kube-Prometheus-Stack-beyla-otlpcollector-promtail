# Loki Helm Chart

![Version: 6.16.0](https://img.shields.io/badge/Version-6.16.0-informational)

## Introduction

Loki is a log aggregation system designed to be easy to operate and integrate with other tools such as Grafana. This Helm chart deploys Loki in a Kubernetes cluster.

## Prerequisites

- Kubernetes 1.16+
- Helm 3.0+
- Persistent storage is recommended for Loki.

## Installation

### Step 1: Add the Grafana Helm Repository

To add the Grafana Helm repository, run:

```bash
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
Step 2: Pull the Loki Helm Chart
You can pull the Loki Helm chart version 6.16.0 and extract it with the following command:


helm pull grafana/loki --version 6.16.0 --untar
Step 3: Install Loki
To install Loki using Helm, run:


helm install loki grafana/loki --values values.yaml
Configuration
The values.yaml file contains configuration options that can be customized before installation. Below are the key configurations you might want to modify:

Schema Configuration

loki:
  schemaConfig:
    configs:
      - from: 2024-04-01
        store: tsdb
        object_store: s3
        schema: v13
        index:
          prefix: loki_index_
          period: 24h
Ingesters
Adjust the ingester settings, including the chunk encoding:


  ingester:
    chunk_encoding: snappy
Tracing
Enable or disable tracing:


  tracing:
    enabled: true
Storage
Configure S3 bucket details for storing logs:


  storage:
    type: s3
    bucketNames:
      chunks: "<INSERT S3 BUCKET NAME FOR CHUNKS>"
      ruler: "<INSERT S3 BUCKET NAME FOR RULER>"
      admin: "<INSERT S3 BUCKET NAME FOR ADMIN>"
    s3:
      endpoint: <your-aws-endpoint>
      region: <your-region>
      accessKeyId: <your-access-key-id>
      secretAccessKey: <your-secret-access-key>
      signatureVersion: v4
Deployment Mode
Set the deployment mode to Distributed:


deploymentMode: Distributed
Replicas and Availability
Configure the number of replicas for each component to ensure high availability:


ingester:
  replicas: 3
querier:
  replicas: 3
queryFrontend:
  replicas: 2
distributor:
  replicas: 3
MinIO Configuration
Disable MinIO if you are using S3:


minio:
  enabled: false
Single Binary Mode
Disable single binary mode for distributed deployment:


singleBinary:
  replicas: 0
Usage
After the installation, you can check the status of the Loki pods:


kubectl get pods -l app=loki
To access the logs, you can integrate Loki with Grafana by adding it as a data source.
