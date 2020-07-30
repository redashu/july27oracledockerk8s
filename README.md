
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
