Kubernetes
===========

### Kubernetes Installation

1. Playwithk8s - https://labs.play-with-k8s.com
2. Minikube
3. kubeadm
4. google kubernetes Engine
5. Amazon EKS
6. Azure Kubernetes service (AKS)

#### Play with K8S

* Github or Docker account is required
* Creates K8s cluster in seconds
* Four hours time limit
* Default 8 instance can be created


### Setting up k8s

#### Initializing Master node

```
kubeadm init --apiserver-advertise-address $(hostname -i) --pod-network-cidr 10.5.0.0/16
```

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

#### Initialize Routing policies

```
kubectl apply -f https://raw.githubusercontent.com/cloudnativelabs/kube-router/master/daemonset/kubeadm-kuberouter.yaml
```

#### Joining the worker nodes to the cluster

```
kubeadm join 192.168.0.30:6443 --token 9jgq70.kjjpjn1e45elfl0k   \
  --discovery-token-ca-cert-hash \
  sha256:aefa158639e38d52ae722f6fd6f0139eaa9b5ca0384e5c6eea59ca934f58ed05
```

#### To display the join command if forgot to copy

```
kubeadm token create --print-join-command
```


#### Deploying sample nginx app from Master node

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/website/master/content/en/examples/application/nginx-app.yaml
```


#### To get the node and pod status

```
$ kubectl get nodes
NAME    STATUS   ROLES                  AGE   VERSION
node1   Ready    <none>                 17m   v1.20.1
node2   Ready    <none>                 16m   v1.20.1
node3   Ready    <none>                 16m   v1.20.1
node4   Ready    control-plane,master   25m   v1.20.1
```

```
[node4 ~]$ kubectl get pods
NAME                        READY   STATUS    RESTARTS   AGE
my-nginx-66b6c48dd5-cq4bc   1/1     Running   0          11m
my-nginx-66b6c48dd5-hbtfh   1/1     Running   0          11m
my-nginx-66b6c48dd5-kdrr8   1/1     Running   0          11m
```

```
[node4 ~]$ kubectl get pods
NAME                        READY   STATUS    RESTARTS   AGE
my-nginx-66b6c48dd5-cq4bc   1/1     Running   0          11m
my-nginx-66b6c48dd5-hbtfh   1/1     Running   0          11m
my-nginx-66b6c48dd5-kdrr8   1/1     Running   0          11m
```

### Installing Minikube

#### Setting up Environment with Minikube 

1. Downloading and Installing Minikube

```
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

2. Downloading and installing Kubectl

```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

3. To setup minikube default driver as Virtualbox

```
minikube config set driver virtualbox
```
OR
```
minikube start --driver=virtualbox
```

4. To Start Minikube - This will start minikube and Install required k8s utilities

```
$ minikube start
😄  minikube v1.19.0 on Ubuntu 18.04
✨  Using the virtualbox driver based on user configuration
👍  Starting control plane node minikube in cluster minikube
🔥  Creating virtualbox VM (CPUs=2, Memory=2200MB, Disk=20000MB) ...
🎉  minikube 1.21.0 is available! Download it: https://github.com/kubernetes/minikube/releases/tag/v1.21.0
💡  To disable this notice, run: 'minikube config set WantUpdateNotification false'

🐳  Preparing Kubernetes v1.20.2 on Docker 20.10.4 ...
    ▪ Generating certificates and keys ...
    ▪ Booting up control plane ...
    ▪ Configuring RBAC rules ...
