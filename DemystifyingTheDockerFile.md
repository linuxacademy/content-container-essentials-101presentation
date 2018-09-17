# The Dockerfile

Any line that contains an instruction will create an intermediate layer during the build process. This intermediate layer/container includes the execution of whatever instruction has been passed from the Dockerfile.

For this example, we are going to be building a Dockerfile to create a customized webserver. 

## Creating a Docker file
Using your favorite text editor create a file called ' Dockerfile':


* vim Dockerfile
```
From centos:6

Label Maintainer="ell.marquez@linuxacademy.com" 

RUN yum update -y && yum install httpd net-tools -y && \
mkdir -p /run/httpd && \
rm -rf /run/httpd/* /tmp/httpd* 

COPY index.html /var/www/html

CMD echo " Remember to check your container IP address."

ENV ENVIROMENT="production"

VOLUME /mymount

Expose 80

ENTRYPOINT ls -al / |wc -l"
```

`From centos:6 `
`Label Maintainer="ell.marquez@linuxacademy.com" ` 

 A label is a key-value pair that creates metadata for your image.

`RUN yum update -y && yum install httpd net-tools -y && mkdir -p /run/httpd && rm -rf /run/httpd/* /tmp/httpd*`

`RUN` executes a command in a brand new layer during the build process creating the intermediate container. This container is a process which runs on the host and has its own filesystem, its own networking and its own isolated process tree separate from the host. 

`COPY index.html /var/www/html`

`COPY` and `ADD` can both take files from the local path and put them into the newly created image. The difference being copy only works with files where add command supports URLs and supports local-only tar extraction.

` CMD echo " Remember to check your container IP address." `

Only one `CMD` can exist in a Dockerfile; if you have multiple CMDS, just the last one will take effect. CMD will provide defaults for executing a container. These defaults can include executables, or they can be used to specify an `entry point` instruction. 

Note: (From the Docker Docs)  If `CMD` is used to provide default arguments for the ENTRYPOINT instruction, both the CMD and ENTRYPOINT instructions should be specified with the JSON array format.

`ENV ENVIROMENT="production"` 

Setting container envrioment variables.

`VOLUME /mymount`

The `VOLUME` option creates a mount point with the specified name and marks it as holding externally mounted volumes.

`Expose 80`

This does not map the port however it serves as documentation to users to know what ports the service is intended to be run on. 

`ENTRYPOINT ls -al / |wc -l `

Configures this container to run as an executable providing a count of the number of files in the root directory. 

## Dockerfile build
1) Create an index.html file for the image to copy. 

```
[user@ellmarquez1 ~]$ echo "Hello World" > index.html
```
2) Build our new image from our Dockerfile.

```
[user@ellmarquez1 ~]$ docker build -t mybuild:v1 .
Sending build context to Docker daemon  349.4MB
Step 1/9 : From centos:6
 ---> b5e5ffb5cdea
Step 2/9 : Label Maintainer="ell.marquez@linuxacademy.com"
 ---> Using cache
 ---> 85ef096ca62b
Step 3/9 : RUN yum update -y && yum install httpd net-tools -y && mkdir -p /run/httpd && rm -rf /run/httpd/* /tmp/httpd*
 ---> Using cache
 ---> bad4dfa47765
Step 4/9 : COPY index.html /var/www/html
 ---> 0b4c9f794a51
Step 5/9 : CMD echo " Remember to check your container IP address."
 ---> Running in 6fd258e216be
Removing intermediate container 6fd258e216be
 ---> b6d5006bc645
Step 6/9 : ENV ENVIROMENT="production"
 ---> Running in 0c035c8c5793
Removing intermediate container 0c035c8c5793
 ---> fcb97d01d558
Step 7/9 : VOLUME /mymount
 ---> Running in 503970dcdde1
Removing intermediate container 503970dcdde1
 ---> c479f0664d8d
Step 8/9 : Expose 80
 ---> Running in e99fa8f43d16
Removing intermediate container e99fa8f43d16
 ---> e6fe31ca0770
Step 9/9 : ENTRYPOINT ls -al / |wc -l
 ---> Running in cf5496cdf1bf
Removing intermediate container cf5496cdf1bf
 ---> c85c327605d6
Successfully built c85c327605d6
Successfully tagged mybuild:v1
```
3) Confirm our new image exists. 

```
[user@ellmarquez1 ~]$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
mybuild             v1                  c85c327605d6        59 seconds ago      292MB
```

4) Create a container from our new image.

```
[user@ellmarquez1 ~]$ docker run -t mybuild:v1 -name mycontainer
26
```

