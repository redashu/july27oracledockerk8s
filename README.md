
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
# Replication COntroller 

## example 1
```
[ec2-user@ip-172-31-74-156 rc]$ cat  ashu-rc1.yaml 
apiVersion: v1
kind: ReplicationController 
metadata:
 name: ashu-rc-web1  #  name of rc 
spec:
 replicas: 2   #  NO OF pods we want to deploy 
 template:
  metadata:
   name: ashupod111   #  name of pod (optional field ) bcz rc will give a random name
   labels:
    x: ashuwebpod  #  labels of both the pods 
  spec:
   containers:
   - name: ashuc1 
     image: nginx
     ports:
     - containerPort: 80
     
     
     kubectl apply -f  ashu-rc1.yaml 
     kubectl  expose  rc  ashu-rc-web1  --type NodePort  --port 1244  --target-port  80  --name ashusvc111 
     [ec2-user@ip-172-31-74-156 rc]$ kubectl  get  svc
NAME                TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
arun-service1       NodePort    10.104.87.61     <none>        1729:30307/TCP   53s
ashusvc111          NodePort    10.111.10.209    <none>        1244:30674/TCP   82s
kubernetes          ClusterIP   10.96.0.1        <none>        443/TCP          27m
pankaj-rc-service   NodePort    10.110.96.229    <none>        2356:30940/TCP   35s
rvsvc5              NodePort    10.101.114.43    <none>        1300:30392/TCP   27s
sachsvc007          NodePort    10.105.215.152   <none>        1255:30942/TCP   50s
sowsvc68            NodePort    10.109.205.103   <none>        7654:31769/TCP   4s
sri-rc1             NodePort    10.102.56.199    <none>        2999:31983/TCP   60s
suresh-service-1    NodePort    10.103.98.85     <none>        8091:30008/TCP   5s



```

### increase replica
```
kubectl  scale  rc  ashu-rc-web1  --replicas=10 
 kubectl  edit rc  ashu-rc-web1
```
# Deployment 

```
 kubectl create deployment  ashu-dep1  --image=dockerashu/multiapp:ashuv1july282020  --dry-run=client   -o yaml 
 
 OR
 
  kubectl create deployment  ashu-dep1  --image=dockerashu/multiapp:ashuv1july282020  --dry-run=client   -o yaml          >ashudep-svc.yaml 
  kubectl  create  service  nodeport  ashudep-svc1 --tcp 1212:80 --dry-run=client -o yaml  >>ashudep-svc.yaml    
[ec2-user@ip-172-31-74-156 deployment]$ kubectl  apply -f  ashudep-svc.yaml 
deployment.apps/ashu-dep1 created
service/ashudep-svc1 created
  
```

