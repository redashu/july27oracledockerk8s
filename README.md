
#  K8s  data
##  k8s on aws cloud

## ks8 on mac using minikube 
```
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-darwin-amd64
sudo mv minikube   /usr/local/bin/
sudo chmod +x  /usr/local/bin/minikube 
minikube  start  --driver=hyperkit 
```
---https://kubernetes.io/docs/tasks/tools/install-minikube/

## Installing multinode k8s using kubeadm
```
[root@k8s-master ~]# cat  setup.sh 
yum   install  docker  -y
systemctl  start  docker
systemctl  enable  docker 
swapoff  -a  #  turning  off  swap memory 
modprobe br_netfilter
echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables
#  configure yum repo for kubeadm 

cat  <<EOF  >/etc/yum.repos.d/kube.repo
[kube]
baseurl=https://packages.cloud.google.com/yum/re

pos/kubernetes-el7-x86_64
gpgcheck=0
EOF

#  install kubeadm 
yum  install kubeadm  -y
systemctl  start kubelet
systemctl  enable  kubelet 

```

### optiona steps 
```
[root@k8s-master ~]# cat  /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost6 localhost6.localdomain6

172.31.32.137         k8s-master
172.31.47.113        k8s-minion1
172.31.38.156       k8s-minion2
172.31.45.225       k8s-minion3
```

### Only on master node 
```
 kubeadm  init  --pod-network-cidr=192.168.0.0/16   --apiserver-advertise-address=0.0.0.0 --apiserver-cert-extra-sans=52.204.11.125   
  24   mkdir -p $HOME/.kube
   25   sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
   
 ```
 ###  apply CNI plugin on Master node
 ```
  kubectl apply -f https://docs.projectcalico.org/v3.8/manifests/calico.yaml
  ```
  
  ## k8s client on. Linux 
  ```
   830  curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"
  831  ls
  832  sudo mv  kubectl   /usr/bin/
  833  sudo chmod +x  /usr/bin/kubectl 

[ec2-user@ip-172-31-74-156 ~]$ kubectl   version  --client 
Client Version: version.Info{Major:"1", Minor:"18", GitVersion:"v1.18.6", GitCommit:"dff82dc0de47299ab66c83c626e08b245ab19037", GitTreeState:"clean", BuildDate:"2020-07-15T16:58:53Z", GoVersion:"go1.13.9", Compiler:"gc", Platform:"linux/amd64"}

```

## kubectl on mac 
```
 510  curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/darwin/amd64/kubectl"
 
  514  sudo mv kubectl   /usr/local/bin/
  515  sudo chmod +x  /usr/local/bin/kubectl
  
 ashutoshhs-MacBook-Air:~ fire$ kubectl  version  --client
Client Version: version.Info{Major:"1", Minor:"18", GitVersion:"v1.18.6", GitCommit:"dff82dc0de47299ab66c83c626e08b245ab19037", GitTreeState:"clean", BuildDate:"2020-07-15T16:58:53Z", GoVersion:"go1.13.9", Compiler:"gc", Platform:"darwin/amd64"}

```

## kubectl. on. windows 

```
download https://storage.googleapis.com/kubernetes-release/release/v1.18.0/bin/windows/amd64/kubectl.exe
add this in a PATH 
then open cmd | powershell

-- kubectl version --client

```

### Download token file from master to client 
```
[root@k8s-master kubernetes]# cat   /etc/kubernetes/admin.conf  
apiVersion: v1
clusters:
- cluster:

```

### testing connection to master from client 
```
[ec2-user@ip-172-31-74-156 ~]$ kubectl   get  nodes  --kubeconfig  admin.conf 
NAME          STATUS   ROLES    AGE   VERSION
k8s-master    Ready    master   26m   v1.18.6
k8s-minion1   Ready    <none>   26m   v1.18.6
k8s-minion2   Ready    <none>   26m   v1.18.6
k8s-minion3   Ready    <none>   26m   v1.18.6


----
[ec2-user@ip-172-31-74-156 ~]$ kubectl   version    --kubeconfig  admin.conf  
Client Version: version.Info{Major:"1", Minor:"18", GitVersion:"v1.18.6", GitCommit:"dff82dc0de47299ab66c83c626e08b245ab19037", GitTreeState:"clean", BuildDate:"2020-07-15T16:58:53Z", GoVersion:"go1.13.9", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"18", GitVersion:"v1.18.6", GitCommit:"dff82dc0de47299ab66c83c626e08b245ab19037", GitTreeState:"clean", BuildDate:"2020-07-15T16:51:04Z", GoVersion:"go1.13.9", Compiler:"gc", Platform:"linux/amd64"}

----

[ec2-user@ip-172-31-74-156 ~]$ kubectl  cluster-info     --kubeconfig  admin.conf  
Kubernetes master is running at https://52.204.11.125:6443
KubeDNS is running at https://52.204.11.125:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

```


