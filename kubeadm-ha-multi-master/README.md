
# Set up a Highly Available Kubernetes Cluster using kubeadm

Follow this documentation to set up a highly available Kubernetes cluster using Ubuntu 20.04 LTS.

This documentation guides you in setting up a cluster with two master nodes, one worker node and a load balancer node using HAProxy.

Vagrant Environment

# Kubernetes Cluster Setup

| Role          | FQDN                     | IP             | OS           | RAM | CPU |
|---------------|--------------------------|----------------|--------------|-----|-----|
| Load Balancer | loadbalancer.example.com | 172.16.16.100  | Ubuntu 20.04 | 1G  | 1   |
| Master        | kmaster1.example.com     | 172.16.16.101  | Ubuntu 20.04 | 2G  | 2   |
| Master        | kmaster2.example.com     | 172.16.16.102  | Ubuntu 20.04 | 2G  | 2   |
| Worker        | kworker1.example.com     | 172.16.16.201  | Ubuntu 20.04 | 1G  | 1   |

---

**Notes:**
- Password for the root account on all these virtual machines: `kubeadmin`
- Perform all commands as **root user** unless otherwise specified.

**Pre-requisites**

If you want to try this in a virtualized environment on your workstation

KVM installed
Vagrant installed
Host machine has atleast 8 cores
Host machine has atleast 8G memory

**Bring up all the virtual machines**

       vagrant up

**Set up load balancer node**

Install Haproxy

       apt update && apt install -y haproxy

Configure haproxy

Append the below lines to /etc/haproxy/haproxy.cfg

   frontend kubernetes-frontend
      bind 172.16.16.100:6443
      mode tcp
      option tcplog
      default_backend kubernetes-backend

   backend kubernetes-backend
      mode tcp
      option tcp-check
      balance roundrobin
      server kmaster1 172.16.16.101:6443 check fall 3 rise 2
      server kmaster2 172.16.16.102:6443 check fall 3 rise 2  

Restart haproxy service

      systemctl restart haproxy

# On all kubernetes nodes (kmaster1, kmaster2, kworker1)

**Disable Firewall**

    ufw disable

**Disable swap**

    swapoff -a; sed -i '/swap/d' /etc/fstab

**Update sysctl settings for Kubernetes networking**

    cat >>/etc/sysctl.d/kubernetes.conf<<EOF
    net.bridge.bridge-nf-call-ip6tables = 1
    net.bridge.bridge-nf-call-iptables = 1
    EOF
    sysctl --system

**Install docker engine**

     sudo apt-get update
     sudo apt-get install ca-certificates curl
     sudo install -m 0755 -d /etc/apt/keyrings
     sudo curl -fsSL https://download.docker.com/linux/ubuntu//gpg -o /etc/apt/keyrings/docker.asc
     sudo chmod a+r /etc/apt/keyrings/docker.asc

     # Add the repository to Apt sources:

     echo   "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu/ \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" |   sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

     sudo apt-get update

     sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin

     systemctl status containerd.service

# Kubernetes Setup

**Add Apt repository**

    echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.listpt-cache madison kubeadm | grep 1.31
    
    sudo apt update

    sudo apt-cache madison kubeadm | grep 1.31

**Install Kubernetes components**

    sudo apt-get install -y  kubeadm=1.31.3-1.1 kubelet=1.31.3-1.1 kubectl=1.31.3-1.1

# On any one of the Kubernetes master node (Eg: kmaster1)

**Initialize Kubernetes Cluster**

    kubeadm init --control-plane-endpoint="172.16.16.100:6443" --upload-certs --apiserver-advertise-address=172.16.16.101 --pod-network-cidr=192.168.0.0/16

Copy the commands to join other master nodes and worker nodes.

**Deploy Calico network**

   curl https://raw.githubusercontent.com/projectcalico/calico/v3.30.3/manifests/calico.yaml -O

   kubectl --kubeconfig=/etc/kubernetes/admin.conf create -f calico.yaml

# Join other nodes to the cluster (kmaster2 & kworker1)
 
 Use the respective kubeadm join commands you copied from the output of kubeadm init command on the first master.

IMPORTANT: You also need to pass --apiserver-advertise-address to the join command when you join the other master node.

    kubeadm join 172.16.16.100:6443   --token g81bqz.w7iwhe45r4c1f9uz   --discovery-token-ca-cert-hash sha256:5a0----f-e0xxxxxxxxx   --control-plane   --certificate-key xxxxxxxxxxxxx   --apiserver-advertise-address=172.16.16.102



# Downloading kube config to your local machine

On your host machine

     mkdir ~/.kube
     scp root@172.16.16.101:/etc/kubernetes/admin.conf ~/.kube/config

Password for root account is kubeadmin (if you used my Vagrant setup)

# Verifying the cluster
     
     kubectl cluster-info
     kubectl get nodes
     kubectl get cs

     
