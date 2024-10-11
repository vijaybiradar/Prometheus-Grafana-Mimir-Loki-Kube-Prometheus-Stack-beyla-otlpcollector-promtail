# Loki Helm Chart

![Version: 6.16.0](https://img.shields.io/badge/Version-6.16.0-informational)

## Introduction

Loki is a log aggregation system designed to be easy to operate and integrate with other tools such as Grafana. This Helm chart deploys Loki in a Kubernetes cluster.

## Prerequisites

- Kubernetes 1.16+
- Helm 3.0+
- Persistent storage is recommended for Loki.

## Installation

To install Loki using Helm, run:

```bash
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install loki grafana/loki --values values.yaml
Configuration
The values.yaml file contains configuration options that can be customized before installation. Below are the key configurations you might want to modify:

Schema Configuration
yaml
Copy code
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

yaml
Copy code
  ingester:
    chunk_encoding: snappy
Tracing
Enable or disable tracing:

yaml
Copy code
  tracing:
    enabled: true
Storage
Configure S3 bucket details for storing logs:

yaml
Copy code
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

yaml
Copy code
deploymentMode: Distributed
Replicas and Availability
Configure the number of replicas for each component to ensure high availability:

yaml
Copy code
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

yaml
Copy code
minio:
  enabled: false
Single Binary Mode
Disable single binary mode for distributed deployment:

yaml
Copy code
singleBinary:
  replicas: 0
Usage
After the installation, you can check the status of the Loki pods:

bash
Copy code
kubectl get pods -l app=loki
To access the logs, you can integrate Loki with Grafana by adding it as a data source.
