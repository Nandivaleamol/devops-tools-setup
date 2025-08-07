# Complete guide to setting up Minikube on an AWS EC2 instance
# Minikube Setup on AWS EC2 Instance
# This guide will walk you through the steps to set up Minikube on an AWS EC2 instance.
# Prerequisites
## Prerequisites
- An AWS account
- An EC2 instance running a supported Linux distribution (e.g., Ubuntu, CentOS)
- SSH access to the EC2 instance
- Basic knowledge of Linux command line
- A terminal or SSH client
- Sudo privileges on the EC2 instance

## Step 1: Launch an EC2 Instance
1. Log in to your AWS Management Console.
2. Navigate to the EC2 Dashboard.
3. Click on "Launch Instance".
4. Choose an Amazon Machine Image (AMI) that supports Minikube (e.g., Ubuntu 20.04 LTS).
5. Select an instance type (t2.medium or larger is recommended).
6. Configure instance details, storage, and security groups as needed.
7. Review and launch the instance.
8. Download the key pair (.pem file) if you haven't done so already.

## Step 2: Connect to Your EC2 Instance
1. Open your terminal or SSH client.
2. Navigate to the directory where your key pair (.pem file) is located.
3. Run the following command to connect to your instance (replace `your-key.pem` and `ec2-user@your-instance-ip` with your actual key file and instance IP address):
   ```bash
   chmod 400 your-key.pem
   ssh -i "your-key.pem" ec2-user@your-instance-ip
   ```
4. If prompted, type "yes" to continue connecting.
5. You should now be logged into your EC2 instance.
6. Update the package manager:
   ```bash
   sudo apt-get update
   ```
## Step 3: Install Dependencies
1. Install required packages:
   ```bash
   sudo apt-get install -y apt-transport-https ca-certificates curl
   ```
2. Install Docker:
   Run the following command to uninstall all conflicting packages:
    ```bash
   for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
   ```
3. Install Docker: Install using the apt repository
   ```bash
   # Add Docker's official GPG key:
    sudo apt-get update
    sudo apt-get install ca-certificates curl
    sudo install -m 0755 -d /etc/apt/keyrings
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
    sudo chmod a+r /etc/apt/keyrings/docker.asc
    
    # Add the repository to Apt sources:
    echo \
    "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
    $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
    sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    sudo apt-get update
   ```

4. Install Docker Engine:
   ```bash
    sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
   ```
5. Start and enable Docker:
   ```bash
    sudo systemctl start docker
    sudo systemctl enable docker
    ```
6. Verify Docker installation:
    ```bash
   sudo docker run hello-world
   ```

## Step 4: Install Minikube
### What you’ll need
- 2 CPUs or more
- 2GB of free memory
- 20GB of free disk space
- Internet connection
- Container or virtual machine manager

1. Install Minikube for Linux ARM64 architecture:
   ```bash
    curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-arm64
    sudo install minikube-linux-arm64 /usr/local/bin/minikube && rm minikube-linux-arm64
   ```
2. Install cri-dockerd Ubuntu ARM
   1. Install cri-dockerd Ubuntu ARM package to enable Docker as a container runtime and Install Minikube on EC2 instance:

      To install cri-dockerd on Ubuntu for ARM64 architecture, follow these steps:

      First, ensure you have the necessary tools installed:
        ```bash
          sudo apt update
          sudo apt install -y git wget curl   
        ```
      Next, determine the latest release version of cri-dockerd:
      ```bash
        VER=$(curl -s https://api.github.com/repos/Mirantis/cri-dockerd/releases/latest | grep tag_name | cut -d '"' -f 4 | sed 's/v//g')
        echo $VER
      ```
      Download the ARM64 binary package for the latest version:
      ```bash
        wget https://github.com/Mirantis/cri-dockerd/releases/download/v${VER}/cri-dockerd-${VER}.arm64.tgz   
      ```
      Extract the downloaded archive:
      ```bash
      tar xvf cri-dockerd-${VER}.arm64.tgz   
      ```
      Move the cri-dockerd binary to a directory in your PATH, such as /usr/local/bin:
      ```bash
      sudo mv cri-dockerd/cri-dockerd /usr/local/bin/
      ```
      Verify the installation by checking the version:
      ```bash
      cri-dockerd --version
      ```
      Download the systemd service and socket files:
        ```bash
        wget https://raw.githubusercontent.com/Mirantis/cri-dockerd/master/packaging/systemd/cri-docker.service
        wget https://raw.githubusercontent.com/Mirantis/cri-dockerd/master/packaging/systemd/cri-docker.socket
        ```
      Move these files to the systemd system directory:
        ```bash
        sudo mv cri-docker.socket cri-docker.service /etc/systemd/system/
        ```
      Update the service file to point to the correct binary path:
       ```bash
        sudo sed -i -e 's,/usr/bin/cri-dockerd,/usr/local/bin/cri-dockerd,' /etc/systemd/system/cri-docker.service
       ```
      Reload the systemd daemon to recognize the new service configuration:
      ```bash
      sudo systemctl daemon-reload
      ```
      Enable and start the cri-dockerd socket service:
      ```bash
      sudo systemctl enable --now cri-docker.socket
      ```
      Confirm the service is running:
      ```bash
      systemctl status cri-docker.socket
      ```
   This process ensures cri-dockerd is installed and configured to run as a service on Ubuntu ARM64, providing a CRI-compliant interface for Docker.

3. Install CNI plugins:
   Note:- When using the none VM driver with Minikube on Ubuntu, you need to install the container networking (CNI) plugins.
    ```bash
     sudo apt-get install -y conntrack
   ````
4. Download and install CNI plugins:
   ```bash
    # Create the required directory
    sudo mkdir -p /opt/cni/bin
    
    # Download and extract the CNI plugins
    curl -Lo cni-plugins.tgz https://github.com/containernetworking/plugins/releases/download/v1.3.0/cni-plugins-linux-arm64-v1.3.0.tgz
    
    # Extract the archive to the CNI directory
    sudo tar -C /opt/cni/bin -xzf cni-plugins.tgz
    
    # Remove the downloaded archive
    rm cni-plugins.tgz

   ```
5. Start Minikube with the `None` driver to run Minikube Kubernetes directly on the host:
   ```bash
   minikube start --vm-driver=none
   ```

6. Verify Minikube installation:
   ```bash
    minikube status
    ```
7. Deploy an nginx container using a Kubernetes manifest YAML file
   ### Step 1: Create the Manifest File
    Create a file named nginx-deployment.yaml with the following content:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: NodePort
```
###  Step 2: Apply the Manifest
 ```bash
 kubectl apply -f nginx-deployment.yaml
```
### Step 3: Verify the Deployment
```bash
kubectl get pods
kubectl get deployment
kubectl get service
```
###  Step 4: Access Nginx
Find the NodePort:
```bash
  kubectl get svc nginx-service
```
You’ll see something like this:
```plaintext
NAME            TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
nginx-service   NodePort   10.96.47.212   <none>        80:31000/TCP   1m
```
Then access it at:
```plaintext
http://<your-node-ip>:<node-port>
```


              
