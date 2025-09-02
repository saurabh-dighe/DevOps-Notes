 Docker similar platforms      ->      Podman, Buildah
Docker, Buildah, and Podman all play a role in the containerization world, but they serve distinct purposes:
Docker:
•	The OG: Docker is the most widely used container platform. It offers a comprehensive suite of tools for building, running, and managing containers. It includes a daemon process that runs in the background and manages container lifecycles.
•	Ease of Use: Docker provides a user-friendly interface with familiar commands like docker build and docker run. It offers a large community and extensive documentation.
•	Drawbacks: Docker relies on a daemon, which can be a security concern for some users. It also has historical licensing issues that some prefer to avoid.
Buildah:
•	Image Builder: Buildah specializes in creating OCI-compliant container images. It doesn't require a daemon and offers a more granular approach to image building.
•	Flexibility: Buildah allows building images from Dockerfiles or directly with its commands. It provides greater control over the image creation process.
•	Focus: Buildah primarily focuses on image building, not container management. It's ideal for scenarios where you need to create custom images without the overhead of a daemon.
Podman:
•	Multi-Purpose: Podman combines functionalities from both Docker and Buildah. It can build container images using a subset of Buildah's commands and manage containers similar to Docker.
•	Daemonless: Like Buildah, Podman operates without a daemon, making it lightweight and secure.
•	Docker Compatibility: Podman offers a familiar command-line interface with commands mirroring those in Docker, making it easier to switch for Docker users.
•	Focus: Podman aims to be a complete container solution minus the daemon requirement, providing image building and container management in one tool.
Docker Compose: Ideal for managing multiple containers within a single application
 

Here's a table summarizing the key differences:
Feature	Docker	Buildah	Podman
Primary Function	Build & manage	Build Images	Build & manage
Daemon Required	Yes	No	No
Image Building Commands	docker build	Buildah commands	Subset of Buildah
Container Management	Extensive	Limited	Similar to Docker
User Interface	Familiar	Command Line	Similar to Docker
Focus	All-in-one	Image Building	Daemonless Tool
			
Export to Sheets
Choosing between them depends on your needs:
•	Docker: Ideal for beginners or those needing a comprehensive solution with extensive community support.
•	Buildah: Perfect for advanced users who require granular control over image building without a daemon.
•	Podman: A good option for users comfortable with Docker who want a daemonless alternative with both building and management capabilities.
Key concepts in Docker networking:
•	Bridge networks: The default network type in Docker. It creates a virtual network interface for each container and connects them to a common bridge network.

•	Host networking: This mode shares the host's network namespace with the container, giving it direct access to the host's network interfaces.

•	Overlay networks: These networks create a virtual network across multiple hosts, allowing containers on different hosts to communicate with each other as if they were on the same physical network.

•	Macvlan networking: This mode assigns a unique MAC address to each container, making it appear as a physical network interface on the host.

Docker Architecture
Docker's architecture is designed to provide a lightweight, portable, and efficient environment for running applications. It consists of several key components:
1. Docker Daemon:
•	The core component that runs in the background on the host machine.
•	Manages images, containers, and networks.
•	Interacts with the Docker client to execute commands.

2. Docker Client:
•	The user interface for interacting with the Docker daemon.
•	Sends commands to the daemon, which executes them.
•	Can be run locally on the host machine or remotely.
3. Docker Images:
•	Read-only templates that contain the instructions for building a container.
•	Based on a base image (e.g., Ubuntu, Alpine) and can be customized with layers.
•	Can be pushed and pulled from registries like Docker Hub.
4. Docker Containers:
•	A running instance of an image.
•	Isolated from other containers and the host system.
•	Can be started, stopped, paused, and removed.
•	Share the same kernel with other containers on the host.
5. Docker Registry: [ECR, DockerHub]
•	A centralized repository for storing Docker images.
•	Can be public or private.
•	Allows for sharing and distribution of images.

