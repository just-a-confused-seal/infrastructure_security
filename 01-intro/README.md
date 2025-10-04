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
~# sudo apt install docker-compose
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
Let's create the container(Think about image is the raw material and container is the product of that raw material).
Now that the container is ready, its time to start it:
```
~# sudo docker run -it ubuntu:plucky /bin/bash   
root@7b6c74e6fd4d:/# 
```
The container in a very minimalistic environment, we need to do some installation! run the following command in the docker:
```
root@7b6c74e6fd4d:/# apt update
root@7b6c74e6fd4d:/# apt install net-tools
root@7b6c74e6fd4d:/# apt install nano
```
Once installation complete, we can use the standard linux network cli, like:
```
root@7b6c74e6fd4d:/# netstat -aptn
root@7b6c74e6fd4d:/# ifconfig
```
So on and so forth. Now lets try to install apache(web server) into our docker for testing, put the following command:
```
root@7b6c74e6fd4d:/# apt install apache2 
```
Once its done, lets start the service:
```
root@7b6c74e6fd4d:/# service apache2 start
```
We can check if the HTTP port open using the command:
```
root@3f55c4081b93:/# netstat -aptn
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      3573/apache2      
```
Let's visit the web server via browser from kali linux. As you can see it will run!
Note: you can only visit the ubuntu instance because you are the host, if you want other user to visit the same apache page. You have to run the docker using -p parameter so it mapped to host port.

Now that we have a very minimalistic ubuntu server to play with, lets do network scanning and enumeration using nmap from kali linux.
```
~# nmap 172.17.0.2             
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-07 11:03 EDT
Nmap scan report for 172.17.0.2 (172.17.0.2)
Host is up (0.0000070s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE
80/tcp open  http
MAC Address: 02:42:AC:11:00:02 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.38 seconds

~# nmap -A 172.17.0.2
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-07 11:03 EDT
Nmap scan report for 172.17.0.2 (172.17.0.2)
Host is up (0.00013s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.63 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.63 (Ubuntu)
MAC Address: 02:42:AC:11:00:02 (Unknown)
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop

TRACEROUTE
HOP RTT     ADDRESS
1   0.12 ms 172.17.0.2 (172.17.0.2)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.43 seconds


```
As you can see using nmap we can get information regarding the open port and the software running in the port. 
Let's right now switch side as the operation team, can we make this little harder for attacker? for example can we hide banner, so attacker don't know what software running in the http port.
Let's go to the apache2.conf file to put some hardening, like this:
```
root@3f55c4081b93:~# cd /etc/apache2
root@3f55c4081b93:/etc/apache2# ls
apache2.conf  conf-available  conf-enabled  envvars  magic  mods-available  mods-enabled  ports.conf  sites-available  sites-enabled
```
Open the file using nano, like this:
```
root@3f55c4081b93:/etc/apache2# nano apache2.conf
```
Go to the end of file using your arrow key and add the following configuration:
```
ServerTokens Prod
ServerSignature Off
```
Don't forget to save it with ctrl+o and ctrl+x to exit the nano. Finally restart apache server like this:
```
root@3f55c4081b93:/etc/apache2# service apache2 restart
```
Once it got up again , try to scan it using nmap:
```
~# nmap -A 172.17.0.2 -p 80
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-07 11:13 EDT
Nmap scan report for 172.17.0.2 (172.17.0.2)
Host is up (0.00015s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache
MAC Address: 02:42:AC:11:00:02 (Unknown)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop

TRACEROUTE
HOP RTT     ADDRESS
1   0.15 ms 172.17.0.2 (172.17.0.2)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.01 seconds

```
As you can see the server version is gone. Now let's move on into something more interesting. Lets setup a vulnerable FTP server, in this case we will be using vsftpd.
```
~# apt install vsftpd
```
If the installation asking you about country and location just pick any area you like!

Next is to create ftp user for you to access:
```
root@3f55c4081b93:/etc/apache2/conf-available# useradd -m ftpuser
root@3f55c4081b93:/etc/apache2/conf-available# passwd ftpuser
```
Use any password that you like. Next is to create the following folder:

```
root@3f55c4081b93:~# mkdir -p /var/ftp/share
```
Finally open the vsftpd.conf using nano:
```
root@3f55c4081b93:~# nano /etc/vsftpd.conf
```
Add the following changes:
```
anonymous_enable=YES
local_enable=YES
```
Finally you can start the vsftpd:
```
root@3f55c4081b93:~# service vsftpd start
 * Starting FTP server vsftpd                                                                                                          [ OK ] 
root@3f55c4081b93:~# netstat -aptn
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      4157/apache2        
tcp6       0      0 :::21                   :::*                    LISTEN      5091/vsftpd         
```

Now you can nmap the port and see what the nmap pick up:
```
~# nmap 172.17.0.2 -p 21 -A    
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-07 11:49 EDT
Nmap scan report for 172.17.0.2 (172.17.0.2)
Host is up (0.00015s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.5
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:172.17.0.1
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 3.0.5 - secure, fast, stable
|_End of status
MAC Address: 02:42:AC:11:00:02 (Unknown)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, OpenWrt 21.02 (Linux 5.4), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 1 hop
Service Info: OS: Unix

TRACEROUTE
HOP RTT     ADDRESS
1   0.15 ms 172.17.0.2 (172.17.0.2)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 4.46 seconds
```
### Notes
Several docker command that can be useful:

1. For example, if you want to restart your ubuntu docker that has been exited, like this:
```
~# sudo docker container ps -a  
CONTAINER ID   IMAGE                  COMMAND       CREATED             STATUS                          PORTS                                       NAMES
428fce953623   ubuntu:plucky          "/bin/bash"   About an hour ago   Exited (0) About a minute ago                                               eloquent_lamport

```
You can do the following command, first is to start the docker again by providing the container id as in this case it would be 428fce953623:
```
~# sudo docker container start 428fce953623
```
Once it starts you can attach to the docker again, using the following command:
```
~# sudo docker attach 428fce953623 
root@428fce953623:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```
2. After this you will doing a lot of setup in docker and it may cause your memory storage, thus, we recommend you to periodically prune the docker. This means to remove all of the cache, docker image and container.
```
~# sudo docker system prune
WARNING! This will remove:
  - all stopped containers
  - all networks not used by at least one container
  - all dangling images
  - unused build cache

Are you sure you want to continue? [y/N] y

```
This will remove all the mentioned items in your docker.

### Exercise - 1 (20 points)
Now do you know how to build a vulnerable FTP server, I want you to do hardening on the FTP server. Create a report on how to harden the configuration and make sure try to do research on serveral way to this task. Internet is your friend! 

