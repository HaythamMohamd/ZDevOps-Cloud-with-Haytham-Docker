## Devops

Here Devops Roadmap post at Linkedin [DevOps_Roadmap](https://www.linkedin.com/posts/haythamm3wadallah_haythamabrdevopsabr101-aehafeaesaetafaabraeyafaaeuaesaepafcaehabraeqaev-activity-7069259330746728448-EUHU/?utm_source=share&utm_medium=member_ios):

![alt text](image-182.png)

- Linux administration (RHCSA prefered)
- Network basic knowledge
- Virtualization (vmware vsphere prefered)
- SDLC (software development life cycle) just the concept at the beginning
- git
- CI/CD tool (Jenkins or azure devops or github actions ...etc)
- Docker & Docker compose
- kubernetes
- Ansible
- Terraform
- Cloud(AWS, Azure, Google, ...)
- Monitring (Prometheus and Grafana)

## Docker

Docker is the most popular containerization technology. Containerization addresses the problem of *"It works on my machine"* by packaging an **application** with its **environment** as an image.
> Environment includes the OS setup with all the needed tools/packages installed with their specific versions.

![Docker Architecture](https://media.geeksforgeeks.org/wp-content/uploads/20221205115118/Architecture-of-Docker.png)

**How it works & common commands:**

- A `Dockerfile` inclues the recipe for building an image: `docker build -t <TAG> .`
- One or more containers are instantiated from a given image: `docker run <TAG>`
- You can check running containers using `docker ps` or use docker extensions for your IDE.
- An image is typically pushed to a container registry (e.g., DockerHub): `docker login`, `docker push`
- Images can then be pulled from the registry for usage: `docker pull`

### Docker Installation

- Recommended way:

  Follow the official installation steps at <https://docs.docker.com/desktop/install/linux-install/> to install docker desktop for your distro with latest updates and supplementary tools.

- install on Centos8

  ```bash
  # Update the System
  sudo dnf update -y

  # Add Docker Repository
  sudo dnf config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo

  # Install Docker
  sudo dnf install docker-ce docker-ce-cli containerd.io -y

  # (Optional) Manage Docker as a Non-Root User
  sudo usermod -aG docker $USER

  # Start and Enable Docker
  sudo systemctl start docker
  sudo systemctl enable docker
  sudo systemctl status docker

  ```

### Docker Engine Architecture

![alt text](image-4.png)

- **docker cli:** for command line
- **REST API:** provide an interface to manage objects
- **Docker Daemon:** responsible for create and manage objects (images, containers, networks and volumes)
- **LXC:** the old method which use linux kernel capabilities  (Namespace amd CGroups) to manage containers
  - Namespace
  - CGroups
- **LibContainer:** writen in go language , and docker dealed with kernel directly
![alt text](image-1.png)
- **LibContainer replaced LXC:**
- **OCI:** provides some standard for creation images and containers
![alt text](image-3.png)
- **Docker Daemon:** responsible for create and manage objects (images, containers, networks and volumes)
![alt text](image-5.png)
- **RunC:** to ececute the standard of OCI so organized the tasks of containerd and runC is responsible for creation the containers
![alt text](image-6.png)
- **containerd:** for organize more so containerd manage containers then send to runC to start it then send to LibContainer to create Namespace CGroup from kernel
![alt text](image-7.png)

- **what will happen if docker is down?:** all things will be down,to fix this point the containerd-shim process is here,so if daemon is down,the containers will be running
![alt text](image-8.png)
- **docker objects:**
  - Images
  - Networks
  - Containers
  - Volumes
  
![alt text](image-9.png)

- **Registry:** is the place that hold the images (dockerhub, ECR, ...)
![alt text](image-10.png)
Docker Service Configuration
  - Docker CLI: run the command for example `docker container run -it ubuntu`
  - REST API: the command will go as API to Docker Daemon
  - Docker Daemon: will search about the image local and if not found will download from DockerHub as this is the default, then call containerd
  - Containerd: will create the container and send to container-shim
  - Container-shim: will manage the container and send to runC
  - runC: will contact with kernel for Namespace and CGroup
![alt text](image-11.png)

**Docker Service Configuration:**

```bash
systemctl start docker
systemctl stop docker
systemctl status docker

dockerd
dockerd --debug 
dockerd --debug --host=tcp://192.168.1.10:2375
```

![alt text](image-12.png)
![alt text](image-13.png)
![alt text](image-14.png)
![alt text](image-15.png)
![alt text](image-16.png)
![alt text](image-17.png)
![alt text](image-18.png)
![alt text](image-19.png)

**Basic Container Operations:**

```bash
docker container run -it ubuntu
docker image build .
docker container attach ubutu
docker container kill ubutu
docker image ls 
docker container ls 

docker container create httpd
ls /var/lib/docker
ls -ltr  /var/lib/docker/containers
ls /var/lib/docker/containers/21f4888fbe384a3e1d203a20a643edd837aa8a18dfd241818653d8af6d140349

docker container ls 
docker container ls -a
# to get last created container 
docker container ls -l
# to get the short name of running containers
docker container ls -q
# to get all
docker container ls -aq

# to start container
docker container start httpd
docker container ls

# docker run = create and start the container 
docker container run httpd

```

![alt text](image-20.png)

**Container process:** the idea for container to ececute one task and then die, so you should run continous one

![alt text](image-21.png)
![alt text](image-22.png)

**create container with name and rename it:**

```bash
docker container run -itd  --name=webapp httpd
docker container rename webapp new-webapp
```

**Interacting with a Running Container:**

```bash
docker container run -itd  --name=webapp httpd
docker container ls -l
docker container exec 1115ddd hostname 
[root@jenkins ~]# docker exec -it  2867e561c297 hostname
2867e561c297
```

**Inspecting a Container:**

```bash
docker inspect 2867e561c297
docker container stats
docker container top ubuntu

docker container logs ubuntu
docker container logs -f ubuntu

docker system events  --since 10m
```

**Stopping and Removing a Container:**

```bash
docker container run --name web httpd
docker container pause web
docker container unpause web
docker container stop web
docker container kill --signal=-9 web

# removing containers
docker container rm web
docker container ls -a
docker container ls -q
docker container stop $(docker container ls -q)
docker container rm $(docker container ls -aq)
alias boom='docker container stop $(docker container ls -q); docker container rm $(docker container ls -aq)'
docker container prune

```

![alt text](image-23.png)
![alt text](image-24.png)
![alt text](image-25.png)
![alt text](image-26.png)
![alt text](image-27.png)
![alt text](image-28.png)
![alt text](image-29.png)

**Setting a Container Hostname:**

```bash
[root@jenkins ~]# docker container run -it --name ubuntu  ubuntu
root@c946d8540af2:/# hostname
c946d8540af2

[root@jenkins ~]# docker container run -it  --name ubuntu --hostname ubuntu ubuntu
root@ubuntu:/# hostname
ubuntu
```

**Restart Policies:** you will use `--retart=(no, on-failure, always, unless stopped)`

- no: means no automatic restart
- on-failure: if exit code is 1 will restart
- always: will restart container if exit code is 0 or 1
- unless stopped: like always but if you start manual will not restart automatic

```bash
docker container run --restart=no ubuntu
docker container run --restart=on-failure ubuntu
docker container run --restart=always ubuntu
docker container run --restart=unless-stopped ubuntu
```

![alt text](image-30.png)

**Live restore:**: if you stop docker all the containers will be down so put this `"live-restore":true`in `/etc/docker/daemon.json`
![alt text](image-31.png)

**Copying Contents into Container:**

```bash
[root@jenkins docker_kodekloud]#   docker container run -itd --name ubuntu  ubuntu
be70fee232f82a224e20a8ce12497191b00a3132aed4dbc7e4c9f3687fc9ffe0
[root@jenkins docker_kodekloud]#
[root@jenkins docker_kodekloud]# docker container cp index.html ubuntu:/tmp
Successfully copied 2.05kB to ubuntu:/tmp
[root@jenkins docker_kodekloud]# docker exec -it ubuntu ls /tmp
index.html

```

![alt text](image-32.png)
![alt text](image-33.png)

**Publishing Ports:**

![alt text](image-34.png)
![alt text](image-35.png)
![alt text](image-36.png)
![alt text](image-37.png)
![alt text](image-38.png)
![alt text](image-39.png)
![alt text](image-40.png)
![alt text](image-41.png)
![alt text](image-42.png)
![alt text](image-43.png)

**Demo - Docker Container Operations Continued:**

- run and stop container
- restart Policies
- system events
- port mapping

```bash
docker container run -itd --name kodekloud --rm ubuntu
docker ps -a
docker container ls -l
docker container stop kodekloud
docker container ls -l
docker container run -itd --name kodekloud --hostname haytham_ubuntu --rm ubuntu
docker container ls -l

# restart options 
[root@jenkins docker_kodekloud]# docker container run -itd --name case_no --restart=no ubuntu
1db5c8d4d34c47a9edb03f7c343881f65991ad704e6955b6f8aab7a05501bb35
[root@jenkins docker_kodekloud]# docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
1db5c8d4d34c   ubuntu    "/bin/bash"   6 seconds ago   Up 5 seconds             case_no
[root@jenkins docker_kodekloud]# docker ps -a
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS                       PORTS     NAMES
1db5c8d4d34c   ubuntu    "/bin/bash"   33 seconds ago   Exited (137) 4 seconds ago             case_no

[root@jenkins docker_kodekloud]# docker container run -itd --name case_on-failure --restart=on-failure ubuntu
f8b2c3ad210aa4dcbb5d42f49ce80e0ccacba66ee3bafc9624a807d242f976e0
[root@jenkins docker_kodekloud]# docker container top case_on-failure
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                17173               17153               0                   23:38               pts/0               00:00:00            /bin/bash
[root@jenkins docker_kodekloud]# docker ps -a
CONTAINER ID   IMAGE     COMMAND       CREATED              STATUS                            PORTS     NAMES
f8b2c3ad210a   ubuntu    "/bin/bash"   22 seconds ago       Up 21 seconds                               case_on-failure
1db5c8d4d34c   ubuntu    "/bin/bash"   About a minute ago   Exited (137) About a minute ago             case_no
[root@jenkins docker_kodekloud]# kill -p 17173
[root@jenkins docker_kodekloud]# docker ps -a
CONTAINER ID   IMAGE     COMMAND       CREATED              STATUS                            PORTS     NAMES
f8b2c3ad210a   ubuntu    "/bin/bash"   38 seconds ago       Up 3 seconds                                case_on-failure
1db5c8d4d34c   ubuntu    "/bin/bash"   About a minute ago   Exited (137) About a minute ago             case_no

[root@jenkins docker_kodekloud]# docker container run -itd --name case_always --restart=always ubuntu
b241a487f68040ed0aa8c87a8118723b971328b7013d4506234c11c9b3390cd6
[root@jenkins docker_kodekloud]#
[root@jenkins docker_kodekloud]# docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
b241a487f680   ubuntu    "/bin/bash"   3 seconds ago   Up 2 seconds             case_always
[root@jenkins docker_kodekloud]#
[root@jenkins docker_kodekloud]# systemctl restart docker
[root@jenkins docker_kodekloud]#
[root@jenkins docker_kodekloud]# docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS          PORTS     NAMES
b241a487f680   ubuntu    "/bin/bash"   12 seconds ago   Up 11 seconds             case_always


[root@jenkins docker_kodekloud]#  docker container run -itd --name case_unless-stopped --restart=unless-stopped ubuntu
e2075c206feb2068afa7ad7adfafceaa1f3de16043eb39a4d645f55641dbc4da
[root@jenkins docker_kodekloud]# docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
e2075c206feb   ubuntu    "/bin/bash"   4 seconds ago   Up 3 seconds             case_unless-stopped
[root@jenkins docker_kodekloud]#
[root@jenkins docker_kodekloud]# docker container stop case_unless-stopped
case_unless-stopped
[root@jenkins docker_kodekloud]#
[root@jenkins docker_kodekloud]# docker ps -a
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS                       PORTS     NAMES
e2075c206feb   ubuntu    "/bin/bash"   36 seconds ago   Exited (137) 5 seconds ago             case_unless-stopped
[root@jenkins docker_kodekloud]#
[root@jenkins docker_kodekloud]# systemctl restart docker
[root@jenkins docker_kodekloud]#
[root@jenkins docker_kodekloud]# docker ps -a
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS                        PORTS     NAMES
e2075c206feb   ubuntu    "/bin/bash"   45 seconds ago   Exited (137) 14 seconds ago             case_unless-stopped
[root@jenkins docker_kodekloud]#

# system events
[root@jenkins docker_kodekloud]# docker system events --since 10m

# copy
[root@jenkins docker_kodekloud]# docker container run -itd --name copy --rm ubuntu
3007100f08ea4fafcc2a1e3b8bfafe082f0bc881fbde3495c80fe4727291db1b
[root@jenkins docker_kodekloud]#
[root@jenkins docker_kodekloud]# docker container cp index.html copy:/tmp
Successfully copied 2.05kB to copy:/tmp
[root@jenkins docker_kodekloud]# docker exec -it copy ls /tmp
index.html
[root@jenkins docker_kodekloud]#

# port mapping
[root@jenkins docker_kodekloud]# docker container run -itd --name case01 httpd
6f20f735db3f1d88678de887eee893ea16e7e48c4f8699b16f49ee3b139d37cf
[root@jenkins docker_kodekloud]#
[root@jenkins docker_kodekloud]# docker ps -a
CONTAINER ID   IMAGE     COMMAND              CREATED         STATUS         PORTS     NAMES
6f20f735db3f   httpd     "httpd-foreground"   6 seconds ago   Up 5 seconds   80/tcp    case01
[root@jenkins docker_kodekloud]# docker container run -itd -P --name case02 httpd
[root@jenkins docker_kodekloud]# docker ps -a
CONTAINER ID   IMAGE     COMMAND              CREATED          STATUS          PORTS                                     NAMES
6354c24cb248   httpd     "httpd-foreground"   3 seconds ago    Up 2 seconds    0.0.0.0:32768->80/tcp, :::32768->80/tcp   case02
6f20f735db3f   httpd     "httpd-foreground"   37 seconds ago   Up 36 seconds   80/tcp                                    case01
[root@jenkins docker_kodekloud]# docker container run -itd -p 82:80 --name case03 httpd
1e3871a0fdb15cec74ed55fdf20b1490a9c5a11591dd74f7c5f899ca8c71b8c6
[root@jenkins docker_kodekloud]# docker ps -a
CONTAINER ID   IMAGE     COMMAND              CREATED              STATUS              PORTS                                     NAMES
1e3871a0fdb1   httpd     "httpd-foreground"   40 seconds ago       Up 38 seconds       0.0.0.0:82->80/tcp, :::82->80/tcp         case03
6354c24cb248   httpd     "httpd-foreground"   About a minute ago   Up About a minute   0.0.0.0:32768->80/tcp, :::32768->80/tcp   case02
6f20f735db3f   httpd     "httpd-foreground"   2 minutes ago        Up 2 minutes        80/tcp                                    case01
[root@jenkins docker_kodekloud]#

# here port 32768 will  be changed to other port after restart container 
[root@jenkins docker_kodekloud]# docker container restart case02
case02
[root@jenkins docker_kodekloud]# docker ps -a
CONTAINER ID   IMAGE     COMMAND              CREATED              STATUS          PORTS                                     NAMES
1e3871a0fdb1   httpd     "httpd-foreground"   55 seconds ago       Up 54 seconds   0.0.0.0:82->80/tcp, :::82->80/tcp         case03
6354c24cb248   httpd     "httpd-foreground"   About a minute ago   Up 1 second     0.0.0.0:32769->80/tcp, :::32769->80/tcp   case02
6f20f735db3f   httpd     "httpd-foreground"   2 minutes ago        Up 2 minutes    80/tcp                                    case01

# here port 82 will not be changed after restart container 
[root@jenkins docker_kodekloud]# docker container restart case03
case03
[root@jenkins docker_kodekloud]# docker ps -a
CONTAINER ID   IMAGE     COMMAND              CREATED         STATUS              PORTS                                     NAMES
1e3871a0fdb1   httpd     "httpd-foreground"   2 minutes ago   Up 2 seconds        0.0.0.0:82->80/tcp, :::82->80/tcp         case03
6354c24cb248   httpd     "httpd-foreground"   2 minutes ago   Up About a minute   0.0.0.0:32769->80/tcp, :::32769->80/tcp   case02
6f20f735db3f   httpd     "httpd-foreground"   3 minutes ago   Up 3 minutes        80/tcp                                    case01

```

**Troubleshooting Docker Daemon:**

- if you got this error

![alt text](image-52.png)

- you should understand difference between http and https

  ![alt text](image-59.png)
  ![alt text](image-60.png)

- check logs

![alt text](image-55.png)  ![alt text](image-53.png)
![alt text](image-56.png)

- check daemon config file

  ![alt text](image-57.png)

- check free disk space on host

  ![alt text](image-58.png)

**Demo - Docker Debug Mode:**

```bash

[root@jenkins docker_kodekloud]# docker system info | grep -i Debug
 Debug Mode: false

[root@jenkins docker_kodekloud]# cat /etc/docker/daemon.json
{
  "debug": true
}

systemctl daemon-reload
```

**Loging drivers:**

```bash
[root@jenkins docker_kodekloud]# docker run -d --name nginx nginx
[root@jenkins docker_kodekloud]# docker logs nginx
[root@jenkins docker_kodekloud]# docker system info | grep -i logg
 Logging Driver: json-file

[root@jenkins docker_kodekloud]# cd /var/lib/docker/containers/; ls
5cf5d10c6abb0f8f79cb3469a0b9571f3f8aadd1c60056e26af523efc14e06d7
[root@jenkins containers]# cat 5cf5d10c6abb0f8f79cb3469a0b9571f3f8aadd1c60056e26af523efc14e06d7/5cf5d10c6abb0f8f79cb3469a0b9571f3f8aadd1c60056e26af523efc14e06d7-json.log

```

![alt text](image-61.png)

![alt text](image-62.png)

- if you want to change to aws logs

![alt text](image-63.png)

- log drivers options

![alt text](image-64.png)

**Docker Image Registry:**

```bash
docker run ubuntu
docker run ubuntu:latest
docker run ubuntu:18.04
docker run ubuntu:trusty

docker images ls

docker search httpd
docker search httpd --limit 2
docker search --filter stars=10 httpd 

docker image pull httpd
docker image list 
```

**Image Addressing Convention:**

![alt text](image-65.png)

**Authenticating to Registries:**

![alt text](image-66.png)
![alt text](image-67.png)
![alt text](image-68.png)
![alt text](image-69.png)

**Removing a Docker Image:**

![alt text](image-70.png)
![alt text](image-71.png)
![alt text](image-72.png)

**Inspecting a Docker Image:**

![alt text](image-73.png)
![alt text](image-74.png)
![alt text](image-75.png)

**Save and Load Images:**

![alt text](image-76.png)
![alt text](image-77.png)
![alt text](image-78.png)

**Demo - Image Registry and Operations:**

```bash
[root@jenkins containers]# docker search httpd
NAME                     DESCRIPTION                                     STARS     OFFICIAL
httpd                    The Apache HTTP Server Project                  4841      [OK]
manageiq/httpd           Container with httpd, built on CentOS for Ma…   1
paketobuildpacks/httpd                                                   0
vulhub/httpd                                                             0
openquantumsafe/httpd    Demo of post-quantum cryptography in Apache …   17
openeuler/httpd                                                          0
dockerpinata/httpd                                                       1
e2eteam/httpd                                                            0
manasip/httpd                                                            0
centos/httpd                                                             36

[root@jenkins containers]# docker search httpd --limit 3
NAME                     DESCRIPTION                                     STARS     OFFICIAL
httpd                    The Apache HTTP Server Project                  4841      [OK]
manageiq/httpd           Container with httpd, built on CentOS for Ma…   1
paketobuildpacks/httpd                                                   0


[root@jenkins containers]# docker search --filter stars=10 httpd
NAME                    DESCRIPTION                                     STARS     OFFICIAL
httpd                   The Apache HTTP Server Project                  4841      [OK]
openquantumsafe/httpd   Demo of post-quantum cryptography in Apache …   17
centos/httpd                                                            36
arm32v7/httpd           The Apache HTTP Server Project                  11
arm64v8/httpd           The Apache HTTP Server Project                  11

[root@jenkins containers]# docker search --filter  is-official=true httpd
NAME      DESCRIPTION                      STARS     OFFICIAL
httpd     The Apache HTTP Server Project   4841      [OK]

docker pull httpd
docker pull httpd:alpine

# tag
docker tag httpd:apline httpd:kodekloudv1

# check size of images
docker system df

# tag and push
[root@jenkins containers]# docker tag ubuntu:latest haytham1992/java-maven:ubuntu
[root@jenkins containers]# docker push haytham1992/java-maven:ubuntu

# rm
docker rm httpd:alpine

# save and load
[root@jenkins containers]# docker image save httpd:latest -o haytham.tar
[root@jenkins containers]# docker image load -i haytham.tar

# to get layers
[root@jenkins containers]# docker image history ubuntu:latest
IMAGE          CREATED       CREATED BY                                      SIZE      COMMENT
a04dc4851cbc   5 weeks ago   /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
<missing>      5 weeks ago   /bin/sh -c #(nop) ADD file:6df775300d76441aa…   78.1MB
<missing>      5 weeks ago   /bin/sh -c #(nop)  LABEL org.opencontainers.…   0B
<missing>      5 weeks ago   /bin/sh -c #(nop)  LABEL org.opencontainers.…   0B
<missing>      5 weeks ago   /bin/sh -c #(nop)  ARG LAUNCHPAD_BUILD_ARCH     0B
<missing>      5 weeks ago   /bin/sh -c #(nop)  ARG RELEASE                  0B

# export from container to image then import
[root@jenkins containers]# docker run -d --name nginx nginx
a284951e77bfb10d792373fd583d33a113f8454499fe20b8d3cc2693598bd9d7
[root@jenkins containers]#
[root@jenkins containers]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS     NAMES
a284951e77bf   nginx     "/docker-entrypoint.…"   13 seconds ago   Up 12 seconds   80/tcp    nginx
[root@jenkins containers]# docker  export nginx > haytham.tar
[root@jenkins containers]# ls
haytham.tar
[root@jenkins containers]# docker image import haytham.tar haytham:latest
sha256:9cca021d25fdee6299c48c8d7859eebde40d9e28f8f4da5faf80b1ec5502ec03
[root@jenkins containers]# docker images | grep -i hay
haytham                          latest             9cca021d25fd   12 seconds ago   190MB

```

**Building a custom image:**

![alt text](image-79.png)
![alt text](image-80.png)
![alt text](image-81.png)
![alt text](image-82.png)
![alt text](image-83.png)
![alt text](image-84.png)

**Demo - Build HTTPD image:**

```bash
mkdir httpd_project ; cd mkdir httpd_project
[root@jenkins httpd_project]# echo "<h1>Hello from Dockerfile </h1>" > index.html


[root@jenkins httpd_project]# cat > Dockerfile
FROM centos:7

# Fix repository URLs
RUN sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*.repo && \
    sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*.repo

# Update and install httpd
RUN yum -y update && \
    yum -y install httpd

# Copy custom index.html
COPY ./index.html /var/www/html/index.html

# Expose port 80
EXPOSE 80

# Start httpd service
CMD ["httpd", "-D", "FOREGROUND"]

[root@jenkins httpd_project]# docker image history haytham1992/test_httpd:v1
IMAGE          CREATED          CREATED BY                                      SIZE      COMMENT
bbeaa72995f0   52 seconds ago   CMD ["httpd" "-D" "FOREGROUND"]                 0B        buildkit.dockerfile.v0
<missing>      52 seconds ago   EXPOSE map[80/tcp:{}]                           0B        buildkit.dockerfile.v0
<missing>      52 seconds ago   COPY ./index.html /var/www/html/index.html #…   32B       buildkit.dockerfile.v0
<missing>      52 seconds ago   RUN /bin/sh -c yum -y update &&     yum -y i…   428MB     buildkit.dockerfile.v0
<missing>      2 minutes ago    RUN /bin/sh -c sed -i 's/mirrorlist/#mirrorl…   15kB      buildkit.dockerfile.v0
<missing>      3 years ago      /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
<missing>      3 years ago      /bin/sh -c #(nop)  LABEL org.label-schema.sc…   0B
<missing>      3 years ago      /bin/sh -c #(nop) ADD file:b3ebbe8bd304723d4…   204MB


[root@jenkins httpd_project]# docker run -itd --name haytham -p 82:80 haytham1992/test_httpd:v1
[root@jenkins httpd_project]# docker push haytham1992/test_httpd:v1

```

**Demo - Build Tomcat image:**

```bash
git clone https://github.com/yogeshraheja/dockertomcat.git
cd dockertomcat/

[root@jenkins dockertomcat]# cat Dockerfile
FROM centos:7
# Fix repository URLs
RUN sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*.repo && \
    sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*.repo
ARG tomcat_version=8.5.6
RUN  yum install -y epel-release java-1.8.0-openjdk.x86_64 wget
RUN groupadd tomcat && mkdir /opt/tomcat
RUN useradd -s /bin/nologin -g tomcat -d /opt/tomcat tomcat
WORKDIR /
RUN wget https://archive.apache.org/dist/tomcat/tomcat-8/v$tomcat_version/bin/apache-tomcat-$tomcat_version.tar.gz
RUN tar -zxvf apache-tomcat-$tomcat_version.tar.gz -C /opt/tomcat --strip-components=1
RUN cd /opt/tomcat && chgrp -R tomcat conf
RUN chmod g+rwx /opt/tomcat/conf && chmod g+r /opt/tomcat/conf/*
RUN chown -R tomcat /opt/tomcat/logs/ /opt/tomcat/temp /opt/tomcat/webapps /opt/tomcat/work
RUN chgrp -R tomcat /opt/tomcat/bin && chgrp -R tomcat /opt/tomcat/lib && chmod g+rwx /opt/tomcat/bin && chmod g+r /opt/tomcat/bin/*
WORKDIR /opt/tomcat/webapps
RUN wget https://tomcat.apache.org/tomcat-7.0-doc/appdev/sample/sample.war
EXPOSE 8080
CMD ["/opt/tomcat/bin/catalina.sh","run"]

docker build -t haytham1992/test_tomcat:v1 .
docker run -itd --name haytham_tomcat_v1 -p 85:8080 haytham1992/test_tomcat:v1

docker build -t haytham1992/test_tomcat:v2 --build-arg tomcat_version=8.5.8 .
docker run -itd --name haytham_tomcat_v2 -p 90:8080 haytham1992/test_tomcat:v2

```

**Docker commit method:**

![alt text](image-85.png)
![alt text](image-86.png)

**Demo - Image Creation Docker Commit Method:**

```bash
[root@jenkins dockertomcat]# docker pull  centos:7
[root@jenkins dockertomcat]# docker container create -it --name centos7 centos:7
[root@jenkins dockertomcat]# docker container start centos7
[root@jenkins dockertomcat]# docker exec -it centos7 /bin/bash
[root@8f660e078173 /]# sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*.repo 
[root@8f660e078173 /]# sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' etc/yum.repos.d/CentOS-*.repo
[root@8f660e078173 /]# yum install -y httpd
[root@8f660e078173 /]# echo "hello from kodekloud" > /var/www/html/index.html
[root@8f660e078173 /]# exit 

[root@jenkins dockertomcat]# docker container stop centos7
centos7
# -a ==> author and -c ==> the commad for docker file , centos7 ==> is the name of container centos7_from_commit_conatiner:v1 => the new image name 
[root@jenkins dockertomcat]# docker container commit -a "Haytham Awadallah" -c 'CMD ["httpd","-D","FOREGROUND"]' centos7 centos7_from_commit_conatiner:v1
sha256:6c8d949c009536c157e7179a62e74eab3a365e6ad4e8b35335c84f90fbffd4b3
[root@jenkins dockertomcat]# docker container run -itd --name commit-container -p 80:80 centos7_from_commit_conatiner:v1
13a951f178834ab3a55f1a2f1c48a52bc243989fbd1577a2c9b80cde0e11fcd6
[root@jenkins dockertomcat]#

[root@jenkins dockertomcat]# docker tag centos7_from_commit_conatiner:v1 haytham1992/centos7_from_commit_conatiner:v1
[root@jenkins dockertomcat]# docker push haytham1992/centos7_from_commit_conatiner:v1

```

**Build Contexts:**

![alt text](image-87.png)
![alt text](image-89.png)
![alt text](image-90.png)
![alt text](image-91.png)
![alt text](image-92.png)

**Build Cache:**

![alt text](image-93.png)
![alt text](image-94.png)
![alt text](image-95.png)
![alt text](image-96.png)
![alt text](image-97.png)
![alt text](image-98.png)
![alt text](image-99.png)
![alt text](image-100.png)
![alt text](image-101.png)
![alt text](image-102.png)
![alt text](image-103.png)
![alt text](image-104.png)

**COPY vs ADD:**

- Best practice is to use COPY not ADD why? as copy save layers than add

![alt text](image-105.png)
![alt text](image-106.png)

**CMD vs Entrypoint:**

![alt text](image-107.png)

![alt text](image-108.png)

![alt text](image-109.png)

![alt text](image-110.png)

![alt text](image-111.png)

![alt text](image-112.png)

![alt text](image-113.png)

![alt text](image-114.png)

![alt text](image-115.png)

![alt text](image-116.png)

![alt text](image-117.png)

![alt text](image-118.png)

**Base vs Parent Image:**

![alt text](image-119.png)
![alt text](image-120.png)
![alt text](image-121.png)
![alt text](image-122.png)
![alt text](image-123.png)
![alt text](image-124.png)

**Multi-Stage Builds:**

![alt text](image-125.png)

![alt text](image-126.png)

![alt text](image-127.png)

![alt text](image-128.png)

![alt text](image-129.png)

![alt text](image-130.png)

![alt text](image-131.png)

![alt text](image-132.png)

![alt text](image-133.png)

![alt text](image-134.png)

![alt text](image-135.png)

**Dockerfile - Best Practices:**

![alt text](image-136.png)

![alt text](image-137.png)

![alt text](image-138.png)

![alt text](image-139.png)

**Docker Daemon Security:**

![alt text](image-140.png)

![alt text](image-141.png)

![alt text](image-142.png)

![alt text](image-143.png)

![alt text](image-144.png)

![alt text](image-145.png)

![alt text](image-146.png)

![alt text](image-147.png)

**Namespaces and Capabilities:**

![alt text](image-148.png)
![alt text](image-149.png)
![alt text](image-150.png)
![alt text](image-151.png)
![alt text](image-152.png)
![alt text](image-153.png)
![alt text](image-154.png)
![alt text](image-155.png)

![alt text](image-156.png)

![alt text](image-157.png)
![alt text](image-158.png)
![alt text](image-159.png)
![alt text](image-160.png)

**CGroups:**

![alt text](image-161.png)
![alt text](image-162.png)

![alt text](image-163.png)
![alt text](image-164.png)

![alt text](image-165.png)
![alt text](image-166.png)

![alt text](image-167.png)
![alt text](image-168.png)
![alt text](image-169.png)
![alt text](image-170.png)

**Resource Limits Memory:**

![alt text](image-171.png)
![alt text](image-172.png)
![alt text](image-173.png)

**Demo - Resource Limits:**

![alt text](image-174.png)
![alt text](image-175.png)
![alt text](image-176.png)
![alt text](image-177.png)
![alt text](image-178.png)
![alt text](image-179.png)

**Docker Network:**

- create network bridge type
- cteate two containers in this network
- test ping each other

```bash
[root@jenkins docker_kodekloud]# docker network create --driver bridge --subnet 192.168.1.0/24 test_network
0d6ff82a83c8656db6be93293aba776ac29f9760923ad00eebd61bf4629f6836
[root@jenkins docker_kodekloud]#
[root@jenkins docker_kodekloud]# docker network inspect test_network
[
    {
        "Name": "test_network",
        "Id": "0d6ff82a83c8656db6be93293aba776ac29f9760923ad00eebd61bf4629f6836",
        "Created": "2025-03-09T20:46:03.187104823+02:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.168.1.0/24"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]
[root@jenkins docker_kodekloud]#  docker container run -itd --name container01 --network test_network centos:7
12b7d04908a3a0bdd6362335cd73780b08f5dad79b1d5479f3e0d94f40926f41
[root@jenkins docker_kodekloud]#
[root@jenkins docker_kodekloud]#  docker container run -itd --name container02 --network test_network centos:7
5638d4ab7c406207560b4cb7e97de3c3d3576c14e96243b52a135851c39d1130
[root@jenkins docker_kodekloud]#
[root@jenkins docker_kodekloud]# docker exec -it container01 ping container02
PING container02 (192.168.1.3) 56(84) bytes of data.
64 bytes from container02.test_network (192.168.1.3): icmp_seq=1 ttl=64 time=0.420 ms
64 bytes from container02.test_network (192.168.1.3): icmp_seq=2 ttl=64 time=0.179 ms
^C
--- container02 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 0.179/0.299/0.420/0.121 ms
[root@jenkins docker_kodekloud]#
[root@jenkins docker_kodekloud]#
[root@jenkins docker_kodekloud]# docker exec -it container01 cat /etc/resolv.conf
search localdomain
nameserver 127.0.0.11
options ndots:0
[root@jenkins docker_kodekloud]# docker network rm test_network
Error response from daemon: error while removing network: network test_network id 0d6ff82a83c8656db6be93293aba776ac29f9760923ad00eebd61bf4629f6836 has active endpoints
[root@jenkins docker_kodekloud]#
[root@jenkins docker_kodekloud]# docker network disconnect test_network container01
[root@jenkins docker_kodekloud]# docker network rm test_network
Error response from daemon: error while removing network: network test_network id 0d6ff82a83c8656db6be93293aba776ac29f9760923ad00eebd61bf4629f6836 has active endpoints
[root@jenkins docker_kodekloud]# docker network disconnect test_network container02
[root@jenkins docker_kodekloud]#
[root@jenkins docker_kodekloud]# docker network rm test_network
test_network

```

**Networking Deep Dive - Namespaces:**

```bash
[root@jenkins ~]# ip netns add red
[root@jenkins ~]# ip netns add blue
[root@jenkins ~]#
[root@jenkins ~]# ip netns
blue
red
[root@jenkins ~]#
[root@jenkins ~]# ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: ens160: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP mode DEFAULT group default qlen 1000
    link/ether 00:0c:29:c9:f1:cd brd ff:ff:ff:ff:ff:ff
3: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN mode DEFAULT group default qlen 1000
    link/ether 52:54:00:52:5e:0a brd ff:ff:ff:ff:ff:ff
4: virbr0-nic: <BROADCAST,MULTICAST> mtu 1500 qdisc fq_codel state DOWN mode DEFAULT group default qlen 1000
    link/ether 52:54:00:52:5e:0a brd ff:ff:ff:ff:ff:ff
5: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN mode DEFAULT group default
    link/ether 02:42:11:86:51:55 brd ff:ff:ff:ff:ff:ff
432: br-119bb63962c2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default
    link/ether 02:42:50:f0:1a:f5 brd ff:ff:ff:ff:ff:ff
434: vethc13cdc3@if433: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master br-119bb63962c2 state UP mode DEFAULT group default
    link/ether fa:8d:be:f7:40:7a brd ff:ff:ff:ff:ff:ff link-netnsid 0
436: veth5a80bfd@if435: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master br-119bb63962c2 state UP mode DEFAULT group default
    link/ether 2e:1b:72:2b:78:4d brd ff:ff:ff:ff:ff:ff link-netnsid 1
[root@jenkins ~]#
[root@jenkins ~]# ip netns exec red ip link
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
[root@jenkins ~]#
[root@jenkins ~]# ip netns exec blue ip link
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
[root@jenkins ~]#
[root@jenkins ~]#
[root@jenkins ~]# arp
Address                  HWtype  HWaddress           Flags Mask            Iface
192.168.38.1             ether   00:50:56:c0:00:08   C                     ens160
_gateway                 ether   00:50:56:fe:a7:e5   C                     ens160
192.168.38.254           ether   00:50:56:fd:35:99   C                     ens160
[root@jenkins ~]#
[root@jenkins ~]#
[root@jenkins ~]# ip netns exec red arp
[root@jenkins ~]#
[root@jenkins ~]# ip netns exec blue arp
[root@jenkins ~]#
[root@jenkins ~]# route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         _gateway        0.0.0.0         UG    100    0        0 ens160
192.168.10.0    0.0.0.0         255.255.255.0   U     0      0        0 br-119bb63962c2
192.168.38.0    0.0.0.0         255.255.255.0   U     100    0        0 ens160
[root@jenkins ~]#
[root@jenkins ~]# ip netns exec red route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
[root@jenkins ~]#
[root@jenkins ~]# ip netns exec blue route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
[root@jenkins ~]#
[root@jenkins ~]#

ip netns add red
ip netns add blue
ip netns
ip link
ip netns exec red ip link
ip netns exec blue ip link
arp
ip netns exec red arp
ip netns exec blue arp
route
ip netns exec red route
ip netns exec blue route
ip link add veth-red type veth peer name veth-blue
ip link set veth-red netns red
ip link add veth-blue type veth peer name veth-red
ip link set veth-blue netns blue
ip -n red addr 192.168.15.1 dev veth-red
ip -n red addr add 192.168.15.1 dev veth-red
ip -n blue addr add 192.168.15.2 dev veth-blue
ip -n red link set veth-red up
ip -n blue link set veth-blue up
ip netns exec red ping 192.168.15.2


[root@jenkins ~]# ip -n red link del veth-red
[root@jenkins ~]#
[root@jenkins ~]# ip link add v-net-0 type bridge
[root@jenkins ~]# ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: ens160: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP mode DEFAULT group default qlen 1000
    link/ether 00:0c:29:c9:f1:cd brd ff:ff:ff:ff:ff:ff
3: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN mode DEFAULT group default qlen 1000
    link/ether 52:54:00:52:5e:0a brd ff:ff:ff:ff:ff:ff
4: virbr0-nic: <BROADCAST,MULTICAST> mtu 1500 qdisc fq_codel state DOWN mode DEFAULT group default qlen 1000
    link/ether 52:54:00:52:5e:0a brd ff:ff:ff:ff:ff:ff
5: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN mode DEFAULT group default
    link/ether 02:42:11:86:51:55 brd ff:ff:ff:ff:ff:ff
432: br-119bb63962c2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default
    link/ether 02:42:50:f0:1a:f5 brd ff:ff:ff:ff:ff:ff
434: vethc13cdc3@if433: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master br-119bb63962c2 state UP mode DEFAULT group default
    link/ether fa:8d:be:f7:40:7a brd ff:ff:ff:ff:ff:ff link-netnsid 0
436: veth5a80bfd@if435: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master br-119bb63962c2 state UP mode DEFAULT group default
    link/ether 2e:1b:72:2b:78:4d brd ff:ff:ff:ff:ff:ff link-netnsid 1
439: v-net-0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether be:17:8f:0c:d9:b7 brd ff:ff:ff:ff:ff:ff
[root@jenkins ~]#
[root@jenkins ~]#
[root@jenkins ~]# ip link add veth-red type veth peer name veth-red-br
[root@jenkins ~]#
[root@jenkins ~]# ip link add veth-blue type veth peer name veth-blue-br
[root@jenkins ~]#
[root@jenkins ~]#
[root@jenkins ~]# ip link set veth-red netns red
[root@jenkins ~]# ip link set veth-red-br master v-net-0
[root@jenkins ~]# ip link set veth-blue netns blue
[root@jenkins ~]# ip link set veth-blue-br master v-net-0
[root@jenkins ~]#
[root@jenkins ~]#
[root@jenkins ~]# ip -n red addr add 192.168.15.1 dev veth-red
[root@jenkins ~]# ip -n blue addr add 192.168.15.2 dev veth-blue
[root@jenkins ~]#
[root@jenkins ~]#
[root@jenkins ~]# ip -n red link set veth-red up
[root@jenkins ~]# ip -n blue link set veth-blue up
[root@jenkins ~]# ping 192.168.15.1
PING 192.168.15.1 (192.168.15.1) 56(84) bytes of data.

--- 192.168.15.1 ping statistics ---
3 packets transmitted, 0 received, 100% packet loss, time 2071ms

[root@jenkins ~]#
[root@jenkins ~]# ip addr add 192.168.15.5/24 dev v-net-0
[root@jenkins ~]# ping 192.168.15.1
PING 192.168.15.1 (192.168.15.1) 56(84) bytes of data.

--- 192.168.15.1 ping statistics ---
4 packets transmitted, 0 received, 100% packet loss, time 3058ms

```

![alt text](image-183.png)

![alt text](image-184.png)

![alt text](image-185.png)

![alt text](image-186.png)

![alt text](image-187.png)

![alt text](image-188.png)

![alt text](image-189.png)

![alt text](image-190.png)

![alt text](image-191.png)

![alt text](image-192.png)

![alt text](image-193.png)

![alt text](image-194.png)

![alt text](image-195.png)

![alt text](image-196.png)

![alt text](image-197.png)

![alt text](image-198.png)

![alt text](image-199.png)

![alt text](image-200.png)

![alt text](image-201.png)

![alt text](image-202.png)

![alt text](image-203.png)

![alt text](image-204.png)

![alt text](image-205.png)

![alt text](image-206.png)

![alt text](image-207.png)

![alt text](image-208.png)

![alt text](image-209.png)

![alt text](image-210.png)

![alt text](image-211.png)

**Networking Deep Dive - Docker:**

```bash
docker run --network none nginx
docker run --network host nginx
docker run --network bridge nginx  == docker run  nginx

```

![alt text](image-212.png)

![alt text](image-213.png)

![alt text](image-214.png)

![alt text](image-215.png)

![alt text](image-216.png)

![alt text](image-217.png)

![alt text](image-218.png)

![alt text](image-219.png)

![alt text](image-220.png)

![alt text](image-221.png)

![alt text](image-222.png)

![alt text](image-223.png)

![alt text](image-224.png)

![alt text](image-225.png)

**Docker Storage:**

```bash
[root@jenkins ~]# docker volume  ls
DRIVER    VOLUME NAME
[root@jenkins ~]#
[root@jenkins ~]# docker volume  create testvol
testvol
[root@jenkins ~]# docker volume  ls
DRIVER    VOLUME NAME
local     testvol
[root@jenkins ~]# docker container run -itd --name test -v testvol:/inside_container centos:7
15f1da7f4386a5afad98cb87780569e2509b8fcc3b4377dcdf2c841cc87c88c6
[root@jenkins ~]# docker volume ls
DRIVER    VOLUME NAME
local     testvol
[root@jenkins ~]# docker exec -it test /bin/bash
[root@15f1da7f4386 /]# ls /inside_container/
[root@15f1da7f4386 /]# touch inside_container/from_container
[root@15f1da7f4386 /]# ls /inside_container/
from_container
[root@15f1da7f4386 /]# df -h | grep -i insi
/dev/mapper/cl_192-root   46G   18G   28G  39% /inside_container
[root@15f1da7f4386 /]# exit
exit
[root@jenkins ~]# docker volume ls
DRIVER    VOLUME NAME
local     testvol
[root@jenkins ~]# docker volume  inspect testvol
[
    {
        "CreatedAt": "2025-03-10T14:31:08+02:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/testvol/_data",
        "Name": "testvol",
        "Options": null,
        "Scope": "local"
    }
]
[root@jenkins ~]# ls /var/lib/docker/volumes/testvol/_data
from_container
[root@jenkins ~]# touch /var/lib/docker/volumes/testvol/_data/form_host
[root@jenkins ~]#
[root@jenkins ~]# docker exec -it test /bin/bash
[root@15f1da7f4386 /]# ls /inside_container/
form_host  from_container
[root@15f1da7f4386 /]#


# delete container and create another one with binding method
[root@jenkins ~]# docker stop test
test
[root@jenkins ~]#
[root@jenkins ~]# docker rm test
test
[root@jenkins ~]# docker container run -itd --name test-again --mount source=testvol,destination=/inside_container centos:7
c5ffa0757a60e595d998b3b5caabf1982abdd81aae743b64b26862a82119d932
[root@jenkins ~]#
[root@jenkins ~]#
[root@jenkins ~]# ls -ltr /var/lib/docker/volumes/testvol/_data
total 0
-rw-r--r-- 1 root root 0 Mar 10 14:33 from_container
-rw-r--r-- 1 root root 0 Mar 10 14:34 form_host
[root@jenkins ~]#
# you will find same data
[root@jenkins ~]# docker exec -it test-again ls /inside_container
form_host  from_container

# if you tried to delete the volume , will refuse as another container using it, so stop container first then delete
[root@jenkins ~]# docker ps
CONTAINER ID   IMAGE      COMMAND       CREATED         STATUS         PORTS     NAMES
c5ffa0757a60   centos:7   "/bin/bash"   3 minutes ago   Up 3 minutes             test-again
[root@jenkins ~]#
[root@jenkins ~]#
[root@jenkins ~]# docker volume rm testvol
Error response from daemon: remove testvol: volume is in use - [c5ffa0757a60e595d998b3b5caabf1982abdd81aae743b64b26862a82119d932]
[root@jenkins ~]# docker stop test-again ; docker rm test-again
test-again
test-again
[root@jenkins ~]#
[root@jenkins ~]# docker volume rm testvol
testvol
[root@jenkins ~]# docker volume ls
DRIVER    VOLUME NAME
[root@jenkins ~]# docker volume prune
WARNING! This will remove anonymous local volumes not used by at least one container.
Are you sure you want to continue? [y/N] y
Total reclaimed space: 0B
[root@jenkins ~]#

# default for creating a volume is read write 
[root@jenkins ~]# docker container run -itd --name volumerw --mount source=data_vol,destination=/inside_container centos:7
1db8d4ee200a396e2293bbe55fcc355f771de717c7d13044ba77953712c07885
[root@jenkins ~]#
[root@jenkins ~]# docker volume ls
DRIVER    VOLUME NAME
local     data_vol

[root@jenkins ~]# docker inspect volumerw
        "Mounts": [
            {
                "Type": "volume",
                "Name": "data_vol",
                "Source": "/var/lib/docker/volumes/data_vol/_data",
                "Destination": "/inside_container",
                "Driver": "local",
                "Mode": "z",
                "RW": true,  ## <<<>>>
                "Propagation": ""
            }
        ],

# here to create a read only conatiner to access volume (volumero)
[root@jenkins ~]# docker container run -itd --name volumero --mount source=data_vol,destination=/inside_container,readonly centos:7
aed88e3407ffe38e746c7897d39e945b19a76e0b0946c69fffabd48eed2dab1e
[root@jenkins ~]# docker inspect volumero

            "Mounts": [
                {
                    "Type": "volume",
                    "Source": "data_vol",
                    "Target": "/inside_container",
                    "ReadOnly": true  ## <<<>>>
                }

## the bind mount mode, you should create the dir first
[root@jenkins ~]# docker container run -itd --name bindmount --mount type=bind,source=/data,destination=/inside_container  centos:7
docker: Error response from daemon: invalid mount config for type "bind": bind source path does not exist: /data.
See 'docker run --help'.
[root@jenkins ~]#
[root@jenkins ~]# mkdir /data
[root@jenkins ~]#
[root@jenkins ~]# docker container run -itd --name bindmount --mount type=bind,source=/data,destination=/inside_container  centos:7
231511f08236c8b7f5bbc92e30489a0d8a0f326044315d15c5f46981acffeaf1
[root@jenkins ~]#
[root@jenkins ~]# docker exec -it bindmount /bin/bash
[root@231511f08236 /]# df -h | grep -i insid
/dev/mapper/cl_192-root   46G   18G   28G  39% /inside_container
[root@231511f08236 /]#

```

![alt text](image-226.png)

![alt text](image-227.png)

![alt text](image-228.png)

**Docker Compose:**

```bash

docker run -d --name redis redis
docker run -d --name db postgres
docker run -d --name vote -p 5000:80 --link redis:redis voting-app
docker run -d --name vote -p 5001:80 --link db:db result-app
docker run -d --name worker --link redis:redis --link db:db worker

# first docker compose file

redis:
  image: redis
db:
  image: postgre:9.4
vote:
  image: voting-app
  ports:
    - 5000:80
  links:
    - redis
result:
  image: result-app
  ports:
    - 5001:80
  links:
    - db
worker:
  image: worker
  links:
 - redis
 - db

# build the customized images
redis:
  image: redis
db:
  image: postgre:9.4
vote:
  build: ./vote
  ports:
    - 5000:80
  links:
    - redis
result:
  build: ./result
  ports:
    - 5001:80
  links:
    - db
worker:
  build: ./worker
  links:
 - redis
 - db


# docker compose with v2
# build the customized images
redis:
  image: redis
  networks:
    - backend
db:
  image: postgre:9.4
  networks:
    - backend
vote:
  build: ./vote
  networks:
    - frontend
    - backend
result:
  build: ./result
  networks:
    - frontend
    - backend
worker:
  build: ./worker
  networks:
    - frontend
    - backend


```

![alt text](image-229.png)

![alt text](image-230.png)

![alt text](image-231.png)

![alt text](image-232.png)

![alt text](image-233.png)

![alt text](image-234.png)

![alt text](image-235.png)

![alt text](image-236.png)

![alt text](image-237.png)

![alt text](image-238.png)

![alt text](image-239.png)

![alt text](image-240.png)

![alt text](image-241.png)

![alt text](image-242.png)

![alt text](image-243.png)

![alt text](image-244.png)

![alt text](image-245.png)

**Docker Compose Example for Voting Application :**

## Resources

- kodeKloud: <https://learn.kodekloud.com/user/courses/docker-certified-associate-exam-course>