🔎  Verifying Kubernetes components...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🌟  Enabled addons: storage-provisioner, default-storageclass
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```

5. To verify the status of Minikube

```
$  minikube status
minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
```

6. To View Minikube version

```
$ minikube version
minikube version: v1.19.0
commit: 15cede53bdc5fe242228853e737333b09d4336b5
```

7. To View the list of nodes [add -o wide option to view in detail]

```
$  kubectl get nodes
NAME       STATUS   ROLES                  AGE    VERSION
minikube   Ready    control-plane,master   4m7s   v1.20.2
```

8. Lets test it with a sample application pods

```
$  kubectl run kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1 --port=8080
pod/kubernetes-bootcamp created
```

9. To View the list of pods

```
$  kubectl get pods -o wide
NAME                  READY   STATUS    RESTARTS   AGE   IP           NODE       NOMINATED NODE   READINESS GATES
kubernetes-bootcamp   1/1     Running   0          76s   172.17.0.3   minikube   <none>           <none>
```


10. To stop the minikube

```
 sundar  ~  minikube stop
✋  Stopping node "minikube"  ...
🛑  1 nodes stopped.
```


### Installing and configuring with Kubeadm

####  Kubeadm 

kubeadm helps with installing and configuring kubernetes cluster

#### Kubeadm Commands

kubeadm init - configure node as master node (to be run on master node)
kubeadm init [flags]

kubeadm join - initializes the worker node (to be run on worker node)

kubeadm join --token [] --discovery-token-ca-cert-hash []

kubeadm token

kubeadm token [create|delete|list|generate] [flags]


kubeadm version [flags]

kubeadm upgrade

kubeadm upgrade plan [version] [flags]


#### Pre-requisites

* 3 GB or MORE RAM (VMs creating)
* 3 CPU
* Full network connectivity among all the networks
* Disable swap on all nodes
* Disable SELINUX on all nodes


#### Steps

1. Create VMs which are part of K8S  clusters (master and worker nodes)
2. Disable SElinux and Swap on all nodes (swapoff -a)(setenforce 0)
    - swapoff -a
    - setenforce 0
    - sed -i 's/enforcing/disabled/g' /etc/selinux/config
    - grep disabled /etc/selinux/config | grep -v '#'
    - reboot
3. Installing kubeadm, kubectl, kubelet and Docker on all nodes
    -    Start and enable Docker and Kubelet services in all nodes
4. Initialize the master node
5. Configuring pod network
6. Join worker node to master node


### Kubectl

command line utilities for running commands against Kubernetes cluster

```
kubectl [command] [TYPE] [NAME] [flags]
```

[command] - create, get, describe, delete, logs, exec, edit, run, apply, scale
[TYPE] - pods, deployments, replicaset, services, daemonset, namespace, 
                persistentvolume, persistentvolumeclaim, jobs, cronjobs

#### Create

Used to create resource from file

```
kubectl create -f pod-example.yaml
```

or

```
kubectl create -f pod-example.json
```
or

```
kubectl create -f <directory>
```

#### get

To display the resources 

```
kubectl get pods 
kubectl get pods <pod-name>
kubectl get pods -o wide
kubectl get pods,deploy #To view multiple resources
```


#### describe

Display detailed state of one or more resources

```
 kubectl describe nodes
 kubectl describe nodes <node-name>
  kubectl describe pods
  kubectl describe pods <pod-name>
  
```

#### delete

To delete resources

```
kubectl delete -f  pod.yaml
kubectl delete pods, services -l name=<label-name>
kubectl delete pods --all
```


#### exec

Execute commands against the containers in pods

```
kubectl exec <pod-name> date
kubectl exec <pod-name> -c <container-name> date
kubectl exec -ti <pod-name> -c <container-name> /bin/bash
```

#### logs

Print the logs of the container

```
kubectl logs <pod-name>
kubectl logs <pod-name> -f
```

### Pods

#### What is Pod?
Pods are basic unit of scheduling in Kubernetes. Every pod has a container 

#### Pod Deployment

* Pod can be deployed through Manifest file with container image details.
* This manifest file deploys application through Api server.
* API servers deploy pods on appropriate worker nodes.
* To scale them we need to create new pods with the same container (app instance)

#### Multi Container
* Usually pod has one container each. some cases there will be supporting containers in single pod too
* In those cases they are differentiated with port numbers

#### Pod Networking
* Every pod in the worker node has an IP. 
* And every container shares the localhost IP of the pod


#### Inter-Pod & Intra-Pod Communication
* Interpod networking is a communication between two pods
* Intrapod networking - two or more container in the pods communicate each other sharing the localhost

#### Pod Lifecycle
* As soon as pod created from manifest file it will be in pending state
* Some other states are running state, succeeded state, failed state

#### Pod Manifest File

This is File with configuration of the pods to be created
```
#nginx-pod.yml
apiVersion: v1
kind: Pod
metadata:
    name: nginx-pod
    labels:
        app: nginx
        tier: dev