### listing 
```
[ec2-user@ip-172-31-74-156 deployment]$ kubectl  get  deployment 
NAME           READY   UP-TO-DATE   AVAILABLE   AGE
arun-dep1      1/1     1            1           33s
ashu-dep1      1/1     1            1           30s
bpn-dp         1/1     1            1           17s
geeta-dep1     1/1     1            1           9s
ja-dep         0/1     1            0           21s
pankaj-dp1     1/1     1            1           45s
rv-dep1        1/1     1            1           15s
sach-dep1      1/1     1            1           18s
sow-dep1       2/2     2            2           22s
sri-dep1       1/1     1            1           30s
sudeployment   1/1     1            1           100s
[ec2-user@ip-172-31-74-156 deployment]$ kubectl  get  rs
NAME                      DESIRED   CURRENT   READY   AGE
arun-dep1-68c9d585c8      1         1         1       39s
ashu-dep1-57859fb4d9      1         1         1       36s
bpn-dp-69467cfb99         1         1         1       23s
geeta-dep1-6fd56749c4     1         1         1       15s
ja-dep-7cd77d4b8          1         1         0       27s
pankaj-dp1-58b55974f7     1         1         1       51s
rv-dep1-f778b5c8b         1         1         1       21s
sach-dep1-56d54b699c      1         1         1       24s
sow-dep1-795c8cc5b8       2         2         2       28s
sri-dep1-69d4b69f46       1         1         1       36s
sudeployment-85fb7cc9cd   1         1         1       106s
[ec2-user@ip-172-31-74-156 deployment]$ kubectl  get  po
NAME                            READY   STATUS             RESTARTS   AGE
arun-dep1-68c9d585c8-5xzvs      1/1     Running            0          44s
ashu-dep1-57859fb4d9-8dv4m      1/1     Running            0          41s
bpn-dp-69467cfb99-8jc74         1/1     Running            0          28s
geeta-dep1-6fd56749c4-8ts7t     1/1     Running            0          20s
ja-dep-7cd77d4b8-dxmj4          0/1     ImagePullBackOff   0          32s
pankaj-dp1-58b55974f7-ztpzw     1/1     Running            0          56s
rv-dep1-f778b5c8b-5bmmv         1/1     Running            0          26s
sach-dep1-56d54b699c-qkqsw      1/1     Running            0          29s
sow-dep1-795c8cc5b8-lpp22       1/1     Running            0          33s
sow-dep1-795c8cc5b8-z9dxh       1/1     Running            0          33s
sri-dep1-69d4b69f46-bw4b2       1/1     Running            0          41s
sudeployment-85fb7cc9cd-6lmqx   1/1     Running            0          111s

```
### all listing 
```
[ec2-user@ip-172-31-74-156 deployment]$ kubectl  get  deploy,rs,po,svc
NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/arun-dep1      1/1     1            1           94s
deployment.apps/ashu-dep1      1/1     1            1           91s
deployment.apps/bpn-dp         1/1     1            1           78s
deployment.apps/geeta-dep1     1/1     1            1           70s
deployment.apps/ja-dep         0/1     1            0           82s
deployment.apps/pankaj-dp1     1/1     1            1           106s
deployment.apps/rv-dep1        1/1     1            1           76s
deployment.apps/sach-dep1      1/1     1            1           79s
deployment.apps/sow-dep1       2/2     2            2           83s
deployment.apps/sri-dep1       1/1     1            1           91s
deployment.apps/sudeployment   1/1     1            1           2m41s

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/arun-dep1-68c9d585c8      1         1         1       94s
replicaset.apps/ashu-dep1-57859fb4d9      1         1         1       91s
replicaset.apps/bpn-dp-69467cfb99         1         1         1       78s
replicaset.apps/geeta-dep1-6fd56749c4     1         1         1       70s
replicaset.apps/ja-dep-7cd77d4b8          1         1         0       82s
replicaset.apps/pankaj-dp1-58b55974f7     1         1         1       106s
replicaset.apps/rv-dep1-f778b5c8b         1         1         1       76s
replicaset.apps/sach-dep1-56d54b699c      1         1         1       79s
replicaset.apps/sow-dep1-795c8cc5b8       2         2         2       83s
replicaset.apps/sri-dep1-69d4b69f46       1         1         1       91s
replicaset.apps/sudeployment-85fb7cc9cd   1         1         1       2m41s

NAME                                READY   STATUS             RESTARTS   AGE
pod/arun-dep1-68c9d585c8-5xzvs      1/1     Running            0          94s
pod/ashu-dep1-57859fb4d9-8dv4m      1/1     Running            0          91s
pod/bpn-dp-69467cfb99-8jc74         1/1     Running            0          78s
pod/geeta-dep1-6fd56749c4-8ts7t     1/1     Running            0          70s
pod/ja-dep-7cd77d4b8-dxmj4          0/1     ImagePullBackOff   0          82s
pod/pankaj-dp1-58b55974f7-ztpzw     1/1     Running            0          106s
pod/rv-dep1-f778b5c8b-5bmmv         1/1     Running            0          76s
pod/sach-dep1-56d54b699c-qkqsw      1/1     Running            0          79s
pod/sow-dep1-795c8cc5b8-lpp22       1/1     Running            0          83s
pod/sow-dep1-795c8cc5b8-z9dxh       1/1     Running            0          83s
pod/sri-dep1-69d4b69f46-bw4b2       1/1     Running            0          91s
pod/sudeployment-85fb7cc9cd-6lmqx   1/1     Running            0          2m41s

NAME                     TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
service/arun-svc1        NodePort    10.97.124.228    <none>        1729:30958/TCP   94s
service/ashudep-svc1     NodePort    10.98.73.89      <none>        1212:30907/TCP   91s
service/bipin-svc        NodePort    10.102.9.190     <none>        1234:32254/TCP   77s
service/geetadepl-svc1   NodePort    10.108.135.32    <none>        1217:32740/TCP   70s
service/ja-dep-svc1      NodePort    10.104.238.148   <none>        1111:30226/TCP   82s
service/kubernetes       ClusterIP   10.96.0.1        <none>        443/TCP          50m
service/pankaj-dep-sv1   NodePort    10.101.183.127   <none>        4564:31597/TCP   106s
service/rvdep-svc1       NodePort    10.101.178.230   <none>        1214:30154/TCP   76s
service/sachdep-svc1     NodePort    10.107.186.73    <none>        1244:32025/TCP   79s
service/sow-dep-svc      NodePort    10.103.150.98    <none>        5645:30389/TCP   83s
service/sri-svc1         NodePort    10.106.249.13    <none>        3999:31213/TCP   91s

```

