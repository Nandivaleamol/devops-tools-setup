# Setting Up Prometheus and Grafana on Kubernetes using Helm
## Cluster Setup

- Cluster Type: Multi-node Kubernetes cluster on AWS EC2 instances
- Nodes: 1 master node, 2 worker nodes
## HELM Installation on Ubuntu OS

- Go with the official website for helm installation. `https://helm.sh/docs/intro/install/`

### From Script
- Helm now has an installer script that will automatically grab the latest version of Helm and install it locally
- You can fetch that script, and then execute it locally. It's well documented so that you can read through it and understand what it is doing before you run it.
```
    $ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
    $ chmod 700 get_helm.sh
    $ ./get_helm.sh
```

### From Apt (Debian/Ubuntu)
```
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
sudo apt-get install apt-transport-https --yes
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm

```
## Step-by-Step Setup
#### 1. Add Helm Repositories
Add necessary Helm repositories:
```
helm repo add stable http://charts.helm.sh/stable
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```
#### 2. Search for Prometheus Helm Chart
Search for Prometheus Helm chart in the repository:
```
helm search repo prometheus-community
```
#### 3. Install Prometheus using Helm
Install Prometheus using the Prometheus Community Helm chart:
```
helm install prometheus prometheus-community/kube-prometheus-stack
```
#### 4. Verify Installation
Check Helm releases to verify Prometheus installation:
```
helm list
```
#### 5. Access Services
View deployed pods and services:
```
kubectl get pods
kubectl get svc
```
#### 6. Configure Access for Grafana and Prometheus
Edit services to change from ***ClusterIP*** to ***NodePort*** for external access:

```
kubectl edit svc prometheus-kube-prometheus-prometheus
kubectl edit svc prometheus-grafana
```
#### 7. Access Grafana and Prometheus
Access Grafana and Prometheus using NodePort:

- Prometheus: `http://<node-ip>:<prometheus-nodeport>`
- Grafana: `http://<node-ip>:<grafana-nodeport>`

#### 8. Stop and Uninstall Services
To stop and uninstall Prometheus and Grafana:
```
helm uninstall prometheus
helm uninstall grafana
```
Verify that services are deleted:
```
kubectl get pods
kubectl get svc
```




`Author: Amol Nandiwale`
`Date: July 5th 2024`