# Kubernetes

Kubernetes is a container orchestration system. So first it is good to know about orchestration

### What is Container Orchestration & why it’s required?

+ Container orchestration is the automation of all aspects of coordinating and managing containers. Container orchestration is focused on managing the life cycle of containers and their dynamic environments.

<p align=center>

<img  src='./Images/Orchestration.png'  alt='Orchestration'/>

</p>

### Use
  + Provisioning and deployment of containers
  + Redundancy and availability of containers
  + Scaling up or removing containers to spread application load evenly across host infrastructure
  + Movement of containers from one host to another if there is a shortage of resources in a host, or if a host dies
  + Allocation of resources between containers
  + External exposure of services running in a container with the outside world
  + Load balancing of service discovery between containers
  + Health monitoring of containers and hosts


#### How does container orchestration work?

Container orchestration works with tools like Kubernetes and Docker Swarm. Configurations files (for example, docker-compose.yml) tell the container orchestration tool where to gather container images (for example, from Docker Hub), how to establish networking between containers, how to mount storage volumes, and where to store logs for that container. The orchestration tool also schedules deployment of containers into clusters and determines the best host for the container. After a host is decided, the orchestration tool manages the lifecycle of the container based on predetermined specifications. Container orchestration tools work in any environment that runs containers.

+ Orchestration tools for Docker include the following:
  +  Docker Machine — Provisions hosts and installs Docker Engine. 
  +  Docker Swarm — Clusters multiple Docker hosts under a single host. It can also integrate with any tool that works with a single     Docker host.
  +  Docker Compose — Deploys multi-container applications by creating the required containers.

+ Orchestration tools for Kubernetes include the following features:
  +  Automated deployment and replication of containers.
  +  Online scale-in or scale-out of container clusters
  +  Load balancing groups of containers.
  +  Automated rescheduling of failed containers.
  +  Controlled exposure of network ports to systems outside of the cluster.
  


## Kubernetes Versus Docker Swarm
Though both the open-source orchestration platforms provide much of the same functionalities, there are some fundamental differences between how these two operate. Below here are some of the notable points.
Kubernetes and Docker are two of the major players in container orchestration. Both of these tools enable us to handle a cluster of servers that run one or more services on them.

+ Logging and Monitoring
  - Inbuilt tools have been included in Kubernetes for the purpose of Logging and Monitoring. Logging helps to understand the cause of failures through the analysis of past records/logs. On the other hand, monitoring enables us to be constantly aware of the health of the nodes and the services that are containerized by them.
  - Swarm lacks the capability of inbuilt tools to handle Logging and Monitoring. Though, you can make use of third-party tools to achieve this. The tool ELK is one such example.

+ Building and Running Containers
  - Kubernetes embraces uniqueness by having its own API, client, and YAML definitions. These are in high contrast to the standard Docker equivalents since it is not possible to use Docker Compose or Docker CLI to define containers.
  - The same Docker CLI is used and new containers can be spinned with a single command.

+ Scalability
  - when it comes to Auto-scaling, Kubernetes is way ahead in the race since it can analyze the server load and scale up or down in tandem with your requirements. If any of the pods get crashed or terminated, it automatically creates new pods.

+ GUI
  - The GUI provided is a reliable dashboard which can be used to control the cluster.
   - Docker hosts and Swarm Clusters can be managed with the help of third party tool such as Portainer.io that provide an easy management UI.

+ Load Balancing
  - Load Balancing is permitted in Kubernetes when Container Pods are defined as Services. Apart from this, we need to manually configure the load balancing settings.
  - Docker Swarm provides an inbuilt facility of Load Balancing. A common network is joined by all containers that are in the same cluster. This allows connection of any node to any container.

+ Container orchestration platforms: let someone else manage Kubernetes for you


<p align=center>
  
  <img  src='./Images/Kubernetes-Swarm-comparison.png'  alt='Comparison'/>

</p>


# Kubernetes Architecture

Kubernetes follows client-server architecture. Where we have master installed on one machine and the node on separate Linux machines.

<p align=center>
  
  <img  src='./Images/Kubernetes-architecture.png'  alt='Kubernetes Architecture'/>
  
</p>

### Important Terms:
+ `Docker` is basically a container engine which is use to create containers on top of an operating system and automates application deployment on the container.
+ `Kubeadm` is a new tool that is part of the Kubernetes distribution as of 1.4.0 which helps you to install and set up a Kubernetes cluster.
+ `Kubelet` is responsible for maintaining and managing a set of pods, which are composed of one or more containers, on a local system. Within a Kubernetes cluster, it act as a local agent that watches for pod specs via the Kubernetes API server.
+ `Kubectl` is a command line tool for controlling Kubernetes clusters.
Control Plane is a series of different services that form the Kubernetes Master structure, that allows the kubernetes master to control the cluster.
+ `Cluster` is a set of nodes with at least one master node and several worker nodes (sometimes referred to minions) that can be virtual or physical machines.
+ `Pods` It contain one or multiple containers which we can run inside it 
+ `Node`contains kubelet which communicate with master node. it have multiple pods running.
+ `Services` handel request from node or outside node. Usually load balancer.
+ `Replication Controllers` deployment is a YAML object that defines the pods and the number of container instances, called replicas, for each pod. You define the number of replicas you want to have running in the cluster via a ReplicaSet, which is part of the deployment object. So, for example, if a node running a pod dies, the replica set will ensure that another pod is scheduled on another available node.

