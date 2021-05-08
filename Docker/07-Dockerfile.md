# Dockerfile

## What is a Dockerfile?
It a text file that carry out all the instructions to build a custom image. Or is a text document that contains all the commands a user could call on the command line to assemble a custom image. 

## Dockerfile Basics
Before we construct our Dockerfile, you need to understand what makes up the file. This will be a text file, named `Dockerfile`, that includes specific keywords that dictate how to build a specific image. The specific keywords you can use in a file are:
- FROM
- ADD 
- CMD 
- ENTRYPOINT 
- ENV 
- EXPOSE 
- MAINTAINER
- RUN 
- USER 
- VOLUME 
- WORKDIR 
- LABEL

## Ways to Create a Custom Image
They are 2 ways to create a custom images
- From a running container (snapshort of a running container at a particular time)
- From Dockerfile (standard way use by companies out there)

## Instruction in Dockerfile
**1. FROM**

The Dockerfile FROM command specifies the base image of your Docker images.

**A base image** is the image that is used to create all of your container images. Your base image can be an official Docker image, such as Centos, or you can modify an official Docker image to suit your needs, or you can create your own base image from scratch.

If you want to start with a bare Linux image, you can use this FROM command:
```Dockerfile
FROM ubuntu:latest
```

**2. COPY**

The Dockerfile `COPY` command copies one or more files from the Docker host (the computer building the Docker image from the Dockerfile) into the Docker image. The COPY command can copy both a file or a directory from the Docker host to the Docker image. Here are some Dockerfile COPY examples:
```
COPY /myapp/target/myapp.jar /myapp/myapp.jar
```
This example copies a single file call `myapp.jar` from the Docker host at `/myapp/target/myapp.jar` to the Docker image at `/myapp/myapp.jar.` The first argument is the Docker host path (where to copy from) and the second argument is the Docker image path (where to copy to).


```Dockerfile
COPY /myapp/target/* /myapp/
```
This example copies all single files and directories from the Docker host at `/myapp/target/` to the Docker image at `/myapp/`. The first argument is the Docker host path (where to copy from) and the second argument is the Docker image path (where to copy to).


**3.ADD**

The Dockerfile `ADD` instruction works in the same way as the `COPY` instruction with a few minor differences:
- The `ADD` instruction can copy and extract TAR files from the Docker host to the Docker image.
- The `ADD` instruction can download files via HTTP and copy them into the Docker image.

**Here are a few Dockerfile ADD examples:**

```Dockerfile
ADD index.html /var/www/html/
ADD /covid19/* /var/www/html/
ADD https://warfiles-for-docker.s3.amazonaws.com/addressbook.war /usr/local/tomcat/webapps/
```

**4. RUN**

The RUN command is executed during build time of the Docker image, so `RUN` commands are only **executed once**. The RUN command can be used to extract files, to download files, to install packages, to run all shell command or other command line activities which are necessary to run while building the docker image.

```Dockerfile
RUN apt-get install some-needed-app
RUN yum -y install git
RUN mkdir -p /tmp/file
```

```Dockerfile
RUN apt -y update
RUN apt -y install wget
RUN apt -y install unzip

OR

RUN apt -y update && \
    apt -y install wget && \
    apt -y install unzip
```

```Dockerfile
RUN cd /usr/local/apache2/htdocs
RUN rm -rf * 
RUN wget https://linux-devops-course.s3.amazonaws.com/WEB+SIDE+HTML/covid19.zip 
RUN unzip covid19.zip 
RUN cp -R covid19/* . 
RUN rm -rf covid19.zip 
RUN rm -rf covid19

OR 

RUN cd /usr/local/apache2/htdocs && \
    rm -rf * && \
    wget https://linux-devops-course.s3.amazonaws.com/WEB+SIDE+HTML/covid19.zip && \
    unzip covid19.zip && \
    cp -R covid19/* .
    rm -rf covid19.zip && \
    rm -rf covid19 && \ 
```

**5. WORKDIR**

The `WORKDIR` instruction specifies what the working directory should be inside the Docker image. The working directory will be in effect for all commands following the WORKDIR instruction. Here is an example:

```Dockerfile
WORKDIR /tmp
WORKDIR /var/www/html/
WORKDIR /usr/local/apache2/htdocs
```

**6. EXPOSE**

The Dockerfile `EXPOSE` instruction opens up network ports in the Docker container to the world. For instance, if your Docker container runs a web server, that web server will probably need port `80` or `8080` open for any client to be able to connect to it. Here is an example of opening a network port using the EXPOSE command:

```Dockerfile
EXPOSE 8080
EXPOSE 80
```

**7. VOLUME**

The Dockerfile `VOLUME` instruction creates a directory inside the Docker image which you can later mount a volume (directory) to from the Docker host. In other words, you can create a directory inside the docker image, called `/data` which can later be mounted to a directory, called `/container-data/container1` in the Docker host. The mounting is done when the container is started up and it is use to share data between the container and the host. Here is an example of defining a volume (mountable directory) in a Dockerfile using the VOLUME instruction:
```
VOLUME /data
```

**8- LABEL**

LABEL allows you to add a label or metadata to your docker image.

