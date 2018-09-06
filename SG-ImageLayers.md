# Image Layers
These notes cover lectures: 
Describe and Display How Image Layers Work
Modify an Image to a Single Layer

## Vocabulary 
Union file system allows file and directories of separate file systems or branches to be overlayed so that they form a single filesystem.

## How Image layers work
1) Understanding `docker image history `

```
[user@ellmarquez1 ~]$ docker image history optimized:v1
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
635c9eac84b9        7 hours ago         /bin/sh -c yum update -y                        130MB
d342a6546b84        7 hours ago         /bin/sh -c #(nop)  LABEL maintainer=ell.marq…   0B
5182e96772bf        4 weeks ago         /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
<missing>           4 weeks ago         /bin/sh -c #(nop)  LABEL org.label-schema.sc…   0B
<missing>           4 weeks ago         /bin/sh -c #(nop) ADD file:6340c690b08865d7e…   200MB

```

 2) The image is not "missing" the history of this image ID is not part of the history of the build, as it was not carried forward when this image was installed from a remote repository. 

3) Further information can be found by using the --no-trunc command

```
[user@ellmarquez1 ~]$ docker image history optimized:v1 --no-trunc
IMAGE                                                                     CREATED             CREATED BY                                                                                                                                                                                                SIZE                COMMENT
sha256:635c9eac84b9fee1b68700c329a260358992881a59eeb3f34f7ffff84a74c855   7 hours ago         /bin/sh -c yum update -y                                                                                                                                                                                  130MB
sha256:d342a6546b8482c8a93239b658164c0e6a35e9aba57159c4bfda1eb1153cf898   7 hours ago         /bin/sh -c #(nop)  LABEL maintainer=ell.marquez@linuxacademy.com                                                                                                                                          0B
sha256:5182e96772bf11f4b912658e265dfe0db8bd314475443b6434ea708784192892   4 weeks ago         /bin/sh -c #(nop)  CMD ["/bin/bash"]                                                                                                                                                                      0B
<missing>                                                                 4 weeks ago         /bin/sh -c #(nop)  LABEL org.label-schema.schema-version=1.0 org.label-schema.name=CentOS Base Image org.label-schema.vendor=CentOS org.label-schema.license=GPLv2 org.label-schema.build-date=20180804   0B
<missing>                                                                 4 weeks ago         /bin/sh -c #(nop) ADD file:6340c690b08865d7eb84a36050a0ab0e8effc2b010a4ccb20b810153a97a9228 in /                                                                                                          200MB
[user@ellmarquez1 ~]$
```

* We can see that the base image used for this image was CentOS, then /bin/bash was set as default, the label maintainer=ell.marquez@linuxacademy.com was added, and the OS was updated. 


## Modify an image to a single layer.
 1) To begin with, let's list out the images on our system, making sure to note the size of the image. 
```
[user@ellmarquez1 ~]$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
optimized           v1                  635c9eac84b9        7 hours ago         329MB
```
2) Next, we will run a container using this image:
```
[user@ellmarquez1 ~]$ docker run optimized:v1
[user@ellmarquez1 ~]$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
40dc970fcd61        optimized:v1        "/bin/bash"         5 seconds ago       Exited (0) 4 seconds ago                       eloquent_bhabha
```

3) Now we will export this container's filesystem as a tar archive. 

```
[user@ellmarquez1 ~]$ docker export eloquent_bhabha > smallbuild.tar
```

4) Import the image as smallbuild:importv2:

```
[user@ellmarquez1 ~]$ docker import smallbuild.tar smallbuild:importv2
sha256:f1378a7521822dade824ffa1db6fc35621c19fa0e92d2996f42e02e25d418326
```

6) List out the image history and note that this image does not inherit optimized:v1's history and the size of the image has decreased.

```
[user@ellmarquez1 ~]$ docker image history smallbuild:importv2
IMAGE               CREATED              CREATED BY          SIZE                COMMENT
f1378a752182        About a minute ago                       283MB               Imported from -

[user@ellmarquez1 ~]$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
smallbuild          importv2            f1378a752182        5 seconds ago       283MB
optimized           v1                  635c9eac84b9        7 hours ago         329MB
```


* Docker images 

Study group questions:
docker image history 

What does cmd /bin/bash do. 
What is the first step in this image? 
CMD vs Entry Point  