spec:
    containers:
    - name: nginx-container
      image: nginx
```

**kind**
Pod, ReplicationController, Service, ReplicaSet, Deployment, DaemonSet, Job

We have to create pod like the below

```
kubectl create -f nginx-pod.yml
```

To delete the pod 
```
kubectl delete pod nginx-pod
```

To get into pod shell within a pod

```
kubectl exec -it nginx-pod -- /bin/sh
```

### Deploying Simple web app with minikube

1. Create minikube cluster with virtualbox in bridged mode

```
minikube start --host-only-cidr "192.168.99.1/24"
```

2. Creating Pod

```
kubectl create  -f nginx-pod.yml
```

3. Entering into the pod and creating test html page

```
$ kubectl exec -it nginx-pod -- /bin/sh

#cat <<EOF > /usr/share/nginx/html/test.html
<!DOCTYPE html>
<html>
<head>
<title>Testing</title>
<body>
<h1> Hello, Kubernetes...! </h1>
</body>
</html>
EOF
exit
```
4. Creating service with Nodeport type and exposing port to outside world

```
kubectl expose pod nginx-pod --type=NodePort --port=80
```

5. Use describe command to identify the port 

```
$  kubectl describe svc nginx-pod
Name:                     nginx-pod
Namespace:                default
Labels:                   app=nginx
                          tier=dev
Annotations:              <none>
Selector:                 app=nginx,tier=dev
Type:                     NodePort
IP:                       10.98.151.15
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  30194/TCP
Endpoints:                172.17.0.3:80
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
```

6. You can Access the app from below url

```
http://<nodeip>:<NodePort>/test.html
```

7. To Delete  the svc and pod

```
kubectl delete svc nginx-pod
kubectl delete pod nginx-pod
```

### ConfigMaps

#### Configuring container apps

* Container images are build to be portable
* Containers expect configuration from 
    * Config files (INI, XML, JSON, Custom Format)
    * Command line arguments
    * Environment variable

#### Configmaps

* configmaps allows to separate pods and configuration
* prevents hardcoding config data
* Stores configuration as key value pairs 
* similar to secrets but don't contain sensitive information
* you must create a configmap before referencing it in a pod spec


#### Creating configmaps

```
kubectl create configmap <mapname> <data-source>
```

<data-source> - pathto directory/file (--from-file), key-value pair (--from-literal)

##### From directories

```
mkdir -p configure-pod-container/configmap/kubectl/
wget https://kubernetes.io/examples/configmap/game.properties -O configure-pod-container/configmap/game.properties
wget https://kubernetes.io/examples/configmap/ui.properties -O configure-pod-container/configmap/ui.properties
kubectl create configmap game-config --from-file=configure-pod-container/configmap/kubectl/
kubectl get configmap -o wide
```

##### From File

```
curl -OL 
cat redis-config
kubectl create configmap example-redis-config --from-file=redis-config
```
using in pod spec

```
apiVersion: v1
kind: Pod
metadata:
    name: redis
spec:
    containers:
    - name: redis
       image: kubernetes/redis:v1
       volumeMounts:
       - mountPath: /redis-master
         name: config       
volumes:
    - name: config
       configMap:
           name: example-redis-config
           items:
               - key: redis-config
                  path: redis.conf
