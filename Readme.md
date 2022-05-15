Installation of kubeadm on master node and two worker nodes

Step 1. 
Installation is done on ubuntu instances. Use the latest instances.
Master node - t2.medium 
Workers nodes - t2.large

Open the following ports when creating the security groups for the instances
On the master node security group:
Port            Source
22              0.0.0.0/0
6443            0.0.0.0/0
6783            Your vpc's cidr block
10250-10252     Your vpc's cidr block
2379-2380       Your vpc's cidr block

On the worker node security group:
Port            Source
6783            Your vpc's cidr block
30000-32767     0.0.0.0/0
10250           Your vpc's cidr block
22              0.0.0.0/0

Step 2.
Configure the master and worker nodes
Connect via ssh and execute the following commands as root

Turn off swap on all the instances
sudo swapoff -a

Assign a hostname to each instance for easy identification
On master node:
sudo hostnamectl set-hostname master 

On worker node1
sudo hostnamectl set-hostname worker1 

On worker node2
sudo hostnamectl set-hostname worker2

Step 3.
Install container runtime on all nodes
Use the containerd-install.sh file to run the installation

Step 4.
Install k8S and its components on all nodes
Use the kubernetes-install.sh file to run the installation

Step 5.
Initialize kubeadm cluster
On the master node:
sudo kubeadm init

Check kubelet that the kubelet process is running on the master node
service kubelet status
systemctl status kubelet

Access the cluster as an admin
On the master node, switch to ubuntu user and run the following commands
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

Step 6.
Install a pod network plugin
On the master node install the network plugin by running the shell script weave-network-install.sh

Step 7.
Join the worker nodes to the cluster
Worker nodes are joined to the cluster using the command generated by the kubeadm init process. 
If the command has been cleared, re-generate it by running the following command on the master node:
kubeadm token create --print-join-command
Execute the command on the worker nodes as root user

Step 8
Start a tes pod
kubectl run test --image=nginx

Verify pod status
kubectl get pods