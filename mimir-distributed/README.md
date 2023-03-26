# This document provides a brief guide on how to upgrade Mimir Distributed using Helm.

# Prerequisites
Before upgrading Mimir Distributed, ensure that the following prerequisites are met:

Helm is installed on your Kubernetes cluster
The target namespace "mimir" exists on your Kubernetes cluster
The Mimir Distributed chart version 3.0.0 is available in your Helm repository
A valid "values.yaml" file that defines the desired configuration for Mimir Distributed is available in your local file system.
Upgrading Mimir Distributed
To upgrade Mimir Distributed, run the following command:

``helm -n mimir upgrade mimir-test grafana/mimir-distributed --values=values.yaml --version 3.0.0 --install --debug``
This command upgrades the "mimir-test" release of Mimir Distributed to version 3.0.0, using the values defined in "values.yaml". The "--install" option installs the chart if it doesn't already exist, and the "--debug" option enables verbose logging.

To preview the upgrade before installing it, you can use the "--dry-run" option:


``helm -n mimir upgrade mimir-test grafana/mimir-distributed --values=values.yaml --version 3.0.0 --dry-run --debug``
This command shows what would happen if the upgrade were performed, without actually installing or modifying anything. The "--debug" option enables verbose logging.

Conclusion
In conclusion, this document provides a brief guide on how to upgrade Mimir Distributed using Helm. By following the steps outlined in this guide, you can easily upgrade your Mimir Distributed installation and take advantage of new features and improvements provided by the 3.0.0 version. Make sure to adjust the values in the "values.yaml" file to match your desired configuration. If you want to preview the upgrade before performing it, use the "--dry-run" option.


Here's the equivalent helm --set command for the given values.yaml file:

helm install mimir-test grafana/mimir-distributed \
--set global.extraEnvFrom[0].secretRef.name=prometheusmimir \
--set global.podAnnotations.bucketSecretVersion=0 \
--set metaMonitoring.serviceMonitor.enabled=true \
--set metaMonitoring.serviceMonitor.labels.release=kube-prometheus-stack \
--set mimir.structuredConfig.alertmanager_storage.s3.access_key_id=<username> \
--set mimir.structuredConfig.alertmanager_storage.s3.bucket_name=mimir-alert \
--set mimir.structuredConfig.alertmanager_storage.s3.endpoint=s3.amazonaws.com \
--set mimir.structuredConfig.alertmanager_storage.s3.secret_access_key=<password> \
--set mimir.structuredConfig.blocks_storage.backend=s3 \
--set mimir.structuredConfig.blocks_storage.bucket_store.sync_dir=/data/tsdb-sync \
--set mimir.structuredConfig.blocks_storage.s3.access_key_id=<username> \
--set mimir.structuredConfig.blocks_storage.s3.bucket_name=mimir-prod-north-virginia \
--set mimir.structuredConfig.blocks_storage.s3.endpoint=s3.amazonaws.com \
--set mimir.structuredConfig.blocks_storage.s3.secret_access_key=<password> \
--set mimir.structuredConfig.blocks_storage.tsdb.dir=/data/tsdb \
--set mimir.compactor.data_dir=/data \
--set mimir.frontend.align_queries_with_step=true \
--set mimir.frontend.log_queries_longer_than=10s \
--set mimir.ingester.instance_limits.max_ingestion_rate=0 \
--set mimir.ingester.ring.final_sleep=0s \
--set mimir.ingester.ring.num_tokens=512 \
--set mimir.ingester_client.grpc_client_config.max_recv_msg_size=104857600 \
--set mimir.ingester_client.grpc_client_config.max_send_msg_size=104857600 \
--set mimir.limits.ingestion_rate=40000 \
--set mimir.limits.compactor_blocks_retention_period=2d \
--set mimir.limits.max_global_series_per_metric=0 \
--set mimir.limits.max_global_series_per_user=0 \
--set mimir.limits.compactor_block_upload_enabled=true \
--set mimir.memberlist.abort_if_cluster_join_fails=false \
--set mimir.memberlist.compression_enabled=false \
--set mimir.ruler.alertmanager_url=dnssrvnoa+http://_http-metrics._tcp.{{ template "mimir.fullname" . }}-alertmanager-headless.{{ .Release.Namespace }}.svc.{{ .Values.global.clusterDomain }}/alertmanager \
--set mimir.ruler.enable_api=true \
--set mimir.ruler.rule_path=/data \
--set mimir.ruler_storage.s3.access_key_id=<username> \
--set mimir.ruler_storage.s3.bucket_name=mimir-ruler-test \
--set mimir.ruler_storage.s3.endpoint=s3.amazonaws.com \
--set mimir.ruler_storage.s3.secret_access_key=<password> \
--set mimir.runtime_config.file=/var/{{ include "mimir.name" . }}/runtime.yaml \
--set mimir.server.grpc_server_max_concurrent_streams=1000 \
--set mimir.server.grpc_server_max_recv_msg_size=104857600 \
--set mimir.server.grpc_server_max_send_msg_size=104857600 \
--set minio.enabled=false \
--set querier.replicas=3 \
--set nginx.service.type=LoadBalancer \
--set nginx.service.port=80
Note that you'll need to replace <username> and <password> placeholders
