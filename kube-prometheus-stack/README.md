# This document provides a brief guide on how to deploy kube-prometheus-stack with Helm using the Prometheus community Helm chart repository.

# Prerequisites
Before deploying kube-prometheus-stack, ensure that the following prerequisites are met:

Helm is installed on your Kubernetes cluster
You have added the Prometheus community Helm chart repository to your Helm configuration
Adding the Prometheus community Helm chart repository
To add the Prometheus community Helm chart repository to your Helm configuration, run the following command:


```helm repo add prometheus-community https://prometheus-community.github.io/helm-charts -n kube-prom```
This command adds the Prometheus community Helm chart repository with the name "prometheus-community" and sets the namespace to "kube-prom".

Updating the Helm chart repository
Before deploying kube-prometheus-stack, ensure that you have updated the Helm chart repository to ensure that you are using the latest version of the chart. To update the Helm chart repository, run the following command:


helm repo update -n kube-prom
This command updates the Helm chart repository in the "kube-prom" namespace.

Deploying kube-prometheus-stack
To deploy kube-prometheus-stack with Helm, run the following command:


```helm upgrade kube-prometheus-stack prometheus-community/kube-prometheus-stack --version 41.7.0 -n kube-prom -f values.yaml```
This command deploys kube-prometheus-stack with the following parameters:

kube-prometheus-stack: the name of the release
prometheus-community/kube-prometheus-stack: the name of the Helm chart to use
--version 41.7.0: the version of the Helm chart to use
-n kube-prom: the namespace in which to deploy the chart
-f values.yaml: the name of the values file to use when deploying the chart
