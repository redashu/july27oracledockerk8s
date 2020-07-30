
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


