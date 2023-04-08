# Prometheus Installation
This guide outlines the steps to install Prometheus using Helm chart from the Prometheus Community repository.

Prerequisites
Kubernetes cluster
Helm 3
Installation
Add Prometheus Community repository:

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts -n kube-prom
Update the repository:

helm repo update -n kube-prom
Pull the Prometheus chart and untar it:

helm pull "prometheus-community/prometheus" --untar
Modify the values.yaml file based on your requirements. You can use the values.yaml file provided in this repository as a starting point.

# Install Prometheus using the Helm chart:


helm upgrade prometheus prometheus-community/prometheus -n kube-prom -f values.yaml --install
Accessing Prometheus
By default, Prometheus is not exposed outside of the cluster. To access the Prometheus UI, you can use kubectl port-forward command to forward the port to your local machine:


kubectl port-forward service/prometheus-kube-prometheus-prometheus 9090:9090 -n kube-prom
Then, you can access the Prometheus UI by navigating to http://localhost:9090 in your web browser.

Uninstallation
To uninstall Prometheus, run the following command:


helm uninstall prometheus -n kube-prom
This will completely remove Prometheus from your cluster.

Conclusion
Prometheus is a powerful tool for monitoring and alerting in Kubernetes environments. With Helm, it is easy to install and configure Prometheus in your cluster.
