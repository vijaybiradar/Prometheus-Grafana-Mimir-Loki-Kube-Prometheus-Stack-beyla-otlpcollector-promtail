# Kubernetes Observability using Grafana-Mimir, Loki, Jaeger, Kube-Prometheus-Stack#


#Introduction

Kubernetes is a powerful tool for managing and orchestrating containerized applications at scale. However, as applications become more complex, it can be challenging to maintain visibility into their performance and behavior. This is where observability comes in. Observability is the practice of gathering and analyzing data from a system to understand its behavior and performance. In the context of Kubernetes, observability tools can help you monitor and troubleshoot your applications to ensure they are running smoothly.

In this guide, we will explore four popular observability tools that you can use to monitor your Kubernetes cluster: Grafana-Mimir, Loki, Jaeger, and Kube-Prometheus-Stack.
Tools
# Grafana-Mimir

Grafana-Mimir is an open-source observability platform that provides a unified view of your system's metrics, logs, and traces. It integrates with a wide range of data sources, making it easy to aggregate and visualize data from across your Kubernetes cluster.
# Loki

Loki is a horizontally scalable, highly available log aggregation system that is designed to be both cost-effective and easy to operate. It was built specifically for cloud-native environments like Kubernetes, and it integrates seamlessly with Grafana-Mimir.
# Jaeger

Jaeger is an open-source, end-to-end distributed tracing system that is designed to help you troubleshoot complex applications. It provides visibility into the flow of requests through your system, making it easy to pinpoint issues and optimize performance.
# Kube-Prometheus-Stack

Kube-Prometheus-Stack is a collection of Kubernetes manifests, Grafana dashboards, and Prometheus rules that are designed to provide comprehensive monitoring for your Kubernetes cluster. It includes pre-configured dashboards for a wide range of Kubernetes components, making it easy to get started with monitoring.
Setup

To get started with these tools, you will need a Kubernetes cluster. You can use a local development cluster like minikube or a cloud-based solution like Amazon EKS or Google Kubernetes Engine. Once you have a cluster set up, you can follow these steps to get started with observability:

    Install Grafana-Mimir: Grafana-Mimir can be installed via Helm, a package manager for Kubernetes. You can find instructions for installing Grafana-Mimir in the official documentation here.

    Install Loki: Loki can also be installed via Helm. You can find instructions for installing Loki in the official documentation here.

    Install Jaeger: Jaeger can be installed via Helm as well. You can find instructions for installing Jaeger in the official documentation here.

    Install Kube-Prometheus-Stack: Kube-Prometheus-Stack can be installed via Helm as well. You can find instructions for installing Kube-Prometheus-Stack in the official documentation here.

    Configure data sources: Once you have installed these tools, you will need to configure them to work together. You can configure Grafana-Mimir to use Loki and Jaeger as data sources by following the instructions in the official documentation here.

    Explore dashboards: Once you have configured these tools, you can start exploring the pre-configured dashboards that come with Kube-Prometheus-Stack. These dashboards provide a wealth



#Step by Step Guide


Clone the grafana helm charts repository from https://github.com/grafana/helm-charts. If you open it in an IDE like VS Code, you will see a folder structure similar to this


2. Clone the prometheus helm charts repository from https://github.com/prometheus-community/helm-charts/



3. Now that we have the helm charts downloaded, let’s create a separate Kubernetes Namespace for our setup.

kubectl create ns observability
kubectl get ns
4. The next step is to start installing the helm charts one by one. We will start with Loki first. Open the charts directory in grafana’s helm-charts folder. Open the folder loki-stack, it will be similar to the one shown below.


5. Update the helm dependencies as we need to install promtail along with Loki’s installation. Run the following commands to update the helm dependencies of loki-stack

cd loki-stack/
helm dependencies update
6. Run this command to install loki and promtail in the observability namespace

cd loki-stack && helm install loki . --namespace observability
7. Next step is to install Tempo to collect traces. Similar to loki-stack, open the tempo folder in the charts directory of grafana’s helm charts. You will see a folder structure similar to this


8. Run this command to install tempo in the observability namespace

cd tempo && helm install tempo . --namespace observability
9. Our next step is to install Cortex. Cortex can be run in two modes – a single instance, single process mode or a distributed mode. The single instance mode is for testing out in local environments while the distributed mode is for production setups

10. Follow the steps in this getting started guide to install Cortex as a single instance, single process: https://cortexmetrics.io/docs/getting-started/

Cortex in Production

For setting up Cortex in production, use the Helm charts here: https://github.com/cortexproject/cortex-helm-chart

11. Now that we have Loki, Tempo and Cortex installed, the next step is to install prometheus, grafana and configure Prometheus to push metrics to Cortex. Prometheus has provided a combined chart for both of them called kube–prometheus-stack

We need to make few changes before we can install this chart. We need to add Cortex, Loki and Tempo as data sources to grafana so that we can view logs and traces

12. To add Loki, Cortex and Tempo as data sources, navigate to the kube-prometheus-stack directory and open up the values.yaml file. Add the following under “additionalDataSources“

prometheus:
 prometheusSpec:
   remoteWrite:
     - url: http://cortex.observability.svc.cluster.local:80/api/prom/push
 