###  copy admin.conf/token file to  .kube folder on client machine

```
856  mkdir  .kube
  857  ls   -a
  858  ls
  859  cp  admin.conf  .kube/config 
  
  ```
  
  # Pods 
  
  ## creating nginx based pod
  ```
  [ec2-user@ip-172-31-74-156 pods]$ cat  ashupod1.yml 
apiVersion: v1  # apiver server version of k8s master
kind: Pod  #  requsting pod to deploy 
metadata:  #  info about pod
 name: ashu-pod1   #  name of pod 
spec:  # here we right about application specifications
 containers:
 - image: nginx  # this image will be pull from Docker hub only 
   name: ashuc1  #  name of container  optional 
   ports:  #  this is completely optional field 
   - containerPort: 80  #  same as we defined in Dockerfile under expose instruction
   
 =====
 
  kubectl apply  -f  ashupod1.yml 
  
  ====
   928  kubectl  get  pod
  929  kubectl  get  pod -o wide 
  
  ```
  
  ### Pod operations 
  ```
   kubectl  describe  pod  ashu-pod1 
   
   ```
   
   ### access pod using exec
   ```
   [ec2-user@ip-172-31-74-156 pods]$ kubectl  exec  -it  ashu-pod1  bash 
kubectl exec [POD] [COMMAND] is DEPRECATED and will be removed in a future version. Use kubectl kubectl exec [POD] -- [COMMAND] instead.
root@ashu-pod1:/# 
root@ashu-pod1:/# 
root@ashu-pod1:/# uname 
Linux
root@ashu-pod1:/# uname  -r
4.14.181-140.257.amzn2.x86_64
root@ashu-pod1:/# cat  /etc/os-release 
PRETTY_NAME="Debian GNU/Linux 10 (buster)"
NAME="Debian GNU/Linux"
VERSION_ID="10"
VERSION="10 (buster)"
VERSION_CODENAME=buster
ID=debian
HOME_URL="https://www.debian.org/"
SUPPORT_URL="https://www.debian.org/support"
BUG_REPORT_URL="https://bugs.debian.org/"

```

### taking help from kubectl 
```
  943  kubectl  explain  pods 
  944  kubectl  explain  pods.apiVersion 
  945  kubectl  explain  pods.kind
  946  kubectl  explain  pods.spec
  947  kubectl  explain  pods.spec.containers

```

### deleting pods
```
  950  kubectl  delete pods  ashu-pod1  
  951  kubectl get  po 
  952  kubectl delete  pods  --all
```
### generating yaml and json files 
```
 957  kubectl  run ashupod2 --image=nginx  
  958  kubectl get  po
  959  kubectl delete pods  --all 
  960  kubectl  run ashupod2 --image=nginx   --dry-run
  961  kubectl  run ashupod2 --image=nginx   --dry-run=client 
  962  kubectl get  po
  963  kubectl  run ashupod2 --image=nginx   --dry-run=client -o yaml
  964  kubectl  run ashupod2 --image=nginx   --dry-run=client -o json 
  965  history 
  966  kubectl  run ashupod2 --image=nginx   --dry-run=client -o yaml >ashupod2.yml 
  967  kubectl  run ashupod2 --image=nginx   --dry-run=client -o json  >ashupod2.json 
  968  ls
  969  cat  ashupod2.
  970  cat  ashupod2.yml 
  971  kubectl  run ashupod2 --image=nginx --port 80   --dry-run=client -o yaml 
```


