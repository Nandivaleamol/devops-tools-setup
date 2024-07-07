# Setting Up ELK/EFK Stack on Kubernetes using Helm

### Pre-requisites
- EKS Cluster
- Nodes: Each node should have at least 4GB RAM
- Client Machine: Should have kubectl and helm configured


### Step-by-Step Setup

#### 1.  Create Namespace
Create a namespace for the ELK/EFK stack:
```
kubectl create ns efk
```
VerifyVerify the namespace creation:
```
kubectl get ns
```

#### 2. Configure Helm Repositories
Add the Elastic Helm repository:
```
helm repo add elastic https://helm.elastic.co
```

List all Helm repositories to confirm:
```
helm repo ls
```

#### 3. Elasticsearch Configuration
Fetch the default Elasticsearch values file:
```
helm show values elastic/elasticsearch >> elasticsearch.values
```

Edit the elasticsearch.values file:
```
vi elasticsearch.values
```

Make the following changes in the elasticsearch.values file:

- Set replicas to 1
- Set masternodes to 1
- Change Service Type from ClusterIP to NodePort
- Change Port to 80

#### 4. Install Elasticsearch
Install Elasticsearch with the customized values:
```
helm install elasticsearch elastic/elasticsearch -f elasticsearch.values -n efk
```

#### 5. Install Kibana
Fetch the default Kibana values file:
```
helm show values elastic/kibana >> kibana.values
```

Edit the kibana.values file if needed:
```
vi kibana.values
```

Install Kibana:
```
helm install kibana elastic/kibana -f kibana.values -n efk
```

#### 6. Verify Installation
Check all resources in the efk namespace:
```
kubectl get all -n efk
```

#### 7. Install Filebeat
Install Filebeat for log shipping:
```
helm install filebeat elastic/filebeat -n efk
```

#### 8. Install Metricbeat
Install Metricbeat for metric collection:
```
helm install metricbeat elastic/metricbeat -n efk
```

#### Conclusion
By following these steps, you will have successfully set up the ELK/EFK stack on your Kubernetes cluster. This setup includes Elasticsearch for storage and search, Kibana for visualization, Filebeat for log shipping, and Metricbeat for metrics collection.



`Author: Amol Nandiwale`
`Date: July 5th 2024`