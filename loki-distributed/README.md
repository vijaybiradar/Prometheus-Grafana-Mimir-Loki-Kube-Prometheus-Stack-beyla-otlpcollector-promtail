# Loki Distributed Helm Chart

This is the Helm chart for deploying Loki in a distributed mode. Loki is a horizontally-scalable, highly-available, multi-tenant log aggregation system inspired by Prometheus.

## Pre-Requisites

- Kubernetes 1.16+
- Helm 3+
- [Helm](https://helm.sh/docs/intro/install/) installed and configured

## Installation

1. Add the Loki Helm repository:

   ```bash
   helm repo add grafana https://grafana.github.io/helm-charts
   helm repo update
Install the chart:

bash
Copy code
helm install loki-distributed grafana/loki-distributed --version <version> --namespace <namespace> --create-namespace
Optionally, specify your custom values file:

bash
Copy code
helm install loki-distributed grafana/loki-distributed --version <version> --namespace <namespace> --create-namespace -f values.yaml
Configuration
The Helm chart comes with default configurations for Loki's components. You can override these by creating a values.yaml file or by using Helm's --set flag.

Example values.yaml
yaml
Copy code
loki:
  structuredConfig:
    ingester:
      max_transfer_retries: 0
      chunk_idle_period: 1h
      chunk_target_size: 1536000
      max_chunk_age: 1h
    storage_config:
      aws:
        access_key_id: your-aws-access-key-id
        secret_access_key: your-aws-secret-access-key
        bucketnames: your-s3-bucket-name
        endpoint: s3.amazonaws.com
      boltdb_shipper:
        shared_store: s3
    compactor:
      shared_store: s3
    schema_config:
      configs:
        - from: 2020-09-07
          store: boltdb-shipper
          object_store: aws
          schema: v11
          index:
            prefix: loki_index_
            period: 24h
Storage Configuration
You need to configure Loki to store logs either in an object store like AWS S3 or GCS, or on persistent volumes (local storage).

For AWS S3:

yaml
Copy code
storage_config:
  aws:
    access_key_id: your-access-key
    secret_access_key: your-secret-key
    bucketnames: your-s3-bucket
    endpoint: s3.amazonaws.com
Additional Configuration
For more advanced configuration options, refer to the Loki documentation:

Loki Configuration
Loki Documentation
Upgrading
To upgrade the chart, use:

bash
Copy code
helm upgrade loki-distributed grafana/loki-distributed --version <version> -f values.yaml --namespace <namespace>
Uninstallation
To uninstall the chart, use:

bash
Copy code
helm uninstall loki-distributed --namespace <namespace>
