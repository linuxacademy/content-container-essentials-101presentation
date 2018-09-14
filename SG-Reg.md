# Docker Registry

## Prepare for a Docker Secure Registry

A Registry is a storage and content delivery system holding Docker Images.

1) Create two directories for our certificate and key to reside in:

`mkdir certs auth`

2)First we will generate a self signed certificate and key. 

   `openssl req --newkey rsa:4096 -nodes -sha256 -keyout certs/dockerrepo.key -x509 -days 365 -out certs/dockerrepo.crt -subj /CN=myregistrydomain.com`

```output
[user@ellmarquez2 ~]$ openssl req -newkey rsa:4096 -nodes -sha256 -keyout certs/dockerrepo.key -x509 -days 365 -out certs/dockerrepo.crt -subj /CN=myregistrydomain.com
Generating a 4096 bit RSA private key
............................................................................................................................................++
.........................................................................................................................................................................................................................................++
writing new private key to 'certs/dockerrepo.key'
-----
```

2)For testing purposes, we can add a local host entry for myregistrydomain.com that uses our internal IP.

`vim /etc/hosts`

```/etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

172.31.115.80 myregistrydomain.com
```

3)Create directory for your certificates.

`sudo mkdir -p /etc/docker/certs.d/myregistrydomain.com:5000`

*Note:* 5000 is the default however this can be set to fit your enviroment.

4)Copy certificates needed ensuring they are owned by root.

`sudo cp /home/users/certs/dockerrepot.crt 
/etc/docker/certs.d/myregistrydomain.com:5000/ca.crt`

`sudo chown root:root /etc/docker/certs.d/myregistrydomain.com:5000/ca.crt`

5)Pull registry image.

`docker pull registry:2`

6)Run the container:

`docker run --entrypoint htpasswd registry:2 -Bbn test password > /home/user/htpasswd`

```
*Note:* Flags are being used for htpasswd
-B Use bcrypt encryption for passwords. This is currently considered to be very secure.

-b
Use batch mode; i.e., get the password from the command line rather than prompting for it

-n
Display the results on standard output rather than updating a file
```

## Deploy, Configure, Log Into, Push, and Pull an Image in a Registry

1) Run a container in detached mode mapping port 5000 to host port 5000 and to configure environment variables:

` docker run -d -p 5000:5000 -v /home/user/certs:/certs -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/dockerrepo.crt -e REGISTRY_HTTP_TLS_KEY=/home/user/certs/dockerrepo.key -v /home/user/auth/:/auth -e REGISTRY_AUTH=htpasswd -e REGISTRY_AUTH_HTPASSWD_REALM=“Registry Realm” -e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd registry:2 `