```

```
kubectl create pod -f redis.yml
kubectl exec redis cat /redis-master/redis.conf
```

#### Configmap in command line (Literal)

```
kubectl create configmap special-config --from-literal=special.how=very
```

```
apiVersion: v1
kind: Pod
metadata: 
    name: test-pod
spec:
    containers:
    - name: test-container
      image: k8s.gcr.io/busybox
      command: ["/bin/sh", "-c", "env"]
      env:
         - name: SPECIAL_LEVEL_KEY
            valueFrom:
                configMapKeyRef:
                    name: special-config
                    key: special.how
     restartPolicy: Never
```
```
kubectl create pod -f test-pod.yml
```

```
kubectl logs test-pod | grep SPECIAL
```

#### Example for Configmaps from File

1. Creating two files with Configurations

```
echo -n 'Non-sensitive data inside file-1' > file-1.txt
echo -n 'Non-sensitive data inside file-2' > file-2.txt
```

2. Create configmaps with kubectl 

```
kubectl create configmap nginx-configmap-vol --from-file=file-1.txt --from-file=file-2.txt
```

3. To display configmaps and its details

```
kubectl get configmaps
kubectl describe configmaps nginx-configmap-vol
```

4. Configuring configmap in pod manifest file spec

```
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod-configmap
spec:
  containers:
  - name: nginx-container
    image: nginx
    volumeMounts:
    - name: test-vol
      mountPath: "/etc/non-sensitive-data"
      readOnly: true
  volumes:
  - name: test-vol
    configMap:
      name: nginx-configmap-vol
      items:
      - key: file-1.txt
        path: file-a.txt
      - key: file-2.txt
        path: file-b.txt
```


5. Create Pod with the manifest file created

```
kubectl create -f nginx-pod-configmap.yaml
```

6. Now check if the configuration files are moved as specified in manifest file

```
kubectl exec nginx-pod-configmap -it /bin/sh
cd /etc/non-sensitive-data/
cat file-a.txt
cat file-b.txt
```

#### Example to create configmap with literals

1. Create configmap using --from-literal tag

```
kubectl create configmap redis-configmap-env --from-literal=file.1=file.a --from-literal=file.2=file.b
```

2. Create pod manifest file and add those configs as environment variables 

```
apiVersion: v1
kind: Pod
metadata:
  name: redis-pod-configmap-env
spec:
  containers:
  - name: redis-container
    image: redis
    env:
      - name: FILE_1
        valueFrom:
          configMapKeyRef:
            name: redis-configmap-env
            key: file.1
      - name: FILE_2
        valueFrom:
          configMapKeyRef:
            name: redis-configmap-env
            key: file.2
  restartPolicy: Never
```

3. Create pod with the manifest file

```
kubectl create -f redis-pod-configmap-env.yaml
```

4. Check if the environment variables are created in pod

```
kubectl exec -it redis-pod-configmap-env -- /bin/sh
```

```
# env | grep file
FILE_1=file.a
FILE_2=file.b
```
5. Alternate way to check the same

```
kubectl exec -it redis-pod-configmap-env -- env | grep FILE
```

#### Example to clean up the environment


1. Delete pods

```
kubectl delete pods nginx-pod-configmap redis-pod-configmap-env 
```

2. Delete Configmaps

```
kubectl delete configmaps nginx-configmap-vol redis-configmap-env
```


### Secrets

- Secrets are Kubenetes object to handle small amount of sensitive data
- Reduce risk of exposing sensitive data
- This can be password, token or keys
- Created outside of pod
- stored in ETCD database in kubernetes master
- Maximum size of secret file is 1 MB
- can be used in two ways 
    1. Volumes
    2. Environment variables
- Secret sent only to the target nodes
- stored in tmpfs so other nodes cannot access it

#### Creating Secret with Kubectl

```
kubectl create secret [Type] [Name] [Data]
```

[Type] - File, directory, literal value
[Data] - path to file, key value pair (--from-file, --from-literal)
[Name] - Name of the secret


1. Create admin and password file

```
echo -n 'admin' > ./username.txt
echo -n 'password@1234' > ./password.txt
```

2. Creating the secret object in kubernetes

```
kubectl create secret generic db-user-pass --from-file=./username.txt --from-file=./password.txt
```

3. Describe the secrets 

```
kubectl describe secrets db-user-pass
Name:         db-user-pass
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
username.txt:  5 bytes
password.txt:  13 bytes
```

#### Creating the secrets manually using literals

1. encode the username and password with base64

```
$ echo -n 'admin' | base64
YWRtaW4=
$ echo -n 'password@123' | base64
cGFzc3dvcmRAMTIz
```

2. Create secrets manifest file

```
cat mysecret.yml 
# mysecret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  username: YWRtaW4=
  password: cGFzc3dvcmRAMTIz 
