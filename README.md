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

