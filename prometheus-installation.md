# How To Setup Prometheus Monitoring On Kubernetes Cluster

- Prometheus is an open-source monitoring and alerting system

### Installation using helm charts
  ```
  helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

  helm repo update

  helm install prometheus prometheus-community/prometheus

  $ sudo helm list
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
prometheus      default         1               2024-07-05 05:57:28.624327833 +0000 UTC deployed        prometheus-25.22.0      v2.53.0


  ```

  OUTPUT:

  ```
  $ sudo helm install prometheus prometheus-community/prometheus
NAME: prometheus
LAST DEPLOYED: Fri Jul  5 05:57:28 2024
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The Prometheus server can be accessed via port 80 on the following DNS name from within your cluster:
prometheus-server.default.svc.cluster.local


Get the Prometheus server URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=prometheus,app.kubernetes.io/instance=prometheus" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9090


The Prometheus alertmanager can be accessed via port 9093 on the following DNS name from within your cluster:
prometheus-alertmanager.default.svc.cluster.local


Get the Alertmanager URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=alertmanager,app.kubernetes.io/instance=prometheus" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9093
#################################################################################
######   WARNING: Pod Security Policy has been disabled by default since    #####
######            it deprecated after k8s 1.25+. use                        #####
######            (index .Values "prometheus-node-exporter" "rbac"          #####
###### .          "pspEnabled") with (index .Values                         #####
######            "prometheus-node-exporter" "rbac" "pspAnnotations")       #####
######            in case you still need it.                                #####
#################################################################################


The Prometheus PushGateway can be accessed via port 9091 on the following DNS name from within your cluster:
prometheus-prometheus-pushgateway.default.svc.cluster.local


Get the PushGateway URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus-pushgateway,component=pushgateway" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9091

For more information on running Prometheus, visit:
https://prometheus.io/

  ```

- Expose the service
```
$ sudo kubectl expose service prometheus-server --type=NodePort --target-port=9090 --name=prometheus-server-ext
```


**OR**

# You can setup Prometheus by cloning gitub repository also

### Steps:
##### Clone github repo: 
`git clone https://github.com/sangam14/Prometheus-Monitoring-with-k8`

##### Create A Namespace
First, we will create a Kubernetes namespace for all our monitoring components. Execute the following command to create a new namespace called monitoring.
`kubectl create namespace monitoring`

##### Create A Prometheus Deployment
1. Create a file named ***prometheus-deployment.yaml*** and copy the following contents onto the file. In this configuration, we are mounting the Prometheus config map as a file inside /etc/prometheus. It uses the official Prometheus image from docker hub.
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: prometheus-deployment
  namespace: monitoring
spec:
  replicas: 1
  selector:
    matchLabels:
      app: prometheus-server
  template:
    metadata:
      labels:
        app: prometheus-server
    spec:
      containers:
        - name: prometheus
          image: prom/prometheus:v2.2.1
          args:
            - "--config.file=/etc/prometheus/prometheus.yml"
            - "--storage.tsdb.path=/prometheus/"
          ports:
            - containerPort: 9090
          volumeMounts:
            - name: prometheus-config-volume
              mountPath: /etc/prometheus/
            - name: prometheus-storage-volume
              mountPath: /prometheus/
      volumes:
        - name: prometheus-config-volume
          configMap:
            defaultMode: 420
            name: prometheus-server-conf
        - name: prometheus-storage-volume
          emptyDir: {}

```

##### Create A Prometheus Deployment
*prometheus-service.yml*

```
apiVersion: v1
kind: Service
metadata:
  name: prometheus-service
  namespace: monitoring
  annotations:
    prometheus.io/scrape: 'true'
    prometheus.io/path: '/'
    prometheus.io/port: '8080'
spec:
  selector:
    app: prometheus-server
  type: NodePort
  ports:
    - port: 8080
      targetPort: 9090
      nodePort: 30000

```

#### Steps to Apply the Configuration
1. Create Namespace(if not already created):
   ```
   sudo kubectl create namespace monitoring
   ```
2. Apply Deployment Configuration:
   ```
   sudo kubectl apply -f prometheus-deployment.yml
   ```
3. Apply Service Configuration:
   ```
   sudo kubectl apply -f prometheus-service.yml
   ```
4. Verify Deployment:
   Check if the Prometheus pod is running:
   ```
   sudo kubectl get pods -n monitoring
   ```
5. Verify Service:
   Chgeck if the Prometheus service is running:
   ```
   sudo kubectl get services -n monitoring
   ```
6. Access Prometheus:
   You can access Prometheus using the node's IP address and the NodePort(e.g., `http://<NodeIP>:30000`).

**Troubleshooting**
If you encounter any issues, you can check the logs of the Prometheus pod for more details:

```
sudo kubectl logs <prometheus-pod-name> -n monitoring
```



`Author: Amol Nandiwale`
`Date: July 5th 2024`