## pods with labels
```
[ec2-user@ip-172-31-74-156 pods]$ cat  ashupod1.yml 
apiVersion: v1  # apiver server version of k8s master
kind: Pod  #  requsting pod to deploy 
metadata:  #  info about pod
 name: ashu-pod1   #  name of pod 
 labels:  #  defining  label is must if we want to access application 
  x: ashuapp1   #  key and value format 
spec:  # here we right about application specifications
 containers:
 - image: nginx  # this image will be pull from Docker hub only 
   name: ashuc1  #  name of container  optional 
   ports:  #  this is completely optional field 
   - containerPort: 80  #  same as we defined in Dockerfile under expose instruction 
   
  ```
  
  ### checking labels
  ```
    993  kubectl  get  po  --show-labels
  994  kubectl  get  po ashu-pod1   --show-labels
  995  kubectl  get  po ashu-pod1 -o wide   --show-labels
  996  kubectl  get  po  -o wide   --show-labels
  
  ```
  
  ## service. creation
  
  ### NodePort 
  ```
  [ec2-user@ip-172-31-74-156 pods]$ cat  ashuservice1.yml 
apiVersion: v1
kind: Service
metadata:
 name: ashu-service-1   #  name of service 

spec:
 ports:
 - name: myport  #  optional field 
   port: 1234  #  this  is service port number this field is must 
   protocol: TCP  #  optional field 
   targetPort: 80  #  this port number must same as POd port where you app is running (optional field)
 selector:  #  this is search for labels of pod 
  x: ashuapp1  #  this label must match your respected  POD 
 type: NodePort   #  type  of  service  we want to create 
 
 ```
 
 ### serivce create automatically
 ```
  1018  kubectl  create  service   nodeport  ashusvc2 --tcp  1239:80 --dry-run=client 
 1019  kubectl  create  service   nodeport  ashusvc2 --tcp  1239:80 --dry-run=client -o yaml
 1020  kubectl  create  service   nodeport  ashusvc2 --tcp  1239:80 --dry-run=client -o json
 1021  kubectl  create  service   nodeport  ashusvc2 --tcp  1239:80 --dry-run=client -o yaml
 1022  kubectl  create  service   nodeport  ashusvc2 --tcp  1239:80 --dry-run=client -o yaml >ashusvc2.yml 
 ```
 
  ### expose pod to create service
  ```
   1008  kubectl  get   po  
 1009  kubectl  expose  pod  ashu-pod1  --type NodePort --port 1290  --target-port 80 --name ashusvc3  --dry-run=client  -o yaml 
 1010  kubectl  expose  pod  ashu-pod1  --type NodePort --port 1290  --target-port 80 --name ashusvc3  
 
 ---
 [ec2-user@ip-172-31-74-156 pods]$ kubectl  get  svc
NAME              TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
arun-service1     NodePort    10.109.70.32     <none>        1729:31241/TCP   13m
ashusvc2          NodePort    10.107.0.236     <none>        1239:32016/TCP   13m
ashusvc3          NodePort    10.108.137.46    <none>        1290:30528/TCP   48s
bipin-service     NodePort    10.101.225.3     <none>        1234:30119/TCP   12m
geetaservice2     NodePort    10.97.94.233     <none>        1237:30215/TCP   10m
janani1           NodePort    10.97.127.182    <none>        9000:30471/TCP   11m
kubernetes        ClusterIP   10.96.0.1        <none>        443/TCP          19m
pankajs2          NodePort    10.111.9.171     <none>        8043:30902/TCP   13m
rvsvc2            NodePort    10.101.163.183   <none>        1240:30196/TCP   11m
sach-service-2    NodePort    10.111.122.78    <none>        1454:31839/TCP   18m
sach-service2     ClusterIP   10.110.47.7      <none>        1444/TCP         3m5s
sowsvc2           NodePort    10.108.27.134    <none>        5432:30783/TCP   13m
sowsvc3           NodePort    10.103.86.97     <none>        7654:31997/TCP   38s
sri-svc-3         NodePort    10.100.71.245    <none>        1600:30605/TCP   12m
sureshservice12   NodePort    10.100.8.143     <none>        8091:31896/TCP   13m

 ```

## tricks
```
 1017  kubectl  get  po,svc
 1018  kubectl delete  all  --all 
 ```
 
## pod + service in single YAML
```
 1022  kubectl  run  ashu-pod5  --image=dockerashu/multiapp:ashuv1july282020 --port 80 --dry-run=client  -o yaml  
 1023  kubectl  run  ashu-pod5  --image=dockerashu/multiapp:ashuv1july282020 --port 80 --dry-run=client  -o yaml   >ashupod-svc.yml 
 kubectl  create  service  nodeport  ashusvc5  --tcp 9090:80 --dry-run=client -o yaml  >>ashupod-svc.yml
 
 ====
 [ec2-user@ip-172-31-74-156 pods]$ cat ashupod-svc.yml 
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:  # label of pods 
    run: ashu-pod5   #  labels 
  name: ashu-pod5  # name of  the pod 
spec:
  containers:
  - image: dockerashu/multiapp:ashuv1july282020  # docker  image  from docker hub 
    name: ashu-pod5  #  name of  container 
    ports:
    - containerPort: 80  #  application port writter in Dockerfile under expose instruction 
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}


---
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:  # optional 
    app: ashusvc5
  name: ashusvc5  # name of  service 
spec:
  ports:
  - name: 9090-80
    port: 9090  #  service port 
    protocol: TCP
    targetPort: 80  #  pod port 
  selector:
   run: ashu-pod5  #  mathching exact  same label of  PODs 
  type: NodePort  #  type  of  service  run: ashu-pod5
  
  ===
  
   kubectl  apply -f  ashupod-svc.yml  
 1035  kubectl  get  po,svc

```

  