```Dockerfile
LABEL maintainer="devopseasylearning.com"
LABEL build_date="2017-09-05"
```

**9. USER**

USER sets username of the user which is to run the container. By default, containers **run as root**. 

```Dockerfile
USER root
```

**10. CMD && ENTRYPOINT**

Both are used to execution commands when the container start or during the creation of the container. We can use `CMD && ENTRYPOINT` to start the process when the container start or to run a script when the container start.

```Dockerfile
CMD ["script.sh"]
ENTRYPOINT["script.sh"]

CMD ["/usr/sbin/httpd", "-D", "FOREGROUND"]
ENTRYPOINT ["/usr/sbin/httpd", "-D", "FOREGROUND"]

CMD ["httpd-foreground"]
ENTRYPOINT ["httpd-foreground"]

CMD ["catalina.sh", "run"]
ENTRYPOINT ["catalina.sh", "run"]
```


### Differences between a Dockerfile, Docker Image and Docker Container
- **A Dockerfile** is a recipe for creating Docker images
- **A Docker image** gets built by running a Docker command (which uses a Dockerfile)
- **A Docker container** is a running instance of a Docker image

### What is the difference between CMD, ENTRYPOINT AND RUN command?
`RUN` command will be executed during the creation of the image while `CMD && ENTRYPOINT` will execute during the creation of the container. `ENTRYPOINT` can not be overwritten at the cli while `CMD` can be overwritten at the cli.


## Dockerfile Example 1
```
vim index.html
```

```html
<!DOCTYPE html>
<html>
<body style="background-color:rgb(217, 250, 210);">

<h1>Welcome to DevOps Easy Learning</h1>
<h3>This is to test httpd Web server at devopseasylearning.com</h3>

</body>
</html>
```

```
vim Dockerfile
```

```Dockerfile
FROM centos:latest
LABEL maintainer="Tia M"
RUN yum -y update && \
    yum -y install httpd

COPY index.html /var/www/html/
USER root
ENTRYPOINT ["/usr/sbin/httpd", "-D", "FOREGROUND"]
EXPOSE 80
```

**Biuld and test**
```
docker build -t <username>/<repository name>:tag
docker build -t leonardtia/devops_repo:test .
docker run --name test -p 8030:80 -d leonardtia/devops_repo:test
docker login
docker push leonardtia/devops_repo:test
```

**Access Application locally**
```
http://<IP>:8030/
http://10.0.0.94:8030/
```


## Dockerfile Example 2
```
vim Dockerfile
```
```Dockerfile
FROM httpd
LABEL maintainer="Tia M"
RUN apt -y update && \
    apt -y install wget && \
    apt -y install unzip

WORKDIR /usr/local/apache2/htdocs/

RUN rm -rf * && \
    wget https://linux-devops-course.s3.amazonaws.com/WEB+SIDE+HTML/covid19.zip && \
    unzip covid19.zip && \
    cp -R covid19/* . && \
    rm -rf covid19.zip && \
    rm -rf covid19

USER root
CMD ["httpd-foreground"]
EXPOSE 80
```

**Biuld and test**
```
docker build -t <username>/<repository name>:tag
docker build -t leonardtia/devops_repo:covid19 .
docker run --name covid19 -p 8010:80 -d leonardtia/devops_repo:covid19 
docker login
docker push leonardtia/devops_repo:covid19
```

**Access Application locally**
```
http://<IP>:8010/
http://10.0.0.94:8010/
```

## Dockerfile Example 3
```
vim Dockerfile
```
```Dockerfile
FROM httpd
LABEL maintainer="Tia M"
RUN apt -y update && \
    apt -y install wget && \
    apt -y install unzip

WORKDIR /usr/local/apache2/htdocs/

RUN rm -rf * && \
    wget https://linux-devops-course.s3.amazonaws.com/WEB+SIDE+HTML/static-website-example.zip && \
    unzip static-website-example.zip && \
    cp -R static-website-example/* . && \
    rm -rf static-website-example.zip && \
    rm -rf static-website-example

USER root
ENTRYPOINT ["httpd-foreground"]
EXPOSE 80
```

**Biuld and test**
```
docker build -t <username>/<repository name>:tag
docker build -t leonardtia/devops_repo:static-website-example .
docker run --name static-website-example -p 8020:80 -d leonardtia/devops_repo:static-website-example
docker login
docker push leonardtia/devops_repo:static-website-example
```

**Access Application locally**
```
http://<IP>:8020/
http://10.0.0.94:8020/
```

## Dockerfile Example 4

```
vim Dockerfile
```
```Dockerfile
FROM tomcat
LABEL maintainer="Tia M"
ADD https://warfiles-for-docker.s3.amazonaws.com/addressbook.war /usr/local/tomcat/webapps/
ENTRYPOINT ["catalina.sh", "run"]
EXPOSE 8080
```

**Biuld and test**
```
docker build -t <username>/<repository name>:tag
docker build -t leonardtia/devops_repo:addressbook .
docker run --name addressbook -p 8050:8080 -d leonardtia/devops_repo:addressbook
docker login
docker push leonardtia/devops_repo:addressbook
```

**Access Application locally**
```
http://<IP>:8050/addressbook
http://10.0.0.94:8050/addressbook
```

