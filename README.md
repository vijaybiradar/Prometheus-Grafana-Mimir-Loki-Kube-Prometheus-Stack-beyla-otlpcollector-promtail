# Grafana-Mimir-Loki-Jaeger-Kube-Prometheus-Stack

Kubernetes Observability Using Grafana-Mimir, Loki, Jaeger, Kube-Prometheus-Stack


Enter Loki, Cortex and Tempo
Loki is an open source project for log aggregation developed by Grafana Labs. In contrast to ElasticSearch, Loki doesn’t index the log content as such. Loki takes an approach similar to Prometheus, where only the metadata (such as labels of your Kubernetes Pods) is indexed. The actual log content is stored as chunks in object stores such as Amazon S3.

This approach of indexing only the metadata and storing the actual log content in object stores drastically simplifies the log aggregation infrastructure and its associated costs. If you have operated ElasticSearch at scale, you will be able to appreciate how complicated that setup can get and how expensive it can quickly become.

Similar to using Fluentd to collect and ingest logs to ElasticSearch, you can use PromTail to collect and ship logs to Loki. Promtail works as an agent that performs service discovery (of your Kubernetes pods), attaches relevant metadata (such as labels), and ships the logs to Loki. This approach is very similar to how Prometheus scrapes metrics.

Cortex provides horizontally scalable, highly available, multi-tenant, long term storage for Prometheus. Prometheus has become the standard choice for metrics in Kubernetes. Prometheus has two components to it – the protocol side which actually scrapes metrics and the storage backend.

Prometheus supports many storage backends through its remote-write capability. Some of the popular backends are time-series datastores like OpenTSDB, TimescaleDB, VictoriaMetrics. Here’s the full list of supported storage backends.

However, using any of these backends means one needs to manage the infrastructure which adds another layer of complexity and overhead.

This is where Cortex comes in. Cortex uses Object Storage such as Amazon S3, GCS as the underlying storage backend. Similar to Loki’s approach, Cortex stores metrics as an index and chunks. The index contains the labels and metrics names while the chunks contain the actual time-series data.

With this approach, correlating between Logs and Metrics becomes easy. Both Promtail and Prometheus employ the same set of service discovery mechanisms and inject the same labels/metadata into Loki and Cortex. So when you see an anomaly in your metrics, you will be able to immediately correlate the corresponding logs.

Tempo is a distributed tracing backend that works on top of Object Storage and is deeply integrated with Loki and Prometheus. Tempo can receive traces from Jaegar/OpenTracing, Zipkin, and OpenTelemetry and durably store the metrics in object stores like Amazon S3, GCS.

Now that we have the three key components figured out, we just need the visualization layer. And that would be Grafana. Grafana integrates Loki, Cortex/Prometheus, and Tempo very well and we can create a single unified dashboard to visualize Logs, Monitoring, and Traces in one place.

Setting up the Observability stack
OK. Let’s get to the hands-on part. We will now set up the Observability stack using Cortex, Loki, Tempo, Prometheus, and build a Grafana dashboard to visualize them all.

You can follow the instructions below to set up the stack on your laptop.

What we will set up
Prometheus – For collecting metrics
Cortex – Long term storage for metrics
Loki – For Log aggregation and long term storage of logs
Tempo – Storage backend for Traces
Promtail – For collecting and sending logs to Loki
Grafana – Dashboard
Pre-requisites
You need to have the following installed in your laptop:

Helm
K8s cluster (Minikube for local)
kubectl
git
Step by Step Guide
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
Final Thoughts
Hope by now you are able to appreciate how Loki, Tempo, and Prometheus/Cortex begin to provide the coveted single pane of glass into observability. Or what we would like to call as “Unified Observability” – a single place where we are able to see monitoring, logging, and tracing, correlate them, and quickly troubleshoot issues.

One of the other major advantages of this stack is that the underlying long-term storage is just an Object Store (like S3/GCS). With the adoption of microservices, our applications now generate significantly higher volumes of observability data. Hence the cost of the observability infrastructure can climb up pretty quickly. With object stores as the underlying long-term storage, we can control the costs of our observability infrastructure and still collect tons of observability data.

What are your thoughts on Unified Observability? How are you collecting metrics, logs and traces in your environment? Are you deploying some other similar stack that provides you a single pane of glass to Observability? Do share your thoughts in the comments section below.
