## **🔹 What is KubeAdm?**

**`kubeadm`** is an official tool provided by Kubernetes to **bootstrap (initialize) a Kubernetes cluster** in a simple and standardized way.

👉 It helps you:

* Initialize a **Control Plane (Master Node)**

* Join **Worker Nodes**

* Set up certificates

* Configure networking basics

It does **NOT**:

* Install Docker / container runtime automatically (you install it)

* Manage cluster after setup (you use `kubectl` for that)

---

## **🔹 What is a KubeAdm Cluster?**

A **KubeAdm Cluster** means:

A Kubernetes cluster that is created and managed using `kubeadm`.

### **Architecture:**

`Control Plane (Master)`  
   `|`  
   `|---- Worker Node 1`  
   `|---- Worker Node 2`  
   `|---- Worker Node 3`

### **Components:**

### **1️⃣ Control Plane Node**

* API Server

* Scheduler

* Controller Manager

* etcd (cluster database)

### **2️⃣ Worker Nodes**

* kubelet

* kube-proxy

* Container Runtime

* Runs Pods

---

# **🚀 STEP-BY-STEP: Setup KubeAdm Cluster (Ubuntu 22.04)**

Assume:

* 1 Master Node

* 2 Worker Nodes

* OS: Ubuntu  
*   
* 2 CPU and  T3.micro small machine on AWS 

# **🔥 STEP 1: Common Setup (Run on ALL Nodes)**

## **1️⃣ Disable Swap**

`sudo swapoff -a`

`sudo sed -i '/ swap / s/^/#/' /etc/fstab`

Kubernetes requires swap to be OFF.

---

## **2️⃣ Enable Required Kernel Modules**

`sudo modprobe overlay`

`sudo modprobe br_netfilter`

`sudo tee /etc/sysctl.d/k8s.conf <<EOF`

`net.bridge.bridge-nf-call-iptables = 1`

`net.bridge.bridge-nf-call-ip6tables = 1`

`net.ipv4.ip_forward = 1`

`EOF`

`sudo sysctl --system`

---

## **3️⃣ Install Container Runtime (Containerd)**

`sudo apt update`

`sudo apt install -y containerd`

Configure:

`sudo mkdir -p /etc/containerd`

`sudo containerd config default | sudo tee /etc/containerd/config.toml`

Enable Systemd Cgroup:

Edit:

`sudo nano /etc/containerd/config.toml`

Change:

`SystemdCgroup = true`

Restart:

`sudo systemctl restart containerd`

`sudo systemctl enable containerd`

---

## **4️⃣ Install kubeadm, kubelet, kubectl**

Add Kubernetes repo:

`sudo apt update`

`sudo apt install -y apt-transport-https curl`

`curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg`

`echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list`

Install:

`sudo apt update`

`sudo apt install -y kubelet kubeadm kubectl`

`sudo apt-mark hold kubelet kubeadm kubectl`

# **🔥 STEP 2: Initialize Control Plane (Run ONLY on Master)**

`sudo kubeadm init --pod-network-cidr=192.168.0.0/16`

After success, it will give:

`kubeadm join <MASTER-IP>:6443 --token xxxxx \`

 `--discovery-token-ca-cert-hash sha256:xxxxx`

⚠️ Copy this command (needed for workers).

---

## **Configure kubectl (On Master)**

`mkdir -p $HOME/.kube`

`sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config`

`sudo chown $(id -u):$(id -g) $HOME/.kube/config`

Check:

`kubectl get nodes`

Status will be `NotReady` (normal).

---

# **🔥 STEP 3: Install CNI Network Plugin (Very Important)**

Without network plugin, cluster won't work.

Example: Install Project Calico

`kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/calico.yaml`

Wait 1–2 minutes.

Check:

`kubectl get nodes`

Now Master should be `Ready`.

---

# **🔥 STEP 4: Join Worker Nodes**

Run the `kubeadm join` command (from master output) on each worker:

`sudo kubeadm join 192.168.1.10:6443 --token xxxxxx \`

 `--discovery-token-ca-cert-hash sha256:xxxxxx`

Check from master:

`kubectl get nodes`

Now you will see:

`master      Ready`

`worker1     Ready`

`worker2     Ready`

🎉 Cluster is Ready\!

# **🔥 STEP 5: Deploy Application**

Create nginx deployment:

`kubectl create deployment nginx --image=nginx`

Check:

`kubectl get pods`

---

## **Expose Service**

`kubectl expose deployment nginx --type=NodePort --port=80`

Check:

`kubectl get svc`

Access:

`http://<NodeIP>:<NodePort>`

---

# **🔥 STEP 6: Scaling**

Scale to 3 replicas:

`kubectl scale deployment nginx --replicas=3`

Check:

`kubectl get pods`

---

# **📌 Important Commands**

| Command | Purpose |
| ----- | ----- |
| `kubeadm init` | Initialize cluster |
| `kubeadm join` | Join worker |
| `kubectl get nodes` | List nodes |
| `kubectl get pods -A` | All pods |
| `kubectl describe pod <name>` | Pod details |
| `kubectl delete pod <name>` | Delete pod |

# **🔥 Real Industry Use**

Companies use:

* kubeadm (for on-prem clusters)

* Managed services like:

  * Amazon EKS

  * Google Kubernetes Engine

  * Azure Kubernetes Service

---

# **🎯 Simple Definition (Interview Ready)**

**KubeAdm** is a tool used to bootstrap a secure Kubernetes cluster quickly by initializing control-plane nodes and joining worker nodes.

**KubeAdm Cluster** is a Kubernetes cluster created using kubeadm.
