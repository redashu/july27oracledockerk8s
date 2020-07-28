# Docker installation on Linux Platform
## we are using amazon linux its like OL | RHEL | Fedora

### amazon linux

```
sudo  yum  install docker  -y
sudo systemctl  start  docker 
```

### check status of docker engine 

```
[ec2-user@ip-172-31-74-156 ~]$ systemctl  status  docker  
● docker.service - Docker Application Container Engine
   Loaded: loaded (/usr/lib/systemd/system/docker.service; disabled; vendor preset: disabled)
   Active: active (running) since Mon 2020-07-27 06:45:07 UTC; 46s ago
     Docs: https://docs.docker.com
  Process: 4817 ExecStartPre=/usr/libexec/docker/docker-setup-runtimes.sh (code=exited, status=0/SUCCESS)
  Process: 4814 ExecStartPre=/bin/mkdir -p /run/docker (code=exited, status=0/SUCCESS)
 Main PID: 4822 (dockerd)

```


## link for docker CE. installation 

-- https://docs.docker.com/engine/install/

##  docker Client configuration 

adding  user into docker group for access of docker engine 

```
usermod  -a  -G  docker  ec2-user
```

### adding all users
```
[root@ip-172-31-74-156 ~]# for  i  in  `ls /home`
> do
> usermod -a -G docker $i
> done
```

## Docker hub 

-- The image registry 

## Docker Client commands :
```
  21  docker  version 
   22  docker  search   java 
   23  docker  search   mysql 
 ```
### check docker images on docker engine 

```
[ec2-user@ip-172-31-74-156 ~]$ docker  images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE

```
### pulling docker images

```
  26  docker  pull  java 
   27  docker pull mysql
   29  docker pull oraclelinux
   30  docker pull oraclelinux:8.2
   32  docker  pull  alpine  
   34  docker  pull  busybox 

```
### container creation 
```
  51  docker  run   alpine   cal  
   55  docker  run  alpine  ping  fb.com 
   61  docker  run  -d  --name  ashuc1  alpine  ping  google.com 
```
### child process 
```
 84  docker  exec  ashuc1  cal  
   85  docker  exec  ashuc1  date
   86  docker  exec  ashuc1  ping fb.com 
   87  docker  exec  -d  ashuc1  ping fb.com 
  ```
 ### basic operations 
 
 ```
  61  docker  run  -d  --name  ashuc1  alpine  ping  google.com 
   62  docker  ps
   63  history 
   64  docker  ps
   65  docker logs  ashuc1  
   66  docker logs -f   ashuc1  
   67  history 
   68  docker  ps  -a
   69  docker  logs  xenodochial_nightingale
   70  history 
   71  docker  ps
   72  docker  stop   ashuc1  
   73  docker  ps
   74  history 
   75  docker  ps
   76  docker  ps   -a
   77  docker  start    ashuc1  
   78  docker  ps
   79  docker  kill ashuc1  
   80  docker  start  ashuc1  
   81  docker  ps
   82  docker   ps  
   83  docker  ps
   84  docker  exec  ashuc1  cal  
   85  docker  exec  ashuc1  date
   86  docker  exec  ashuc1  ping fb.com 
   87  docker  exec  -d  ashuc1  ping fb.com 
   88  history 
   89  docker  ps
   90  docker  kill   ashuc1  
   91  docker  ps  
   92  docker  rm   ashuc1  
   93  docker  ps -a

 ```

### kill and remove all the containers

```
 133  docker  kill  $(docker  ps  -q)
  134  docker  rm   $(docker  ps  -aq)

```
## Dockerfile for buidling. new. docker. images

==== Dockerfile for python code 
---
```
FROM  python
#  we are using  python as a base image to itegrate our python code 
#  and we will create a new python image
# if default python image  will not be there then it will be pulled automatically 
MAINTAINER  ashutoshh@linux.com
#  optional field but info about  image designer 
RUN  mkdir  /pycodes 
#  creating  a directory  where will copy the python code 
COPY  ashu.py   /pycodes/ashu.py
#    sources     Destination 
#  this one copy code from  Host OS  to docker  image
CMD  python  /pycodes/ashu.py 
#  cmd  use to define the default parent process 

```