Volumes in Docker
1. Volume Mount:
•	Created using the docker volume create command.
•	Automatically managed by Docker.
•	Can be shared between multiple containers.
•	Can be inspected and deleted using Docker commands.
2. Bind Mounts:
•	Mount a directory on the host machine directly into a container.
•	Provide direct access to the host filesystem.
•	Can be used for debugging or development purposes.
•	Can be risky if not used carefully, as changes made within the container can affect the host filesystem.
3. Temporary Volumes:
•	Created automatically when a container is started without specifying a volume.
•	Deleted when the container is stopped or removed.
•	Used for temporary data that doesn't need to be persisted.
4. Persistent Volumes:
•	Managed by a separate volume plugin.
•	Can be backed by various storage solutions, such as cloud storage, NFS, or local storage.
•	Provide persistent storage for data that needs to be retained across container restarts or migrations.
5. Secret Volumes:
•	Used to store sensitive data, such as passwords or API keys.
•	Encrypted and stored securely.
•	Can be mounted to containers as read-only volumes.
6. ConfigMaps:
•	Used to store configuration data.
•	Can be mounted to containers as environment variables or files.
•	Provide a way to manage configuration data without having to rebuild containers.

FROM 		node:22			# base image from docker.io
RUN 		useradd -m Saurabh 		# Create a non-root user
WORKDIR 	/app 				# Set working directory
COPY 		package.json ./    		# Copy package.json first (better layer caching)
RUN 		npm install 			# Install dependencies
COPY 		*.js ./				# Copy application code
USER 		Saurabh			# Switch to non-root user
ENTRYPOINT 	["node", "/app/index.js"] 	# Set entry point and default arguments
CMD 		["env", "dev"]			# Env variable dev

# ---------- Stage 1: Build ----------
FROM 		node:22 AS build                    	 	# Full Node.js base image for building
WORKDIR 	/app                              			# Set working directory
COPY 		package.json package-lock.json* ./   	# Copy package files first (better caching)
RUN 		npm install                           		# Install dependencies
COPY		. .                                 			# Copy application source code
# ---------- Stage 2: Runtime ----------
FROM 		node:22-alpine				# Lightweight Alpine Node.js runtime
WORKDIR 	/app                              			# Set working directory inside runtime
COPY 		--from=build /app /app               		# Copy app from build stage to runtime
RUN 		adduser -D -u 1000 appuser             	# Create non-root user inside alpine
USER 		appuser				# Run as non-root user
ENTRYPOINT 	["node", "/app/index.js"]    		# Application entry point
CMD 		["env", "dev"]                        		# Default arguments (can be overridden)