# Namespce 

## default namesapce
```
[ec2-user@ip-172-31-74-156 ~]$ kubectl  get ns 
NAME              STATUS   AGE
default           Active   41h
kube-node-lease   Active   41h
kube-public       Active   41h
kube-system       Active   41h

```

## auto completion in linux client 
```
cat ~/.bashrc 
source  <(kubectl  completion bash)

====
source  ~/.bashrc

```

## auto completion in Mac
```
ashutoshhs-MacBook-Air:~ fire$ cat  /etc/profile 
# System-wide .profile for sh(1)

if [ -x /usr/libexec/path_helper ]; then
	eval `/usr/libexec/path_helper -s`
fi

if [ "${BASH-no}" != "no" ]; then
	[ -r /etc/bashrc ] && . /etc/bashrc
fi
source ~/.kube/kubectl_autocompletion

```
## note :  download kubectl_autocompletion file from internet 

## custom namespace 
```
  kubectl  create  namespace  ashu-space 
   kubectl  create deployment  ashu-dep2  --image=dockerashu/multiapp:ashuv1july282020  -n ashu-space 
  ===
  [ec2-user@ip-172-31-74-156 day5]$ kubectl  get  deployments.apps  -n ashu-space 
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
ashu-dep2   1/1     1            1           47s
[ec2-user@ip-172-31-74-156 day5]$ kubectl  get  deployments  -n ashu-space 
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
ashu-dep2   1/1     1            1           57s
[ec2-user@ip-172-31-74-156 day5]$ kubectl  get  deployment  -n ashu-space 
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
ashu-dep2   1/1     1            1           61s
[ec2-user@ip-172-31-74-156 day5]$ kubectl  get  deploy -n ashu-space 
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
ashu-dep2   1/1     1            1           67s

====

 kubectl expose deployment  ashu-dep2 --type  LoadBalancer  --port 1234 --target-port 80 --name ashus1 -n ashu-space
 
 
 ```
 ### view deployment 
 ```
 [ec2-user@ip-172-31-74-156 day5]$ kubectl  get  deploy,rs,pod,svc  -n ashu-space 
NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/ashu-dep2   1/1     1            1           6m25s

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/ashu-dep2-6f5b45647d   1         1         1       6m25s

NAME                             READY   STATUS    RESTARTS   AGE
pod/ashu-dep2-6f5b45647d-2pz9d   1/1     Running   0          6m25s

NAME             TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/ashus1   LoadBalancer   10.97.104.249   <pending>     1234:30641/TCP   3m31s
```

## updating image in deployment
```
[ec2-user@ip-172-31-74-156 multapp]$ kubectl  describe  deployments.apps ashu-dep2  -n ashu-space 
Name:                   ashu-dep2
Namespace:              ashu-space
CreationTimestamp:      Fri, 31 Jul 2020 05:03:24 +0000
Labels:                 app=ashu-dep2
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=ashu-dep2
Replicas:               5 desired | 5 updated | 5 total | 5 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=ashu-dep2
  Containers:
   multiapp:
    Image:        dockerashu/multiapp:ashuv1july282020
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Progressing    True    NewReplicaSetAvailable
  Available      True    MinimumReplicasAvailable
OldReplicaSets:  <none>
NewReplicaSet:   ashu-dep2-6f5b45647d (5/5 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  17m    deployment-controller  Scaled up replica set ashu-dep2-6f5b45647d to 1
  Normal  ScalingReplicaSet  8m29s  deployment-controller  Scaled up replica set ashu-dep2-6f5b45647d to 5
  
  ====
 kubectl  set  image deployment  ashu-dep2  multiapp=dockerashu/multiapp:ashuv1july282020v002 -n ashu-space 
 
  1091   kubectl  set  image deployment  ashu-dep2  multiapp=dockerashu/multiapp:ashuv1july282020v003 -n ashu-space 
 1092  history 
 1093  kubectl  describe  deployments.apps ashu-dep2  -n ashu-space 
 
 ```
 
 ## rollback now
 ```
 [ec2-user@ip-172-31-74-156 multapp]$ kubectl  rollout history deployment  ashu-dep2 -n ashu-space 
deployment.apps/ashu-dep2 
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
3         <none>


===

[ec2-user@ip-172-31-74-156 multapp]$ kubectl rollout undo deployment  ashu-dep2 -n ashu-space 
deployment.apps/ashu-dep2 rolled back
[ec2-user@ip-172-31-74-156 multapp]$ kubectl  rollout status deployment  ashu-dep2 -n ashu-space 
deployment "ashu-dep2" successfully rolled out
[ec2-user@ip-172-31-74-156 multapp]$ 


====

 1091   kubectl  set  image deployment  ashu-dep2  multiapp=dockerashu/multiapp:ashuv1july282020v003 -n ashu-space 
 1092  history 
 1093  kubectl  describe  deployments.apps ashu-dep2  -n ashu-space 
 1094  history 
 1095  kubectl  rollout history deployment  ashu-dep2 -n ashu-space 
 1096  kubectl  describe  deployments.apps ashu-dep2  -n ashu-space 
 1097  kubectl rollout undo deployment  ashu-dep2 -n ashu-space 
 1098  kubectl  rollout status deployment  ashu-dep2 -n ashu-space 
 1099  kubectl  describe  deployments.apps ashu-dep2  -n ashu-space 
 1100  kubectl  rollout history deployment  ashu-dep2 -n ashu-space 
 1101  kubectl rollout undo deployment  ashu-dep2   --to-revision=1   -n ashu-space 
 1102  kubectl  rollout status deployment  ashu-dep2 -n ashu-space 
 1103  kubectl  describe  deployments.apps ashu-dep2  -n ashu-space 

```