```

3. Create secrets

```
kubectl create -f mysecret.yml
```

4. Decoding secrets

```
$ echo -n 'YWRtaW4=' | base64 --decode
admin
$ echo -n 'cGFzc3dvcmRAMTIz' | base64 --decode
password@123
```

#### Using secrets with volume

1. Create manifest file and specify mounts and secrets

```
apiVersion: v1
kind: Pod
metadata:
  name: mypod-with-secret
spec:
  containers:
  - name: mypod-with-secret
    image: redis
    volumeMounts:
    - name: foo
      mountPath: "/etc/foo"
      readOnly: true
  volumes:
  - name: foo
    secret:
      secretName: mysecret 
```

2. Test it

```
kubectl exec mypod-with-secret cat /etc/foo/username
kubectl exec mypod-with-secret cat /etc/foo/password
```

#### Using secrets with Environment variable

1. Create pod manifest file with ENV variables

```
apiVersion: v1
kind: Pod
metadata:
  name: mypod-with-secret-env
spec:
  containers:
  - name: mypod-with-secret-env
    image: redis
    env:
      - name: SECRET_USERNAME
        valueFrom:
          secretKeyRef:
            name: mysecret
            key: username
      - name: SECRET_PASSWORD
        valueFrom:
          secretKeyRef:
            name: mysecret
            key: password      
  restartPolicy: Never
```

2. Create pod

```
kubectl create -f secret-pod-env.yml
```

3. Display the Environment variables

```
kubectl exec mypod-with-secret-env env| grep SECRET
```

#### Cleanup

```
kubectl delete pods .....
kubectl delete secrets .....
```


### Replication Controller

* Ensures that a specifed number of pods are running at any time
    * If there are excess pods, they are killed and vice versa
    * New pods are launched whey get fail, get deleted or terminated
* Replication controllers and pods are associated with labels
* creating a "rc" with count of 1 ensures that a pod is always available

**Advantages**

- High availability
- Load balancing

Replication Controller - OLD
ReplicaSet - New

#### Implementing Replication controller

1. Manifest File

```
# nginx-rc.yml
apiVersion: v1
kind: ReplicationController
metadata:
  name: nginx-rc
spec:
  replicas: 3
  selector:
    app: nginx-app
  template:
    metadata:
      name: nginx-pod
      labels:
        app: nginx-app
    spec:
      containers:
      - name: nginx-container
        image: nginx
        ports:
        - containerPort: 80
```

2. Deploy app using RC

```
kubectl create -f nginx-rc.yaml
```
3. Display and Validate RC

viewing the pods with label

```
kubectl get pods -l app=nginx-app
```

```
kubectl describe rc nginx-rc
```

4. Test - node fails

```
kubectl get pods -o wide
```

kubectl get nodes    


5. Test - scale up

```
kubectl scale rc nginx-rc --replicas=5
```

```
kubectl get rc nginx-rc
```

6. Test - Scale down

```
kubectl scale rc nginx-rc --replicas=3
```

7. cleanup

kubectl delete -f nginx-rc.yaml