Reduce image size: - Multi stage docker build and using light weight images [e. g distroless and alpine]
If we use final image is distroless + non-root (tiny + secure) also with node:22-alpine runtime instead of distroless (so we can debug inside container with a shell
  
Docker Commands 
•	docker pull imgName   Pulls the image from DockerHub
•	docker run imgName  Run the image on Foreground
•	docker run -d imgName  Run the image in detached mode
•	docker run -d -p 80:8080 imgName  Run the image and port forward
•	docker run -d -P imgName  Run the image and assign random port
•	docker run -d -e envName=value imgName  Run container and assign env variable
•	docker run -d name - -containerName -e envName=value -v /my/custom:/var/lib/mysql imgName:tag
•	docker ps  Check the running containers
•	docker ps -a  To check the terminated containers 
•	docker inspect imgName  Inspect the image
•	docker start and stop  To start and terminate the container
•	docker exec containerName command To run the command without entering into container
•	docker exec -it containerName bash/sh To log in to the container [provide bash or shell Terminal]
•	docker logs containerName/ conatinerID  To check the log of container
•	docker info  To check the client and server info
•	docker images or image ls  To list available images 

	CMD = default arguments (overridable)
	ENTRYPOINT = fixed startup script
	If ENTRYPOINT is not defined, then CMD becomes the main process.
It is not overridden if you pass arguments during docker run, unless you explicitly use --entrypoint.
	docker run -u 1000:1000 alpine id  1000:1000 - UID:GID of the user you want id command inside container will show that it’s not running as root.

K8s - 1.32
Kubernetes Commands
•	kubectl get pod  To see the running pods
•	kubectl get pod --watch  "real-time stream" of pod status.
•	kubectl get node  To see the available nodes
•	kubectl get node – o wide - - show labels  To see the available nodes and more info
•	kubectl get pod podName – o yml  To see the pod details in yml
•	kubectl get ns  To list down available name space
•	kubectl get all -n nameSpace  To list down available resources in that name space
•	kubectl describe pod podName  To inspect the pod and check the events[logs]
•	kubectl describe secret/configMap/cm name  To check the value of secret or config var
•	aws eks update-kubeconfig - -clusterName To download the authentication info
•	kubectl config current-context  To check the connected cluster
•	kubectl cluster-info  To get the cluster info and core dns
•	kubectl api-versions  To list down the available API supported 
•	kubectl api-resources  To list down the available resources
•	kubectl apply -f fileName.yml  To create the resources as mentioned in k8s manifest file
•	kubectl apply -f fileName.yml -- dry-run=server/client  To validate the resources as mentioned in k8s manifest file by server or client
•	kubectl delete -f fileName.yml  To delete the resources as mentioned in k8s manifest file
•	kubectl delete pod podName  To delete pod
•	kubectl logs -f podName  To check the logs of pod
•	kubectl logs -f podName -c containerName  To check the logs of particular container 
•	kubectl logs podName  To check the logs of exited container
•	kubectl exec podName command To run the command without entering into pod
•	kubectl exec -it podName bash/sh  To run the command by entering into pod

EKSCTl is tool we can install k8s with single command

Kubectl 		 Command line tool to access the cluster resource
K9s 		 CLI based UI tools serves the same function
Octant 		 GUI based tool

Sample resource
apiVersion:
kind:
metadata:
spec:
	containers:
	-  name: 
	    image
	K8s supports Dockerstim Cri-O, Container-D runtime

	Kube-proxy – K8s networking supports load balancing 

	Why to choose k8s – Host [ Cluster architecture], Auto-scaling, Auto-healing, Enterprise support.

	CustomResourceDefinition  A CRD tells Kubernetes about a new resource type.

K8s distributions
OpenShift
Rancher Kubernetes Engine (RKE)
Amazon Elastic Kubernetes Service (EKS)
K3s
Cloud-Managed Distributions:
•	Amazon Elastic Kubernetes Service (EKS): A managed Kubernetes service on AWS, offering easy deployment, scaling, and integration with other AWS services.
•	Azure Kubernetes Service (AKS): A managed Kubernetes offering on Microsoft Azure, providing a familiar experience for Azure users.
•	Google Kubernetes Engine (GKE): A managed Kubernetes service on Google Cloud Platform (GCP), offering scalability and integration with GCP services.
Open-Source Distributions:
•	Red Hat OpenShift: A container platform based on Kubernetes, providing additional features like security, service management, and developer tools.
•	Rancher Kubernetes Engine (RKE): A lightweight distribution focused on ease of use and portability across different cloud environments.
•	K3s: A lightweight Kubernetes distribution optimized for resource-constrained environments like edge computing.

KOPS – Kubernetes Operations
Provisioning: Kops automates provisioning the cloud infrastructure needed to run your Kubernetes cluster. This includes resources like virtual machines, networks, storage, and security configurations. It's currently officially supported on AWS and offers beta support for other cloud providers like GCP, Digital Ocean, and OpenStack.
Deployment: Kops streamlines deploying a production-grade, highly available Kubernetes cluster. You can define your cluster configuration using YAML files and leverage Kops commands to create, update, or destroy your cluster.
Management: Kops offers tools for ongoing management of your Kubernetes cluster. This includes tasks like scaling the cluster by adding or removing nodes, upgrading Kubernetes versions, and handling rolling cluster updates.

States of K8s resources
Ready  Pod is ready
Running  Pod is ready and running
containerCreating  Container is creating
Restarting  Due to some issue pod is restarting
CrashLoopBackOff  container starts → crashes → restarts → crashes again… in a loop. 
Error  After the multiple restarts went to  the error state 

Job and CronJob
Unlike a Deployment (which keeps pods running continuously), a Job ensures that a specified number of pods complete successfully.
If a pod in a Job fails, Kubernetes will automatically retry it until it succeeds (or until retries are exhausted).
Jobs are useful for finite workloads like database migrations, batch data processing, sending emails, report generation, etc.
Job   Create a pod  Run the pod  Goes to completed State
A CronJob is like a Linux cron — it schedules and runs Jobs at specified times. Each scheduled execution creates a new Job, and each Job runs one or more pods to completion.




apiVersion: batch/v1
kind: CronJob
metadata:
  name: hello-cron
spec:
  schedule: "*/1 * * * *"      		 # Run every 1 minute
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox
            args:
            - /bin/sh
            - -c
            - "echo Hello from Kubernetes CronJob!"
          restartPolicy: OnFailure


apiVersion: batch/v1
kind: Job
metadata:
  name: pi-job
spec:
  completions: 3        # Number of successful runs required
  parallelism: 2        # How many pods can run at the same time
  template:
    spec:
      containers:
      - name: pi
        image: perl
        command: ["perl", "-Mbignum=bpi", "-wle", "print bpi(2000)"]
      restartPolicy: Never   # Important: Jobs should not restart indefinitely


# ---------- Namespace ----------
apiVersion: v1
kind: Namespace
metadata:
  name: app
---
# ---------- Deployment ----------
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: app                # Deploy in the "app" namespace
spec:
  replicas: 3                   # 3 pod replicas
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
        image: nginx:latest     # Use the latest nginx image
        ports:
        - containerPort: 80

Kubernetes Architecture
Kubernetes, often referred to as K8s, is an open-source container orchestration platform designed to automate the deployment, scaling, and operation of containerized applications. Its architecture is designed to provide a robust and scalable solution for managing containerized workloads.   
Key Components of Kubernetes Architecture:

1.	Master Node:
o	Control Plane: Handles the overall management and orchestration of the cluster.
o	 Components: 
	etcd: A distributed key-value store used to store the state of the cluster.
	KubeAPI Server: The central point of communication for the cluster, handling requests from clients and interacting with other components. Port 6443 
	Scheduler: Assigns pods to worker nodes based on various factors like resource availability and constraints.
	Controller Manager: Responsible for maintaining the desired state of the cluster by running various controllers (e.g., ReplicaSet, Deployment, Service).

2.	Worker Nodes:
o	Runtime Environment: Executes containerized applications.
o	Components: 
	Kubelet: Agent that runs on each worker node, communicates with the master node, and manages pods and containers.
	Container Runtime: The engine that runs containers (e.g., Docker, containerd, CRI-O).
	Kube-proxy: Network proxy that handles service discovery and load balancing within the cluster.

kubectlis the command-line tool for Kubernetes. It's the primary interface for interacting with Kubernetes clusters, allowing you to manage and control various aspects of your applications.

Service in K8s
	Kubernetes service acts as load balancer to routes the incoming requests to available pods, It uses simple routing policies that is round robin.
If we are trying to hit any svc from different namespace we need to mention Fully qualified Domain name
FQDN svcName.nameSpace.svc.cluster.local
E.g: - nginx-svc.default.svc.cluster.local
Types
1. ClusterIP:
•	Description: This is the default service type. It creates a virtual IP address within the cluster that can only be accessed from within the cluster network.
•	Use cases:
o	Internal services that only need to be accessed by other services within the cluster.
o	Services that are not meant to be exposed to the internet.


2. NodePort:
•	Description: This service type allocates a static port on each node in the cluster. This port is exposed to the outside world, allowing external clients to access the service by connecting to any node in the cluster.
•	Use cases:
o	Services that need to be accessed from outside the cluster using a static port.
o	Simple load balancing for services that don't require advanced features.
3. LoadBalancer:
•	Description: This service type creates a load balancer in front of the service, distributing traffic across multiple instances of the service. The load balancer can be either cloud-provider-specific or a dedicated hardware load balancer.
•	Use cases:
o	Services that need to be exposed to the internet with advanced load balancing capabilities.
o	Services that require high availability and fault tolerance.
4. ExternalName:
•	Description: This service type maps a DNS name to an external IP address or a DNS name. It doesn't create any network infrastructure within the cluster.
•	Use cases:
o	Services that are hosted outside the cluster and need to be accessed using a DNS name.
o	Integrating with external services or APIs.
Ingress Resource and Ingress Controller in Kubernetes
Ingress Resource
In Kubernetes, an Ingress resource is a configuration object that defines how external traffic is routed to services within a cluster. It acts as a reverse proxy, handling incoming requests and directing them to the appropriate services based on specific rules.
Key components of an Ingress resource:
•	Rules: These define the hostnames and paths that should be matched with specific services.
•	Paths: Specify the URL paths that should be matched with a particular service.
•	Backend: References the service or endpoint that should handle the request.
•	Annotations: Provide additional configuration options for the Ingress controller.
Ingress Controller
An Ingress controller is a piece of software that implements the logic defined in the Ingress resource. It acts as a proxy between the external network and the Kubernetes cluster, handling incoming requests and routing them to the appropriate services based on the rules specified in the Ingress resource.
Common Ingress controllers:
•	NGINX Ingress Controller: A popular choice based on the NGINX web server.
•	Traefik: A versatile Ingress controller that supports multiple backends and protocols.
•	Contour: An Ingress controller based on Envoy proxy, providing high performance and advanced features.
•	Kong: An API gateway and Ingress controller that offers additional features like rate limiting, authentication, and plugins.
How Ingress Works:
1.	A client sends a request to a specific hostname and path.
2.	The Ingress controller intercepts the request and checks the Ingress resource for matching rules.
3.	If a match is found, the controller routes the request to the specified service.
4.	The service processes the request and returns a response to the controller.
5.	The controller sends the response back to the client.
Benefits of using Ingress:
•	Centralized traffic management: Ingress provides a single point of control for managing external traffic to the cluster.
•	Load balancing: Ingress controllers can distribute traffic across multiple instances of a service.
•	SSL/TLS termination: Ingress can handle SSL/TLS encryption and decryption, providing secure communication.
•	URL rewriting: Ingress can rewrite URLs to match specific patterns or requirements.
•	Customizations: Ingress controllers often support various customizations and plugins to extend their functionality.

Kubernetes Sets: DaemonSet, ReplicaSet, Deployment, and StatefulSet
Kubernetes provides several object sets (or controllers) to manage groups of Pods. Each object set serves a specific purpose and has unique characteristics:
DaemonSet
•	Purpose: Ensures that a Pod runs exactly once on each node in a cluster.
•	Use Cases: Tasks that need to run on every node, such as network plugins, system tools, or daemons.
•	Characteristics:
o	Pods are scheduled to run on every node.
o	If a Pod is terminated or fails, Kubernetes will reschedule it on another node.
o	DaemonSets are typically used for system-level tasks that need to be running on all nodes.
ReplicaSet
•	Purpose: Ensures that a specified number of Pods are running at any given time.
•	Use Cases: Managing a fixed number of Pods for an application.
•	Characteristics:
o	ReplicaSets define the desired number of Pods.
o	Kubernetes will create or delete Pods to maintain the desired number.
o	ReplicaSets are often used as building blocks for other controllers like Deployments.
Deployment
•	Purpose: Declares the desired state of an application, including the number of replicas and the image to use.
•	Use Cases: Deploying and managing applications with rolling updates, canary deployments, and A/B testing.
•	Characteristics:
o	Deployments use ReplicaSets to manage the desired number of Pods.
o	Deployments can be updated with new images or configurations without downtime.
o	Deployments provide features like rolling updates, canary deployments, and A/B testing.
StatefulSet
•	Purpose: Ensures that Pods are created in a specific order and have persistent storage.
•	Use Cases: Deploying applications that require persistent storage or a specific order of creation.
•	Characteristics:
o	StatefulSets guarantee the order of Pod creation and deletion.
o	StatefulSets can be configured to use persistent volumes.
o	StatefulSets are often used for applications like databases or stateful services.

# ---------- Nginx Deployment with RollingUpdate ----------
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: app
spec:
  replicas: 3                          		 # Run 3 replicas
  strategy:
    type: RollingUpdate                 	# Rolling update strategy
    rollingUpdate:
      maxUnavailable: 1                 	# At most 1 pod can be unavailable during update
      maxSurge: 1                       	# At most 1 extra pod can be created during update
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
        image: nginx:1.25.3            	 # Specific version for control
        ports:
        - containerPort: 80


Prometheus/Grafana
Default dashboard 3662, We can use or customize 

Linux
touch  To create empty file
>filename  To remove the content from file
cat >> file name  To write the file
chomod  To change the file and folder permissions [4 Read + 2 Write + 1 Execute]
-rwxr-xr-x  1st char [- / d] represents file or dir. Next 3 permissions for Owner, Group and Other resp.
grep  Filer out the o/p [e.g.  cat filename | grep “name”]
awk  Print the particular field from o/p [e.g.  cat filename | awk -F ‘ ‘  ‘{print $1, $2}’]

curl  browse form internet or download, can also be used to upload file
•	Uploading files: curl -X POST -F file=@my_file.txt https://example.com/upload
•	Making HTTP requests: curl -X GET https://api.example.com/data
•	Sending and receiving data: curl -d "name=John, age=30" https://example.com/submit
wget Download from internet
•	Recursive downloading (downloading entire directories)
•	Mirroring (creating a complete copy of a website)
•	Timeouts and retries
•	User authentication

   find  Finding particular file if u don’t the location
	e.g. sudo find / -name filename.txt    All directory 
	        find . -name “filename.txt”   current directory 
trap  to trap the signal of any command
e.g. ctrl C to stop the execution [trap echo “msg” SIGINT]
Kill  To kill the running process
e.g.       kill -9 #pid [-9 flag for forcefully termination]
kill -15 #pid [-15 flag for gracefully termination means after the completion of process it will terminate the process]

if/else loop
	if [Expression]
		then
		else
	fi
Ex. a=2   b=5
if [ $1 > $2]
then 
	echo “a is the largest number “
else
	echo “b is the largest number “
fi
if [ -z "$1”]; then 
# commands to execute if 1st argument is empty 
Fi

# Check if at least one command line argument is provided 
if [ "$#" -eq 0]; then 
echo "No command line arguments provided." 
exit 1 
fi
for loop
	for i in {1.100}; 
		do echo “number is $i”;
	done	      
•	ps - ef  List all running processes
•	df - h  Disk utilisation
•	du  Displays disk space usage of files and directories
•	top  To check CPU utilization, running process, user and more details
•	free  Available space
•	nproc  Available CPU
•	netstat – tulpn  To check open ports and process
•	systemctl status svcname  To check the status of installed service
•	type svcname  To check whether it is installed or not
•	svcname -V or --version  To check the current version
•	traceroute <destination_host>  Network diagnostic
•	Execute shell script  bash filename.sh       ./filename.sh
•	set -x   run in debug mode
•	set -e   Exit when error
•	set -o pipefail  Exit when pipe fail
•	mkdir  To create directory 
•	rmdir  To remove directory
•	rm -rf  To remove Forcefully and recursively 
•	pwd  Prints the current working directory.
•	Head  Prints the first few lines of a file.
•	tail  Prints the last few lines of a file. 
•	sed  Stream editor for manipulating text.
•	uname  Prints system information (kernel version, hostname, etc.). 
•	whoami  Displays the current user's username.
•	sudo su – uname  To switch user with full access
•	sudo su uname  To switch user with limited access
•	man  Displays the manual page for a command. 
•	history  Displays a list of previously executed commands.
•	sudo useradd uname  To create new user
•	sudo groupadd groupname  To create group
•	sudo usermod -a -G groupname username  To add a user to an existing group


GitHub
git init  To initialize local repo 
git add . ; git commit -m “commit msg”; git push   To track changes, commit and push to remote repo
git remote -v  To check the remote repo for local repo
git branch  To check current branch name
git pull origin  To pull the changes from remote
git branch -r  To check the available branches
git branch branch-name  To create a new branch
git checkout branch-name  To switch branch
git checkout -b branch-name  To create and switch to new branch
git log  To check the logs of current branch
git log branch-name  To check the logs for particular branch
git log –oneline  To check the log in single line per commit
git cherry-pick commit-hash  To merge particular commit to current branch
git fetch branch-name  To fetch changes form particular branch to current branch
git merge branch-name  To merge changes form particular branch to current branch
git rebase branch-name  To merge changes form particular branch to current branch in linear order [By timestamp]
git reset - - hard commit-hash  To move back to previous commit




 