# Tips 

## changin namespace permanently 

```
 kubectl config set-context   $(kubectl  config  current-context)   --namespace=ashu-space
 
 ```
 
 
 # ENV in Kubernetes 
 
 ## env in k8s
 
 ```
 kubectl  run  ashupod-day5  --image=dockerashu/multiapp:ashuv1july282020 --port 80 --dry-run=client -o yaml
 
 ===
 [ec2-user@ip-172-31-74-156 day5]$ cat  envpod.yml 
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: ashupod-day5
  name: ashupod-day5
  namespace: ashu-space  #  name of namespace 
spec:
  containers:
  - image: dockerashu/multiapp:ashuv1july282020
    name: ashupod-day5
    ports:
    - containerPort: 80
    env: # defining  env variable
    - name: web   #  this variable is same as env var in Dockerfile
      value: app2  #  appliction 2 
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always

===
kubectl  expose  pod ashupod-day5  --type LoadBalancer  --port 1231 --target-port 80 -n ashu-space 

```

## api-resources
```
[ec2-user@ip-172-31-74-156 day5]$ kubectl  api-resources 
NAME                              SHORTNAMES   APIGROUP                       NAMESPACED   KIND
bindings                                                                      true         Binding
componentstatuses                 cs                                          false        ComponentStatus
configmaps                        cm                                          true         ConfigMap
endpoints                         ep                                          true         Endpoints
events                            ev                                          true         Event
limitranges                       limits                                      true         LimitRange
namespaces                        ns                                          false        Namespace
nodes                             no                                          false        Node
persistentvolumeclaims            pvc                                         true         PersistentVolumeClaim

```

# configuMAp
```
 1151  kubectl  create  configmap  ashucm1  --from-literal  x=app3   -n ashu-space  --dry-run=client -o yaml
 1152  kubectl  create  configmap  ashucm1  --from-literal  x=app3   -n ashu-space  --dry-run=client -o yaml >ashucombo.yml 
 1153  history 
 1154  kubectl  create  configmap  ashucm1  --from-literal  x=app3 --from-literal y=app2   -n ashu-space  --dry-run=client -o yaml >ashucombo.yml 

=== deployment
kubectl create deployment  ashu-dep6  --image=dockerashu/multiapp:ashuv1july282020 -n ashu-space  --dry-run=client  -o yaml  >>ashucombo.yml

kubectl  create service nodeport  ashusvc6 --tcp 1244:80 --dry-run=client -o yaml >>ashucombo.yml

```

