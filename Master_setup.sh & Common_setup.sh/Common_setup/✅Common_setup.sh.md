# **✅ Make Script Executable**

`chmod +x Common_setup.sh`

Run:

`sudo ./Common_setup.sh`

---

# **🔎 Now Let Me Explain Important Lines (Like Real DevOps)**

---

## **🔹 `#!/bin/bash`**

Tells Linux:

Run this script using bash shell

---

## **🔹 `set -e`**

Very important in production.

It means:

If any command fails → stop the script immediately

Prevents half-configured systems.

---

# **🔥 STEP 1 — Swap Disable**

`swapoff -a`

Disables swap immediately.

`sed -i '/\sswap\s/s/^/#/' /etc/fstab`

* Finds swap line

* Comments it out

* Prevents swap after reboot

Why?  
 Kubernetes scheduler expects full memory control.

---

# **🔥 STEP 2 — Kernel Modules**

`overlay`  
`br_netfilter`

These are required for:

* Container networking

* Pod communication

* iptables rules

We store them in:

`/etc/modules-load.d/k8s.conf`

So they load after reboot.

---

# **🔥 STEP 3 — sysctl Configuration**

These enable:

`net.bridge.bridge-nf-call-iptables`

Allow iptables to see bridged traffic.

`net.ipv4.ip_forward`

Allows pod-to-pod communication.

Apply:

`sysctl --system`

---

# **🔥 STEP 4 — Containerd Setup**

`containerd config default`

Generates default config file.

---

### **🔴 Important Production Line:**

`sed -i 's/SystemdCgroup = false/SystemdCgroup = true/'`

Why?

Kubernetes uses systemd.  
 If containerd uses cgroupfs → mismatch → cluster fails.

So we align both to systemd.

---

# **🔥 STEP 5 — Kubernetes Repo Setup**

We:

1. Add official Kubernetes repo

2. Add GPG key

3. Install kubelet, kubeadm, kubectl

---

## **🔹 `apt-mark hold`**

Prevents accidental upgrades.

In production:  
 Never auto-upgrade kubelet without control.

---

# **🧠 Why This Script Is Production-Ready?**

✔ Idempotent  
 ✔ Uses proper keyring directory  
 ✔ Enables services  
 ✔ Stops on error  
 ✔ No manual nano editing  
 ✔ Follows Kubernetes official practices

