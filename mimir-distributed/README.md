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