### combo
```
[ec2-user@ip-172-31-74-156 day5]$ cat ashucombo.yml 
apiVersion: v1
data:
 x: app3
kind: ConfigMap
metadata:
  creationTimestamp: null
  name: ashucm1 # name of the configMap 
  namespace: ashu-space  # name  of  namespace 

---
apiVersion: apps/v1
kind: Deployment
metadata:  #
  creationTimestamp: null
  labels:
    app: ashu-dep6
  name: ashu-dep6 # name deployment 
  namespace: ashu-space   #  namespace  info 
spec:
  replicas: 1 # only one pod will be deployed 
  selector:
    matchLabels:
      app: ashu-dep6
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: ashu-dep6   # label of pods 
    spec:
      containers:
      - image: dockerashu/multiapp:ashuv1july282020 # image
        name: multiapp  # name of container 
        env: # defining  env  variables
        - name: web  #  this variable name sam as ENV var in Dockerfile 
          valueFrom: #  taking value from other palce
           configMapKeyRef: # pointing  to configMap 
            name: ashucm1  #  name of configMap 
            key: x  #  key of configMap 
        resources: {}
status: {}

---
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: ashusvc6
  name: ashusvc6  #  name of service 
  namespace: ashu-space #  namespace  info  
spec:
  ports:
  - name: 1244-80
    port: 1244
    protocol: TCP
    targetPort: 80
  selector:
   app: ashu-dep6    # matched  labels of pod 
  type: NodePort
status:
  loadBalancer: {}


---
kubectl  apply  -f ashucombo.yml 
[ec2-user@ip-172-31-74-156 day5]$ kubectl get cm,deploy,svc -n ashu-space 
NAME                DATA   AGE
configmap/ashucm1   1      4m54s

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/ashu-dep6   1/1     1            1           113s

NAME               TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
service/ashusvc6   NodePort   10.106.152.224   <none>        1244:31552/TCP   113s

```

# Db mysql deployment in k8s

```
kubectl  create   deployment   ashudb --image=mysql -n ashu-space  --dry-run=client -o yaml >ashu-db.yml
kubectl  create secret generic ashudbinfo --from-literal slqpass=Oracle011  -n ashu-space 

===
[ec2-user@ip-172-31-74-156 day5]$ vim  ashu-db.yml 
[ec2-user@ip-172-31-74-156 day5]$ cat  ashu-db.yml 
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: ashudb
  name: ashudb
  namespace: ashu-space
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ashudb
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: ashudb
    spec:
      containers:
      - image: mysql
        name: mysql
        env: 
        - name: MYSQL_ROOT_PASSWORD  #  env variable already present in Dockerfile of mysql image
          valueFrom:
           secretKeyRef:
            name: ashudbinfo #  name of secret
            key: slqpass  #  key of secret 
        resources: {}
status: {}

===
[ec2-user@ip-172-31-74-156 day5]$ kubectl  get  deployments.apps  -n ashu-space 
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
ashu-dep6   1/1     1            1           105m
ashudb      1/1     1            1           3m58s
[ec2-user@ip-172-31-74-156 day5]$ kubectl  get  pods  -n ashu-space 
NAME                         READY   STATUS    RESTARTS   AGE
ashu-dep6-58c4b569d9-6hpmn   1/1     Running   0          105m
ashudb-66f7b76bb-wsk5v       1/1     Running   0          4m6s
[ec2-user@ip-172-31-74-156 day5]$ kubectl  logs  ashudb-66f7b76bb-wsk5v   -n ashu-space 



===

kubectl  expose   deployment  ashudb --type NodePort --port 1230 --target-port 3306 --name ashudbsvc -n ashu-space 

[ec2-user@ip-172-31-74-156 day5]$ kubectl  exec  -it ashudb-66f7b76bb-wsk5v bash -n ashu-space 
kubectl exec [POD] [COMMAND] is DEPRECATED and will be removed in a future version. Use kubectl kubectl exec [POD] -- [COMMAND] instead.
root@ashudb-66f7b76bb-wsk5v:/# 
root@ashudb-66f7b76bb-wsk5v:/# 
root@ashudb-66f7b76bb-wsk5v:/# 
root@ashudb-66f7b76bb-wsk5v:/# cat /etc/os-release 
PRETTY_NAME="Debian GNU/Linux 10 (buster)"
NAME="Debian GNU/Linux"
VERSION_ID="10"
VERSION="10 (buster)"
VERSION_CODENAME=buster
ID=debian
HOME_URL="https://www.debian.org/"
SUPPORT_URL="https://www.debian.org/support"
BUG_REPORT_URL="https://bugs.debian.org/"
root@ashudb-66f7b76bb-wsk5v:/# mysql  -u  root  -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.0.21 MySQL Community Server - GPL

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.


```

# Dashboard Deployment 
## service account 

