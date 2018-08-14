# Docker Install: Ubuntu

This demo was created using an Ubuntu cloud server. 

If you are not running this install on Linux Academy, ensure that your kernel is running version 3.10 or higher by running the `uname -r` command:
```
[user@ellmarquez1 ~]$ uname -r
4.4.0-1063-aws
```
## Setting up the Repository

1. Ensure your *apt package index* is up to date.

   ```[user@ellmarquez1 ~]$ sudo apt-get update```

1. Install the following apt packages to allow apt to use a repository over HTTPS:

    ```[user@ellmarquez1 ~]$ sudo apt-get install \ apt-transport-https \ ca-certificates \ curl \ software-properties-common```

3. Add Docker’s official GPG key:

   ```[user@ellmarquez1 ~]$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -```

4. Verify that the GPG key fingerprint matches. As of publishing this guide, the fingerprint is *9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88*:
``` 
[user@ellmarquez1 ~]$ sudo apt-key fingerprint 0EBFCD88
pub   4096R/0EBFCD88 2017-02-22
      Key fingerprint = 9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
uid   Docker Release (CE deb) <docker@docker.com>
sub   4096R/F273FCD8 2017-02-22 
```
5. Add the repository using the `add-apt-repository` command:

    ```[user@ellmarquez1 ~]$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" ```

## Install Docker Community Edition

1. Update the *apt package index* using the `apt-get update` command:

   ```[user@ellmarquez1 ~]$ sudo apt-get update```

2. Install Docker Community Edition using the `apt-get install docker-ce` command:

   ```[user@ellmarquez1 ~]$ sudo apt-get install docker-ce```
 

3. Confirm Docker installed correctly using the `docker run` command:

   ```[user@ellmarquez1 ~]$ sudo docker run hello-world``` 

### Optional

For best practices, do not use *root*. Instead, add your user to the Docker group. For this example, our user's is named *user*: 
```
[user@ellmarquez1 ~]$ sudo usermod -a -G docker user
[sudo] password for user:
[user@ellmarquez1 ~]$
```

Confirm the change using the `grep` command:
```
[user@ellmarquez1 ~]$ grep docker /etc/group 
docker:x:999:user
```

Once finished, log out and then log back in for changes to take effect.
