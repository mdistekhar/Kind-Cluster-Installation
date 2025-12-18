# KIND Cluster Setup Guide

## 1. Installing KIND and kubectl
-------------------------------------------------------------------------------------------------------------------------------------------------------------------

✅Step1:- Create a File.

               vim install_kind.sh


This is the Script 👇

-------------------------------------------------------------------------------------------------------------------------------------------------------------------

#!/bin/bash

[ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.20.0/kind-linux-amd64
chmod +x ./kind
sudo cp ./kind /usr/local/bin/kind

VERSION="v1.30.0"
URL="https://dl.k8s.io/release/${VERSION}/bin/linux/amd64/kubectl"
INSTALL_DIR="/usr/local/bin"

curl -LO "$URL"
chmod +x kubectl
sudo mv kubectl $INSTALL_DIR/
kubectl version --client

rm -f kubectl
rm -rf kind

echo "kind & kubectl installation complete."

-------------------------------------------------------------------------------------------------------------------------------------------------------------------



This is the Example of Script 👇

<img width="756" height="481" alt="image" src="https://github.com/user-attachments/assets/b221a539-fa9a-42cf-80e6-23feb50a0e9d" />

-------------------------------------------------------------------------------------------------------------------------------------------------------------------


✅Step2:- After that, give permission.

               chmod 777 install_kind.sh

✅Step3:- After that, Install.

                ./install_kind.sh

-------------------------------------------------------------------------------------------------------------------------------------------------------------------

## 2. Install Docker for Kind:-

✅Step1:- First Update the system

               sudo apt-get update

✅Step2:- After that, Install Docker.

                sudo agt-get install docker.io

✅Step3:- Give permission to docker.

                sudo usermod -aG docker $USER && newgrp docker

✅Step4:- Verify Docker Installation.

                docker ps          

✅Step5:- Verify Docker, Kubectl and Kind .

                docker --version
                kind --version
                kubectl version
                
                
-------------------------------------------------------------------------------------------------------------------------------------------------------------------

## 3. Setting Up the KIND Cluster
✅Step1:- Create a File :

          kind-config.yaml 

```yaml

kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
    image: kindest/node:v1.33.1
  - role: worker
    image: kindest/node:v1.33.1
  - role: worker
    image: kindest/node:v1.33.1
```
✅Step2:- Create the cluster using the configuration file:

```bash

kind create cluster --config kind-config.yaml --name tws-kind-cluster
```
✅Step3:- Verify the cluster:

```bash

kubectl get nodes
kubectl cluster-info
```
## 4. Accessing the Cluster
✅Step1:- Use kubectl to interact with the cluster:
```bash

kubectl cluster-info
```


## 5. Setting Up the Kubernetes Dashboard
✅Step1:- Deploy the Dashboard
Apply the Kubernetes Dashboard manifest:
```bash

kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```
✅Step2:- Create an Admin User
Create a dashboard-admin-user.yml file with the following content:

```yaml

apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```
✅Step3:- Apply the configuration:

```bash

kubectl apply -f dashboard-admin-user.yml
```
✅Step4:- Get the Access Token
Retrieve the token for the admin-user:

```bash

kubectl -n kubernetes-dashboard create token admin-user
```
✅Step5:- Copy the token for use in the Dashboard login.

Access the Dashboard
Start the Dashboard using kubectl proxy:

```bash

kubectl proxy
```
✅Step6:- Open the Dashboard in your browser:

```bash

http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```
Use the token from the previous step to log in.

## 6. Deleting the Cluster
✅Step1:- Delete the KIND cluster:
```bash

kind delete cluster --name my-kind-cluster
```

## 7. Notes

Multiple Clusters: KIND supports multiple clusters. Use unique --name for each cluster.
Custom Node Images: Specify Kubernetes versions by updating the image in the configuration file.
Ephemeral Clusters: KIND clusters are temporary and will be lost if Docker is restarted.

# Kind-Cluster-Installation
