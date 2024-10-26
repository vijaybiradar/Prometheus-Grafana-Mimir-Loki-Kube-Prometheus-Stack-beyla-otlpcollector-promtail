# Grafana Alloy Helm Chart Installation

## Prerequisites

Before proceeding with the installation, ensure you have the following:

- **Kubernetes Cluster**: A running Kubernetes cluster (e.g., GKE, EKS, AKS).
- **kubectl**: Install and configure [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) for managing your Kubernetes cluster.
- **Helm**: Install the Helm CLI. Refer to the [Helm installation guide](https://helm.sh/docs/intro/install/) for setup instructions.

## Installation Steps

### Step 1: Add Helm Repositories

Add the required Helm repositories to your local Helm installation by executing:

```bash
helm repo add grafana https://grafana.github.io/helm-charts
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
Step 2: Pull Helm Charts
Download the necessary charts for local customization:

bash
Copy code
helm pull grafana/alloy --version 0.9.2 --untar
helm pull prometheus-community/prometheus-node-exporter --version 4.40.0 --untar
Step 3: Customize Values
Modify the values.yaml file in the alloy directory to suit your deployment needs. Key sections include:

Alloy Configuration: Adjust settings for the ConfigMap, storage paths, and network settings.
Controller Settings: Modify controller types and other parameters as needed.
Service Settings: Configure service type, annotations, and node ports.
RBAC and Service Account: Define role-based access control and service account settings.
Step 4: Install Grafana Alloy
Deploy Grafana Alloy with the specified configuration:

bash
Copy code
helm install alloy grafana/alloy -n alloy --create-namespace -f values.yaml
Step 5: Install Prometheus Node Exporter
Deploy the Prometheus Node Exporter in the same namespace:

bash
Copy code
helm install node-exporter prometheus-community/prometheus-node-exporter -n alloy --create-namespace
Step 6: Verify Installation
To confirm that Grafana Alloy and the Node Exporter are running correctly, check the status of the pods in the alloy namespace:

bash
Copy code
kubectl get pods -n alloy
Additional Information
Logging: Check the logs of your pods if you encounter any issues using the following command:
bash
Copy code
kubectl logs <pod-name> -n alloy
Uninstalling the Chart: If you need to uninstall Grafana Alloy, use the command:
bash
Copy code
helm uninstall alloy -n alloy