# Setup Server
For building the cluster we need three servers of ubuntu distribution.

`Note: You can use AWS EC2 instance to initiate the server. The minimum no. of CPU should me at least 2 in every server.`

**Tag:**

+ **For Server 1:** Kube Master

+ **For Server 2:** Kube Node 1

+ **For Server 3:** Kube Node 2


#  Setup Docker
<p> The first step in setting up a new cluster is to install a container runtime such as Docker. </p>

<p> We will be installing Docker on our three servers in prearation for standing up a Kubernetes cluster.</p>

Here are the commands :

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

  
sudo add-apt-repository \
"deb [arch=amd64] https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) \
stable"

  
sudo apt-get update

sudo apt-get install -y docker-ce=18.06.1~ce~3-0~ubuntu
  
sudo apt-mark hold docker-ce
```
  

You can verify that docker is working by running this command:
`sudo docker version`

## Setup Kubeadm, Kubelet, and Kubectl
<p> Now that Docker is installed, we are ready to install the Kubernetes components.<p>

**Run these commands on all three servers.**
You can work around this by using version 1.12.7-00 for kubelet, kubeadm, and kubectl.

```
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

cat << EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF

sudo apt-get update

sudo apt-get install -y kubelet=1.12.7-00 kubeadm=1.12.7-00 kubectl=1.12.7-00

sudo apt-mark hold kubelet kubeadm kubectl
```

After installing these components, verify that Kubeadm is working by getting the version info.
`kubeadm version`


## Bootstrapping the Cluster
<p>Now we are ready to get a real Kubernetes cluster up and running!. We will bootstrap the cluster on the Kube master node. Then, we will join each of the two worker nodes to the cluster, forming an actual multi-node Kubernetes cluster.</p>

Here are the commands used in this lesson:

+ On the Kube master node, initialize the cluster:

`sudo kubeadm init --pod-network-cidr=10.244.0.0/16`

That command may take a few minutes to complete.

+ When it is done, set up the local kubeconfig:
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

+ Verify that the cluster is responsive and that Kubectl is working:

`kubectl version`

You should get `Server Version` as well as `Client Version`. It should look something like this:

```Client Version: version.Info{Major:"1", Minor:"12", GitVersion:"v1.12.2", GitCommit:"17c77c7898218073f14c8d573582e8d2313dc740", GitTreeState:"clean", BuildDate:"2018-10-24T06:54:59Z", GoVersion:"go1.10.4", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"12", GitVersion:"v1.12.2", GitCommit:"17c77c7898218073f14c8d573582e8d2313dc740", GitTreeState:"clean", BuildDate:"2018-10-24T06:43:59Z", GoVersion:"go1.10.4", Compiler:"gc", Platform:"linux/amd64"}
```

+ The `kubeadm init` command should output a `kubeadm join` command containing a token and hash. Copy that command and run it with `sudo` on both worker nodes. It should look something like this: </p>

`sudo kubeadm join $some_ip:6443 --token $some_token --discovery-token-ca-cert-hash $some_hash`

+ Verify that all nodes have successfully joined the cluster:

`kubectl get nodes`

You should see all three of your nodes listed. It should look something like this:
```
NAME                      STATUS     ROLES    AGE     VERSION
wboyd1c.mylabserver.com   NotReady   master   5m17s   v1.12.2
wboyd2c.mylabserver.com   NotReady   <none>   53s     v1.12.2
wboyd3c.mylabserver.com   NotReady   <none>   31s     v1.12.2
```
**Note**: The nodes are expected to have a STATUS of `NotReady` at this point.



## Configuring Networking with Flannel
<p>Once the Kubernetes cluster is set up, we still need to configure cluster networking in order to make the cluster fully functional. We will walk through the process of configuring a cluster network using Flannel.</p>

Here are the commands used in this lesson:

+ On all three nodes, run the following:

`echo "net.bridge.bridge-nf-call-iptables=1" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p`

+ Install Flannel in the cluster by running this only on the Master node:

`kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/bc79dd1505b0c8681ece4de4c0d86c5cd2643275/Documentation/kube-flannel.yml`

+ Verify that all the nodes now have a STATUS of Ready:

`kubectl get nodes`

You should see all three of your servers listed, and all should have a STATUS of Ready. It should look something like this:

```
NAME                      STATUS     ROLES    AGE     VERSION
wboyd1c.mylabserver.com   Ready      master   5m17s   v1.12.2
wboyd2c.mylabserver.com   Ready      <none>   53s     v1.12.2
wboyd3c.mylabserver.com   Ready      <none>   31s     v1.12.2
```

**Note:** It may take a few moments for all nodes to enter the Ready status, so if they are not all Ready, wait a few moments and try again.

+ Run this command to get a list of system pods:

`kubectl get pods -n kube-system`

You should have three pods with flannel in the name, and all three should have a status of Running.
