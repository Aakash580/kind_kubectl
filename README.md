# kind_kubectl
# Kubernetes Setup with kind and kubectl

This README provides step-by-step instructions to set up a local Kubernetes environment using `kind` (Kubernetes IN Docker) and `kubectl` on a Linux system.

---

## 📦 Prerequisites

- Linux system (`x86_64` architecture)
- Docker installed and running
- `curl` installed
- `sudo` privileges

---

## ⚙️ Installation Script

Run the following script to install `kind` and `kubectl`:

### install.sh
```bash
#!/bin/bash

# Install kind
[ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.27.0/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind

# Install kubectl
VERSION="v1.30.0"
URL="https://dl.k8s.io/release/${VERSION}/bin/linux/amd64/kubectl"
INSTALL_DIR="/usr/local/bin"

curl -LO "$URL"
chmod +x kubectl
sudo mv kubectl $INSTALL_DIR/
kubectl version --client

# Cleanup
rm -f kubectl
rm -rf kind

echo "kind & kubectl installation complete."
```

### Run the script
```bash
chmod +x install.sh
./install.sh
```

---

## 🚀 Create a Kubernetes Cluster with kind

To create a local Kubernetes cluster:
```bash
kind create cluster
```

To delete the cluster:
```bash
kind delete cluster
```

---

## 📊 Access the Kubernetes Dashboard

### Step 1: Deploy the Dashboard
```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```

### Step 2: Create an admin user
Create a file named `dashboard-adminuser.yaml` with the following content:
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

Apply it:
```bash
kubectl apply -f dashboard-adminuser.yaml
```

### Step 3: Get the login token
```bash
kubectl -n kubernetes-dashboard create token admin-user
```
Copy the token output for the next step.

### Step 4: Start the proxy
```bash
kubectl proxy
```

### Step 5: Open the Dashboard
Open the following URL in your browser:
```
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```

### Step 6: Log in
Choose the **Token** option and paste the token retrieved in step 3 to access the Dashboard.

---

## 📁 Project Files

- `install.sh` – Installation script for kind and kubectl
- `dashboard-adminuser.yaml` – RBAC configuration for Dashboard admin access

---