### building docker. image
---
```
 154  docker  build  -t  python:ashuv1  . 
### OR
  157  docker  build  -t  python:ashuv1  /home/ec2-user/day1
  
  ```
  
  ### creating container 
  
  ```
    165  docker  run  -it  --name  ashuc3  python:ashuv1 
  166  docker  run  -itd  --name  ashuc4  python:ashuv1 
  167  docker  logs  ashuc4  
  ```
  
### dockerfile for java code
```
FROM  java
# using java as a  base  image
MAINTAINER   ashutoshh@linux.com  java team 
RUN  mkdir  /javacodes
COPY  ashu.java   /javacodes/ashu.java
WORKDIR   /javacodes
#  changing  location to javacodes 
#  workdir  is like cd commnad 
RUN  javac  ashu.java
#  here javac  will compile code 
#CMD  java  myclass
ENTRYPOINT  java  myclass
#  default parent process we are running java code 
#  it can't be replaced as last argument during  container creation like CMD 

```

### building and runnning. 
```
 233  docker build  -t  java:ashuv2  .
  234  docker run  -it --name d1  java:ashuv2  bash 

```

## taking image backup  and. restoring. it 

```
docker  save  -o  ashujavaimg.tar   java:ashuv2.   #. taking backup
docker load  -i  ashujavaimg.tar    # loading into docker engine 

```

## cgroups implement 

```
docker  run  -itd  --name  ashux1  --memory 100m  python:ashuv1
```

### limit cpu and ram both

```
 287  docker  run  -d  --name ashux2  --cpus=1  alpine  ping  fb.com 
  288  docker  run  -d  --name ashux3  --cpus=1 --memory=50m alpine  ping  fb.com 
  
  ```
  
 ### limit cpu core with percentage 
 ```
 docker  run -itd --name ashux5  --memory=120m --cpuset-cpus=0 --cpu-shares=30 python:ashuv2
 ```
 
## Docker CE configure on TCP socket 

```
[root@ip-172-31-74-156 sysconfig]# cat  /etc/sysconfig/docker
# The max number of open files for the daemon itself, and all
# running containers.  The default value of 1048576 mirrors the value
# used by the systemd service unit.
DAEMON_MAXFILES=1048576

# Additional startup options for the Docker daemon, for example:
# OPTIONS="--ip-forward=true --iptables=true"
# By default we limit the number of open files per container
OPTIONS="--default-ulimit nofile=1024:4096  -H  tcp://0.0.0.0:2375" # here we updated

# How many seconds the sysvinit script waits for the pidfile to appear
# when starting the daemon.
DAEMON_PIDFILE_TIMEOUT=10

```

### restarting. docker. deamon
```
 27  systemctl daemon-reload 
   28  systemctl restart  docker

```

### Docker client on Linux and MAC
```
  519  export  DOCKER_HOST="tcp://18.210.97.164:2375"
  520  docker version 
  521  docker  images
```

### Docker client on Windows powershell

```
 $env:DOCKER_HOST="tcp://tcp://18.210.97.164:2375"
 ```
 
## Dockerfile for Nginx based 
```
FROM  nginx
#  we are using  default nginx image from docker hub 
MAINTAINER  ashutoshh@linux.com 
WORKDIR  /usr/share/nginx/html
#  changing directory to documentroot of nginx web server 
COPY project-html-website  .
#   source from docker host   ,  destination if docker image 
#  copy can only take data from local system and from local path where dockerfile is present
EXPOSE  80
#  to tell docker engine about default port of nginx web server 
#CMD  |  ENTRYPOINT
#  if we don't use cmd or entrypoint then it will use the parent process of  FROM image 
```

### creating .dockerignore

```
[ec2-user@ip-172-31-74-156 nginxapp]$ cat  .dockerignore 
*.md
.git
LICENSE
.dockerignore
dockerfile
```

### building image and creating containers
```
 348  docker  build -t  nginx:ashuv1 .
  
 352  docker run  -d  --name  ashuc10  -p  1122:80    nginx:ashuv1 
 ```
 

