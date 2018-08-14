# Docker Install

This demo was created using CentOS v7 cloud server. 

If you are not running on a CentOS v7 box, ensure that your kernel is running version 3.10 or better by running the `uname -r` command:

``` 
[user@user ~]$ uname -r
3.10.0-862.9.1.el7.x86_64
``` 

## Install required packages. 
```
$ sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
```

## Setting up the Repository

Add the docker repository to your server to ensure that you have the latest version using the `sudo yum-config-manager \ --add-repo \` command. When you do so, the GPG key will be verified. As of publishing this guide, the fingerprint is *060A 61C5 1B55 8A7F 742B 77AA C52F EB6B 621E 9F35*:

```
[user@ellmarquez1 ~]$ sudo yum-config-manager \ --add-repo \ https://download.docker.com/linux/centos/docker-ce.repo

Retrieving key from https://download.docker.com/linux/centos/gpg
Importing GPG key 0x621E9F35:
 Userid     : "Docker Release (CE rpm) <docker@docker.com>"
 Fingerprint: 060a 61c5 1b55 8a7f 742b 77aa c52f eb6b 621e 9f35
 From       : https://download.docker.com/linux/centos/gpg 
```  
If the two do not match, then you are not pulling from the correct place. Check that the information was entered correctly. 

## Install Docker community edition.

Install docker with the `yum install` command:

```[user@ellmarquez1 ~]$ sudo yum install docker-ce```

Enable Docker with the`systemctl enable` command: 

```[user@ellmarquez1 ~]$ sudo systemctl enable docker``` 

Start Docker with the `systemctl start` command:

```[user@ellmarquez1 ~]$ sudo systemctl start docker``` 

Confirm that Docker installed correctly with the `docker run` command:

```[user@ello ~]$ sudo docker run hello-world``` 

### Optional

For best practices, do not use *root*. Instead, add your user to the Docker group. For this example, our user is named *user*: 
```
[user@ellmarquez1 ~]$ sudo usermod -a -G docker user
[sudo] password for user:
[user@ellmarquez1 ~]$
```

Confirm the change with the `grep` command:
```
[user@eellmarquez1 ~]$ grep docker /etc/group
docker:x:987:user
``` 

Once finished, log out and then log back in for changes to take effect.

