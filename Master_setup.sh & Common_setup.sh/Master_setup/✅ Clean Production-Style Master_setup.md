# **✅ Clean Production-Style `Master_setup.sh`**

Here is the refined version without:

* Join command auto-print

* Manual verification commands

---

`#!/bin/bash`

`set -e`

`POD_NETWORK_CIDR="192.168.0.0/16"`  
`CALICO_VERSION="v3.26.1"`

`echo "========== Kubernetes Master Setup Started =========="`

`# ------------------------------------------------------------`  
`# 1. Initialize Kubernetes Control Plane`  
`# ------------------------------------------------------------`

`echo "[STEP 1] Initializing Control Plane..."`

`kubeadm init --pod-network-cidr=${POD_NETWORK_CIDR}`

`# ------------------------------------------------------------`  
`# 2. Configure kubectl for Current User`  
`# ------------------------------------------------------------`

`echo "[STEP 2] Configuring kubectl access..."`

`mkdir -p $HOME/.kube`  
`cp /etc/kubernetes/admin.conf $HOME/.kube/config`  
`chown $(id -u):$(id -g) $HOME/.kube/config`

`# ------------------------------------------------------------`  
`# 3. Install CNI Plugin (Calico)`  
`# ------------------------------------------------------------`

`echo "[STEP 3] Installing Calico CNI..."`

`kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/${CALICO_VERSION}/manifests/calico.yaml`

`echo "========== Master Setup Completed =========="`

---

# **🔥 Why This Feels More “Real”?**

Because in real environments:

### **🔹 Join Command Handling**

Either generated separately:

 `kubeadm token create --print-join-command`

*   
* Or managed via automation tools (Ansible / Terraform)

* Or stored securely in Vault / Secrets Manager

Not inside bootstrap script.

---

### **🔹 Verification Step**

Instead of:

`kubectl get nodes`  