```
[ec2-user@ip-172-31-74-156 day5]$ kubectl   get  serviceaccounts   -n ashu-space 
NAME      SECRETS   AGE
default   1         5h1m

```
## service account token
```
[ec2-user@ip-172-31-74-156 day5]$ kubectl  get secrets  -n ashu-space 
NAME                  TYPE                                  DATA   AGE
ashudbinfo            Opaque                                1      53m
default-token-5nbp8   kubernetes.io/service-account-token   3      5h3m
[ec2-user@ip-172-31-74-156 day5]$ kubectl  describe  secrets default-token-5nbp8  -n ashu-space 
Name:         default-token-5nbp8
Namespace:    ashu-space
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: default
              kubernetes.io/service-account.uid: ba19aa72-e2c2-4276-a8af-b93d79e18ed0

Type:  kubernetes.io/service-account-token

Data
====
ca.crt:     1025 bytes
namespace:  10 bytes
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6IkppbHluRElpU1VtZDc5QkRkSmRJLUdkaF9Cd2ZWMm9MaTMta3hNdFRsNGcifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJhc2h1LXNwYWNlIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6ImRlZmF1bHQtdG9rZW4tNW5icDgiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiZGVmYXVsdCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6ImJhMTlhYTcyLWUyYzItNDI3Ni1hOGFmLWI5M2Q3OWUxOGVkMCIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDphc2h1LXNwYWNlOmRlZmF1bHQifQ.ZnD61jxoTuBlSZW0J48sjbC7BxA-4MO1YMa2KpFhl0ayoqsezujX7IwVHrNGQU4fThnxzEn9xn3e0bsFvOW9uRhxE2HE2P_w-tUfQOGymWO2SNNyvZ2aDgSTFZgSqvv6By0XRK1V5Pee10gUchw2cnUWUpGd60CY28WE5PJjybvLkmB6naDbGKAuzsOOjVI1LJXX5bliywtdFjY9-abklkjFtCwZPwYeud9nUHaBF1RpyUaJqahShFKYLKy8WsTh7yUbmi155IH-7c9bjzJcV2W_erVhjuH79p8v0-Q-6aS8vrHSmBETTOr8uTTCEFqywQ_HEz39_p5Zra4dO5HQbw

```

