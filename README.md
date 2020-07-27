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
  



