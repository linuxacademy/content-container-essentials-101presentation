# Week 1 Storage and Logging Drivers:

## Selecting a Storage Driver 
- Docker volumes will be used to write data to as containers should be abstract and portable.
- Docker uses a pluggable architecture that  supports multiple storage drivers that controls how images and containers are stored and managed on your host. 
- Device mapper can be used on disk as block storage and uses loopback adaptor.
  - It can be used with a block storage device and allow Docker to manage it for us. 
    
1. To check your Docker Storage driver is: 
      ` docker info |grep Storage` 
    - Overlay is the default 
    - Overlay2 is recommended and will be enabled by default if your kernel supports it (Kernel version 4+, or 3.10.0-514+ if using RHEL or Centos). 
    - Using Overlay2 on kernel 3.10.0-514+ requires an override -- see the [Docker Docs](https://docs.docker.com/storage/storagedriver/overlayfs-driver/#configure-docker-with-the-overlay-or-overlay2-storage-driver) for details.


2. Inside of your /etc/docker file there is a key.json file - this contains TLS keys for connecting to registries or other Docker services
  1.  We need to create a dameon.json file that will contain our configuration information for the Docker daemon to pull its information from.  (We are using “devicemapper” for our example.) 
  
   ` sudo vim /etc/docker/daemon.json`
  
     { “storage-driver”: “devicemapper” }


3. Restart Docker.
   - Note you will lose the images on your system. If they are needed you will need to back them up before restarting Docker. 
     ` sudo systemctl restart docker`
   - confirm Docker status as any typos will cause the service not to start up again.
     ``` [user@ellmarquez1 ~]$ sudo systemctl restart docker
      [user@ellmarquez1 ~]$ sudo systemctl status docker
      ● docker.service - Docker Application Container Engine
         Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled; vendor preset: disabled)
         Active: active (running) since Tue 2018-08-14 16:42:49 UTC; 8s ago
           Docs: https://docs.docker.com
       Main PID: 2029 (dockerd)
          Tasks: 17
         Memory: 46.1M
         CGroup: /system.slice/docker.service
                 ├─2029 /usr/bin/dockerd
                 └─2035 docker-containerd --config /var/run/docker/containerd/containerd.toml
4. Confirm changes: 
     `docker info | grep Storage`

        [user@ellmarquez1 ~]$ docker info| grep Storage
        WARNING: devicemapper: usage of loopback devices is strongly discouraged for production use.
          Use `--storage-opt dm.thinpooldev` to specify a custom block storage device.
        Storage Driver: devicemapper


## Configuring Logging drivers (syslog, JSON-File, etc ) 
- View currently supported logging drivers here: https://docs.docker.com/config/containers/logging/configure/
  - By default docker uses a JSON file for logging in order to allow docker container logs command to be viewed. 
  - In order to make a chance you need to be a privileged user as 


1. Pull httpd image from the docker hub:

    `docker image pull httpd `

 2. Start the container :
 
     ` docker container run -d --name testweb httpd`

3. Obtain containers IP address: 

    ` docker container inspect testweb |grep IPAddr`
   

Sample output: (your IP may be different)

    [user@ellmarquez1 ~]$ docker container inspect testweb |grep IPAddr
                "SecondaryIPAddresses": null,
                "IPAddress": "172.17.0.2",
                        "IPAddress": "172.17.0.2",
5. Install telnet and/or elinks to be able to connect to your container webhost.
    `sudo yum install -y telnet elinks`
6. Confirm that you can connect
     curl http://<ip> 
- Note:  you should see “It works!” 
     
        [user@ellmarquez1 ~]$ curl http://172.17.0.2
        <html><body><h1>It works!</h1></body></html>

7. To see the logs for this container you would do:
     `docker logs testweb `
- remember the name of our container was testweb 
   ``` [user@ellmarquez1 ~]$ docker logs testweb
    AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.2. S
    et the 'ServerName' directive globally to suppress this message
    AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.2. S
    et the 'ServerName' directive globally to suppress this message
    [Tue Aug 14 16:47:44.495110 2018] [mpm_event:notice] [pid 1:tid 140573965682560] AH00489: Apache/2.4.34 (U
    nix) configured -- resuming normal operations
    [Tue Aug 14 16:47:44.501366 2018] [core:notice] [pid 1:tid 140573965682560] AH00094: Command line: 'httpd 
    -D FOREGROUND'
    172.17.0.1 - - [14/Aug/2018:16:51:56 +0000] "GET / HTTP/1.1" 200 45
    172.17.0.1 - - [14/Aug/2018:16:53:31 +0000] "GET / HTTP/1.1" 200 45
    172.17.0.1 - - [14/Aug/2018:16:55:35 +0000] "GET / HTTP/1.1" 200 45

- *Note:* The errors you are seeing concerning server name are due to the fact that we haven not configured a vhost for this environment. 

**Reset our environment by stoping and removing the container.** 

     docker stop testweb  && docker rm test web 



## rsyslog

Rsyslog should already be installed on your cloud server, however if you are using an enviroment outside of Linux Academy you can install it by:

` $ sudo yum install rsyslog `
   
 OR 

`$ sudo apt-get install rsyslog`

- Change configuration to allow for UDP syslog reception:

    `vim /etc/rsyslog.conf`
  
 - Find the section:
    ```
    # Provides UDP syslog reception
    #$ModLoad imudp
    #$UDPServerRun 514```
  - and uncomment out the ModLoad and UDPServerRun sections


    ```
    # Provides UDP syslog reception
     $ModLoad imudp
     $UDPServerRun 514
- Start the rsyslog service and confirm it is running:

   ` sudo systemctl start rsyslog && systemctl status rsyslog`
- Configure for our syslog driver:

    `sudo vim /etc/docker/daemon.json `
 - *Note*: you need to use your private IP which may differ from mine: 
 ```
    { "storage-driver": "devicemapper" }
    { 
         "log-driver" : "syslog",
         "log-opts": { 
               "syslog-address": "udp://172.31.21.64:514" 
          }
    } 
```
- Restart docker 

   ` sudo systemctl restart docker `
- Confirm change

     `docker info |grep Logging `
  - You should now see Logging Driver: syslog
    ```
    [user@ellmarquez1 ~]$ docker info |grep Logging
    WARNING: devicemapper: usage of loopback devices is strongly discouraged for production use.
             Use `--storage-opt dm.thinpooldev` to specify a custom block storage device.
    Logging Driver: json-file

# **Testing changes:** 
In a new terminal, which I’ll refer to as terminal B,  connect to server so we can tail logs.

*Optional step:* Clear out current log files so we can have a better view of what we are doing: ( you will have to be root)  

    sudo su -


    echo “” > /var/log/messages


1. Restart rsyslog:

    `systemctl restart rsyslog `
3. Tail the logs:

`    tail -f /var/log/messages `

*Note: It may be helpful to configure your lay out so you can see both terminals at once. This will help you see the logs come in real time.*
 
**In original terminal :** 

1. Let’s run a new container: *(keep an eye on terminal B as hit enter)*

 `  docker container run -d -name testweb -p 80:80 httpd`


2. Try connecting to local host: 

` curl http://localhost `
     or 
     `elinks http://localhost `

**Reset our environment by stoping and removing the container.** 

     docker stop testweb  && docker rm test web 


**Changing options on a per container basis**   

- Create a new httpd container and specify the log driver you would like to use. *Below is an example of specifying json-file.*

`docker container run -d --name testjson --log-driver json-file httpd`


- Check the logs for testjson container
    `docker logs testjson`

```
    [user@ellmarquez1 ~]$ docker logs testjson
    AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.2. Set the 'ServerName' directive globally to suppress this message
    AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.2. Set the 'ServerName' directive globally to suppress this message
    [Tue Aug 14 18:54:09.079886 2018] [mpm_event:notice] [pid 1:tid 140656390690688] AH00489: Apache/2.4.34 (Unix) configured -- resuming normal operations
    [Tue Aug 14 18:54:09.086288 2018] [core:notice] [pid 1:tid 140656390690688] AH00094: Command line: 'httpd -D FOREGROUND'