grafana:
 additionalDataSources:
   - name: Loki
     type: loki
     url: http://loki.observability.svc.cluster.local:3100
     access: proxy
     jsonData:
         derivedFields:
           - datasourceUid: tempo
             matcherRegex: ((\d+|[a-z]+)(\d+|[a-z]+)(\d+|[a-z]+)(\d+|[a-z]+)(\d+|[a-z]+)(\d+|[a-z]+)(\d+|[a-z]+)(\d+|[a-z]+)(\d+|[a-z]+)(\d+|[a-z]+)(\d+|[a-z]+))
             name: TraceID
             url: $${__value.raw}
         maxLines: 1000
     version: 1
   - name: Tempo
     type: tempo
     uid: tempo
     url: http://tempo.observability.svc.cluster.local:3100
     access: proxy
     version: 1
   - name: Cortex
     type: prometheus
     uid: cortex
     url: http://cortex.observability.svc.cluster.local:80/api/prom
     access: proxy
     version: 1
Note that in the above configuration, we use Prometheus’ remoteWrite to send metrics data to Cortex endpoint

Once added your values.yaml file should look like below


13. Now, run this command to install kube-prometheus-stack in the observability namespace

cd kube-prometheus-stack && helm install kube-prometheus-stack . --namespace observability
14. To validate the entire setup check if all the pods are in running state. Run this command to view all pods in observability namespace

kubectl get pods -n observability
You should see something similar to this as the output.


15. Finally run grafana to view the logs and traces for an application. To do so run the following command to start the grafana service

kubectl port-forward service/kube-prometheus-stack-grafana -n observability 3000:80
16. Once port-forwarding starts, open http://localhost:3000 to access grafana ui. Login to grafana using the following credentials

Username: admin
Password: prom-operator
17. After successful login, navigate to the configuration section where you should see the 4 data sources(Cortex, Loki, Prometheus and Tempo) added


18. Now, lets deploy a sample application to test the setup. Create a Kubernetes “Deployment” yaml file with the contents below. Name the file as hotrod-dep.yaml

# Hotrod Deployment Configuration
 
apiVersion: apps/v1
kind: Deployment
metadata:
 labels:
   app: hotrod
 name: hotrod
spec:
 replicas: 1
 selector:
   matchLabels:
     app: hotrod
 template:
   metadata:
     labels:
       app: hotrod
   spec:
     containers:
       - args:
           - all
         env:
           - name: JAEGER_AGENT_HOST
             value: tempo.observability
           - name: JAEGER_AGENT_PORT
             value: '6831'
           - name: JAEGER_SAMPLER_TYPE
             value: const
           - name: JAEGER_SAMPLER_PARAM
             value: '1'
         image: jaegertracing/example-hotrod:latest
         name: hotrod
         ports:
           - containerPort: 8080
     restartPolicy: Always
19. Create a Kubernetes Service yaml file with the contents below. Name the file as hotrod-svc.yaml

# Hotrod Service Configuration
 
apiVersion: v1
kind: Service
metadata:
 labels:
   app: hotrod
 name: hotrod
spec:
 ports:
   - name: 'http'
     port: 8080
     targetPort: 8080
 selector:
   app: hotrod
 type: ClusterIP
20. Apply the above Deployment and Service yaml configuration using the following command

kubectl apply -f hotrod-dep.yaml -n observability
kubectl apply -f hotrod-svc.yaml -n observability
21. Once deployed, now open grafana and navigate to the explore section and select the datasource as Loki. Now query for the logs of hotrod application like shown below


22. Let’s send some logs from our sample application and query them in grafana. Run the below command to port-forward the sample application that was deployed

kubectl port-forward service/hotrod -n observability 3001:8080
23. On your browser, navigate to http://localhost:3001 and you will see the sample application as below. Click on the buttons few times to send some requests to the application


24. Once the requests are sent, switch to grafana and navigate go to the explore section to query for new logs again. You will see the latest logs with trace ids similar to the one shown below


25. Click on any one of the log lines to see an expanded view. You will see a auto detected field called TraceID which is the extracted trace_id from the logs

26. Now you can navigate to the traces by clicking on the link next to TraceID. This will open up the Tempo UI for you to view the trace information. You should be seeing something similar to the screenshot below


As you may appreciate now, the ability to directly jump from logs to traces helps you in troubleshooting your applications quickly. This is where the integration between Grafana, Loki and Tempo comes in handy.

27. For metrics, you can visualize by querying the Cortex datasource. PromQL queries will just continue to work fine as-is with Cortex


28. Now that you are able to query metrics, logs and traces in one place, you can also create a unified dashboard. You can import this sample dashboard JSON in grafana to create a dashboard like below. The dashboards shows metrics, logs with quick navigation to Loki and Tempo.


29. Finally, you can also check your S3 buckets to find Cortex, Loki and Tempo creating respective chunks and index data. Your buckets should contain data as shown below


Cortex Data in S3

Loki Data in S3

Tempo Data in S3
And that’s it. With the above setup, you are now able to see monitoring, logging and tracing in one place and troubleshoot issues quickly.

Few Things to Remember
If you are using a separate namespace for your observability services, make sure you give the correct service endpoint URL with namespaces in data source configuration. For example:
http://loki.default:3100  -> loki in default namespace
http://tempo.tempo:3100 -> tempo in tempo namespace
Make sure the sample application sends traces to Tempo. Configure it as shown in the Deployment.yaml created in Step 16 where JAEGER_AGENT_HOST is set up to the tempo address and port. If this is not configured properly no traces will be seen
Standalone Promtail configuration escapes the log line twice which causes issues in extracting trace ids from logs. So, loki-stack has been used to avoid promtail configuration issues as loki-stack deploys the bundled loki with pre-configured promtail
Cleaning Up
To delete the resources that were created through the above steps, run the following commands:

kubectl delete -f hotrod-dep.yaml -n observability

kubectl delete -f hotrod-svc.yaml -n observability

helm uninstall loki tempo promtail kube-prometheus-stack --namespace observability

