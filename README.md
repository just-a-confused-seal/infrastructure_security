## Introduction

Note: this repository assume you used docker in KALI LINUX with intel architecture and also some familiarity with Linux. If you used M1/M2/M3 related chip, you have to find your way to adjust with the lab. But wait what's intel arch? M1? M2? M3? Kali Linux? if you cannot answer this basic question its better to choose more basic stuff. Don't be lazy! Internet have a lot of good reading that cover basic of cyber security that you can read for free, use your brain! if not you're getting replaced by AI in no time!

Reference: DOCKER DEEP DIVE ZERO TO DOCKER IN A SINGLE BOOK, By Nigel Poulton

### Why docker?

| Point| Detail	   					     		 |
|-----:|-----------------------------------------------------------------|
|     1| In the past one app equal to one server	     		 |
|     2| New business => New app => New server => Cost go up 		 |
|     3| Then come VMWare, to solve this problem	     		 |
|     4| But it introduce new problem - Its slow to boot and not portable|

Then container came along! it has the same approach as VM but it does not require a full-blown OS, thus, its portable and easy to setup.

### Installing Docker
To install docker, open terminal and use the following command:
```
~# sudo apt install docker.io

```
Once the installation complete, you can check if the docker is installed correctly:
```
~# docker version
Client:
 Version:           26.1.5+dfsg1
 API version:       1.45
 Go version:        go1.24.2
 Git commit:        a72d7cd
 Built:             Sat May 24 17:38:32 2025
 OS/Arch:           linux/amd64
 Context:           default

```
Please be aware that result might be different but thats not gonna be a problem for now.
 
### Get your Hands Dirty

Run the following command in the terminal:
```
~# sudo docker images           
REPOSITORY                 TAG       IMAGE ID       CREATED       SIZE
```
To used docker you need to used sudo! As you can see this will show docker images as the name implies. Images is basically contain all the necessary component including dependencies to run application.

Okay, now let's try to download our first image!
Run the following command in the terminal:
```
~# sudo docker pull ubuntu:plucky
plucky: Pulling from library/ubuntu
60fb2420030a: Pull complete 
Digest: sha256:95a416ad2446813278ec13b7efdeb551190c94e12028707dd7525632d3cec0d1
Status: Downloaded newer image for ubuntu:plucky
docker.io/library/ubuntu:plucky
```
Once download is done, you can check by entering this command again:
```
~# sudo docker images                           
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
ubuntu       plucky    92598a7a70c7   3 weeks ago   77MB
```
Let's create the container(Think about image is the raw material and container is the product of that raw material):
```
~# sudo docker container create --name vuln_server ubuntu:plucky
181068c012e9d47531cf7a8f45ae2bcc09d9c502826956c2d8aa623d1f82f469
```
Again the result might not be the same as yours and you can check if the container is created:
```
~# sudo docker container ls -all
CONTAINER ID   IMAGE           COMMAND       CREATED          STATUS    PORTS     NAMES
181068c012e9   ubuntu:plucky   "/bin/bash"   20 seconds ago   Created             vuln_server
```
Now that the container is ready, its time to run it:
```
~# sudo docker run -it ubuntu:plucky /bin/bash   
root@7b6c74e6fd4d:/# 
```
The container in a very minimalistic environment, we need to do some installation! run the following command in the docker:
```
root@7b6c74e6fd4d:/# apt update
root@7b6c74e6fd4d:/# apt install net-tools
```
