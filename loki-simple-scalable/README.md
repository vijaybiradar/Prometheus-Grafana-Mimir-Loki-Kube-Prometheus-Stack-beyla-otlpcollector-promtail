## loki-simple-scalable

This is a Helm chart for deploying Loki, a horizontally-scalable, highly-available log aggregation system inspired by Prometheus.

Installation
To install the chart with default values:


``helm install loki grafana/loki-simple-scalable --namespace loki --create-namespace``
To install the chart with custom values:


``helm install loki grafana/loki-simple-scalable -f values.yaml --namespace loki --create-namespace``
To upgrade the chart:


``helm upgrade loki grafana/loki-simple-scalable -f values.yaml --namespace loki --version 1.8.11 --install --debug``
To preview the upgrade before installing it, you can use the "--dry-run" option:


``helm upgrade loki grafana/loki-simple-scalable -f values.yaml --namespace loki --version 1.8.11 --install --debug --dry-run``
To download the chart locally:


``helm pull grafana/loki-simple-scalable --version 1.8.11 --untar``
Uninstallation
To uninstall the chart:


``helm uninstall loki --namespace loki``
Configuration
The chart can be configured using the values.yaml file or by specifying individual parameters using the --set flag. For example:


``helm install loki grafana/loki-simple-scalable --namespace loki --create-namespace --set service.type=LoadBalancer``
The following table lists the configurable parameters of the chart and their default values:

## Parameter	Description	Default
image.repository	Image repository	grafana/loki
image.tag	Image tag	2.4.1
image.pullPolicy	Image pull policy	IfNotPresent
service.type	Kubernetes service type	ClusterIP
service.port	Kubernetes service port	3100
config.loki	Loki configuration	loki-config.yaml
config.tableManager	Table manager configuration	table-manager-config.yaml
config.auth	Authentication configuration	auth-config.yaml
config.customConfigMap	Custom configuration to be added to config map	nil
config.customConfigMapKey	Key under which custom configuration is added to config map	nil
config.overwriteConfig	Overwrite existing configuration	false
config.relabelConfigs	List of relabeling configurations	[]
persistence.enabled	Enable persistence	false
persistence.storageClassName	Storage class name	standard
persistence.accessModes	Access modes for persistent volume claim	[ReadWriteOnce]
persistence.size	Size of persistent volume claim	10Gi
resources	Resource requests and limits	{}
nodeSelector	Node selector	{}
tolerations	Tolerations	[]
affinity	Affinity	{}
For more information on Loki and its configuration options, please refer to the official [Loki documentation](https://grafana.com/docs/loki