## installing dashboard
```
[ec2-user@ip-172-31-74-156 day5]$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0/aio/deploy/recommended.yaml
namespace/kubernetes-dashboard created
serviceaccount/kubernetes-dashboard created
service/kubernetes-dashboard created
secret/kubernetes-dashboard-certs created
secret/kubernetes-dashboard-csrf created
secret/kubernetes-dashboard-key-holder created
configmap/kubernetes-dashboard-settings created
role.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrole.rbac.authorization.k8s.io/kubernetes-dashboard created
rolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
deployment.apps/kubernetes-dashboard created
service/dashboard-metrics-scraper created
deployment.apps/dashboard-metrics-scraper created

========
[ec2-user@ip-172-31-74-156 day5]$ kubectl  get   ns
NAME                   STATUS   AGE
arun-ns                Active   5h8m
ashu-space             Active   5h10m
bpn-ns                 Active   5h15m
default                Active   46h
geeta-k8s-space        Active   5h9m
janani                 Active   5h14m
kube-node-lease        Active   46h
kube-public            Active   46h
kube-system            Active   46h
kubernetes-dashboard   Active   92s
pankaj-ns              Active   5h10m

====
[ec2-user@ip-172-31-74-156 day5]$ kubectl  get  deploy  -n  kubernetes-dashboard 
NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
dashboard-metrics-scraper   1/1     1            1           2m1s
kubernetes-dashboard        1/1     1            1           2m1s
[ec2-user@ip-172-31-74-156 day5]$ kubectl  get  po  -n  kubernetes-dashboard 
NAME                                         READY   STATUS    RESTARTS   AGE
dashboard-metrics-scraper-6b4884c9d5-zsn9z   1/1     Running   0          2m16s
kubernetes-dashboard-7b544877d5-8z79b        1/1     Running   0          2m16s
[ec2-user@ip-172-31-74-156 day5]$ 


======


[ec2-user@ip-172-31-74-156 day5]$ kubectl  get  svc  -n  kubernetes-dashboard 
NAME                        TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
dashboard-metrics-scraper   ClusterIP   10.98.206.90     <none>        8000/TCP   2m41s
kubernetes-dashboard        ClusterIP   10.105.102.197   <none>        443/TCP    2m41s


====
[ec2-user@ip-172-31-74-156 day5]$ kubectl  get  sa  -n kubernetes-dashboard 
NAME                   SECRETS   AGE
default                1         3m
kubernetes-dashboard   1         3m
[ec2-user@ip-172-31-74-156 day5]$ 
[ec2-user@ip-172-31-74-156 day5]$ 
[ec2-user@ip-172-31-74-156 day5]$ kubectl  get secrets  -n kubernetes-dashboard 
NAME                               TYPE                                  DATA   AGE
default-token-gfw92                kubernetes.io/service-account-token   3      3m16s
kubernetes-dashboard-certs         Opaque                                0      3m16s
kubernetes-dashboard-csrf          Opaque                                1      3m16s
kubernetes-dashboard-key-holder    Opaque                                2      3m16s
kubernetes-dashboard-token-6tg8g   kubernetes.io/service-account-token   3      3m16s

====

[ec2-user@ip-172-31-74-156 day5]$ kubectl  edit   svc kubernetes-dashboard   -n  kubernetes-dashboard 
service/kubernetes-dashboard edited
[ec2-user@ip-172-31-74-156 day5]$ kubectl  get  svc  -n  kubernetes-dashboard 
NAME                        TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)         AGE
dashboard-metrics-scraper   ClusterIP   10.98.206.90     <none>        8000/TCP        4m36s
kubernetes-dashboard        NodePort    10.105.102.197   <none>        443:32191/TCP   4m36s


=====

[ec2-user@ip-172-31-74-156 day5]$ kubectl get secrets  -n kubernetes-dashboard 
NAME                               TYPE                                  DATA   AGE
default-token-gfw92                kubernetes.io/service-account-token   3      9m17s
kubernetes-dashboard-certs         Opaque                                0      9m17s
kubernetes-dashboard-csrf          Opaque                                1      9m17s
kubernetes-dashboard-key-holder    Opaque                                2      9m17s
kubernetes-dashboard-token-6tg8g   kubernetes.io/service-account-token   3      9m17s
[ec2-user@ip-172-31-74-156 day5]$ kubectl  describe  secrets  kubernetes-dashboard-token-6tg8g  -n kubernetes-dashboard 
Name:         kubernetes-dashboard-token-6tg8g
Namespace:    kubernetes-dashboard
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: kubernetes-dashboard
              kubernetes.io/service-account.uid: 63cb12ad-6103-4ff0-97af-feac137161dd

Type:  kubernetes.io/service-account-token

Data
====
ca.crt:     1025 bytes
namespace:  20 bytes
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6IkppbHluRElpU1VtZDc5QkRkSmRJLUdkaF9Cd2ZWMm9MaTMta3hNdFRsNGcifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJrdWJlcm5ldGVzLWRhc2hib2FyZC10b2tlbi02dGc4ZyIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6IjYzY2IxMmFkLTYxMDMtNGZmMC05N2FmLWZlYWMxMzcxNjFkZCIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlcm5ldGVzLWRhc2hib2FyZDprdWJlcm5ldGVzLWRhc2hib2FyZCJ9.UZNaiM9pl0adUfizn5HIBKtal3KHFX07Tdvl0EKMuuW-Cll0RPjVF6XlGRQX1FF4Lq8MAW1c8BuQgKXID21fFICYY_fZ7Njf3mZ1WUAjcbraS7HXfCROlnrEWszwqCY1AyV_HB3oQlelZgeOQ8Dvh0b5IJXMru-lDbTXoZHCyoUv2I7Mrkyb-3he6BTwiD5ZfPX4Hbkg8msdFdeIQkaV6F1oy3Qvih37Erb0Ey3mFlkMtDRvQrBAw_w7eFevPZvGYp-FTkMWGswzKqQG2GOCrHtFPq16FNyZ5ruFnFmxq7xZ08CPVYU5QpjzkJw_zJ-uyVjaVkPHTKtNz8Ll_oq27w


```

## clusterrole bind to kubernetes-dashboard svc account
```
[ec2-user@ip-172-31-74-156 day5]$ cat  clusterrolebind.yml 
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
  name: kubernetes-dashboard
  namespace: kubernetes-dashboard
  
  ====
  [ec2-user@ip-172-31-74-156 day5]$ kubectl apply  -f  clusterrolebind.yml 
clusterrolebinding.rbac.authorization.k8s.io/admin-user created


```
# volumes in k8s

## emptyDir

```
kubectl  run   ashupodemp  --image=alpine  --dry-run=client -o yaml     -n ashu-space  >lastpod.yml 

====
[ec2-user@ip-172-31-74-156 day5]$ cat  lastpod.yml 
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: ashupodemp
  name: ashupodemp
  namespace: ashu-space 
spec:
  containers:
  - image: alpine
    name: ashupodemp
    command: ["/bin/sh","-c","ping fb.com"]  #  here command is the replacement of Entrypoint in docker
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
[ec2-user@ip-172-31-74-156 day5]$ 

====

[ec2-user@ip-172-31-74-156 day5]$ cat  lastpod.yml 
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: ashupodemp
  name: ashupodemp
  namespace: ashu-space 
spec:
  volumes:  #  for creating  a volume 
  - name: ashuvol1  #  name of volume 
    emptyDir: {} # it will create a random folder with in the worker node where pod got scheduled 
  containers:
  - image: alpine
    name: ashupodemp
    command: ["/bin/sh","-c","while true;do date  >>/mnt/oracle/time.txt;sleep 10;done"]  # e replacement of Entrypoint in docker
    volumeMounts: # for mounting the volume into the container 
    - name: ashuvol1  #  using volume that got created above
      mountPath: /mnt/oracle   #  in the container it will be mounted 
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}

=======
[ec2-user@ip-172-31-74-156 day5]$ kubectl  exec  -it ashupodemp  sh  -n ashu-space  
kubectl exec [POD] [COMMAND] is DEPRECATED and will be removed in a future version. Use kubectl kubectl exec [POD] -- [COMMAND] instead.
/ # 
/ # 
/ # cd  /mnt/oracle/
/mnt/oracle # ls
time.txt
/mnt/oracle # cat time.txt 
Fri Jul 31 11:14:01 UTC 2020
Fri Jul 31 11:14:11 UTC 2020
Fri Jul 31 11:14:21 UTC 2020
Fri Jul 31 11:14:31 UTC 2020
Fri Jul 31 11:14:41 UTC 2020
Fri Jul 31 11:14:51 UTC 2020
Fri Jul 31 11:15:01 UTC 2020
Fri Jul 31 11:15:11 UTC 2020
Fri Jul 31 11:15:21 UTC 2020
Fri Jul 31 11:15:31 UTC 2020


```

## hostpath volumes
```
[ec2-user@ip-172-31-74-156 day5]$ cat hostpath1.yml 
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: webpod
  name: webpod
  namespace: ashu-space
spec:
  nodeName: k8s-minion3  #  this manual scheduling  
  volumes:
  - name: ashuvol22  # vol1
    hostPath:
     path: /etc/passwd  # file from minion node 3
     type: File 
  - name: ashuvol33   #  vol 2
    hostPath:
     path: /usr   # directory from Minion 3 
     type: Directory
  containers:
  - image: nginx
    name: webpod
    ports:
    - containerPort: 80
    volueMounts:
    - name: ashuvol22 #  above created volume 
      mountPath: /usr/share/nginx/html/index.html  #  mount file
    - name: ashuvol33  #  volue name 
      mountPath:  /mnt/oracledata # mountpath 
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
  
  ====
  
  [ec2-user@ip-172-31-74-156 day5]$ kubectl  exec -it  webpod  bash  -n ashu-space 
kubectl exec [POD] [COMMAND] is DEPRECATED and will be removed in a future version. Use kubectl kubectl exec [POD] -- [COMMAND] instead.
root@webpod:/# 
root@webpod:/# 
root@webpod:/# cd  /usr/share/nginx/html/
root@webpod:/usr/share/nginx/html# ls
50x.html  index.html
root@webpod:/usr/share/nginx/html# cd  /mnt/oracledata/
root@webpod:/mnt/oracledata# ls
bin  etc  games  include  lib  lib64  libexec  local  sbin  share  src	tmp


```

# image private registry 
```
 kubectl  create secret  docker-registry  ashuimgrg1  --docker-server=hub.docker.com  --docker-username=dockerashu  --docker-password=234234234sfdsf   -n ashu-space
 
 ===
 [ec2-user@ip-172-31-74-156 day5]$ cat lastpod.yml 
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: ashupodemp
  name: ashupodemp
  namespace: ashu-space 
spec:
  imagePullSecret:
  - name: ashuimgrg1  # name of  secret 
  volumes:  #  for creating  a volume 
  - name: ashuvol1  #  name of volume 
    emptyDir: {} # it will create a random folder with in the worker node where pod got scheduled 
  containers:
  - image: alpine
    name: ashupodemp
    command: ["/bin/sh","-c","while true;do date  >>/mnt/oracle/time.txt;sleep 10;done"]  # e replacement of Entrypoint in docker
    volumeMounts: # for mounting the volume into the container 
    - name: ashuvol1  #  using volume that got created above
      mountPath: /mnt/oracle   #  in the container it will be mounted 
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always


====
# Done 




