docker hub


create docker iec2 isntance in aws account
copy the public ip and login to supperputty with the public ip and run the below commands

sudo su - (become  aroot in ec2-user)
once you run the above sudo command you will be redirect into root user.

yum install -y yum-utils

to setup the docker repository
sudo yum-config-manager --add-repo https://download.docker.com/linux/rhel/docker-ce.repo

to install the docker docker engine
sudo yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

to start the docker engine
systemctl start docker

to check docker status
systemctl status docker

to enable docker engine 
systemctl enable docker 

to come out to root user 
exit
docker ps (you will get eroor because ec2 user dont have docker installation), then

NOTE:
docker image-->>static file
docker container-->> running instances of image is called container
install docker-->> it will create a group called docker.
"users who are in docker group or root user can only run docker commands"

so to become docker user give the permission to that particular user usebelow command
sudo usermod -aG docker <user-name>

ex: sudo usermod -aG docker ec2-user 

refresh the ec2 termonal once and re login 

check you are able to accesss or not like user 
docker ps -->>it will show running containers 
docker ps -a -->> to check all running container
to check the docker images 
docker images

to pull docker images from docker hub
docker pull <image-name>
ex: docker images nginx

to create docker container
docker create <image>:tag
ex: docker create nginx:1.0.0

to start docker container
docker start <container id>
ex: docker start 89573106f3fd

to stop running container
docker stop <containerid>

to delete the running containers
docker rm <containerid>
if you get eroor anything while removeing time 
docker rm -f <containerid>

to delete the docker image
docker rmi <imageid>

to delete all docker images at at time docker rmi 'docker images -a -q"

to delete all docker container at at time 
docker rm 'docker ps -a -q"

how to run docker run 
docker run <nginx>
how to docker run command in background
docker run -d <nginx>

docker run = docker pull + docker craete + docker run

how can you access docker container from internet?
by enabling the port, we need to open host port that can redirect traffic

docker run -d -p <host-port>:<container-port> nginx
ex: docker run -d -p 9000:80 nginx

to get the full deatsils of that particular id
docker inspect <containerid>
docker inspect <imageid>

how to get the terminal access of running container?
docker exec -it <containerid> bash
ex: docker exec -it aeaa6725b4e8 bash
 you will moved to bash terminal
then exit

docker logs-->> to check docker logs

Dockerfile:
it is declarative way of creating custom images
keep all the instruction a file to dockerfile and build the image.

FROM:
from should be teh first instructions to represent base os
 dockerfile:
   FROM almalinux:9

push the code into central repo
then got ec2 user clone the repository
and go to the paricular folder
ex: cd FROM/
 ls -l 
 docker builld -t <image-name>:version .
 docker build -t from:1.0 .
 docker images

RUN: 
we will use RUN instuction to install packages and configure them
execute at the time of image creation.
dockerfile: 
  FROM almalinux:9 
  RUN dnf install nginx -y

git push
git pull 
cd RUN/
docker builld -t <image-name>:version .
docker build -t run:1.0 .
docker images

CMD:
this instruction will run at the time of container creation.
systemctl start backend -->> then only you can access backend application
whatever the instructiobs you give in image to run as container, should run infinite times

dockerfile:
  FROM run:1.0
  RUN dnf install nginx -y
  CMD ["nginx","-g","daemon off;"]

git push
git pull 
cd CMD/
docker builld -t <image-name>:version .
docker build -t cmd:1.0 .
docker images
docker run -d -p <host-port>:<container-port> cmd:1.0

container port should be 80
ex:docker run -d -p 8081:80 cmd:1.0
docke  ps

NOTE: systemctl command will not work containers and images
RUN vs CMD:
RUN: executes at build time image creation time
CMD: only executes at the time of container creaion that is docker run.

LABEL:
docker file:
  FROM almalinux:9
  LABEL author="PAVAN" \
  course="Docker" \
  discription="docker practice"

git push
git pull 
cd LABEL/
ls-l 
docker builld -t <image-name>:version .
ex:docker build -t label:1.0 .
docker images

EXPOSE:
expose instrcutions will let the users know what are the ports used by image/container
dockrfile: 
FROM almalinux:9
EXPOSE 80

git push
git pull 
cd LABEL/
ls-l 
docker builld -t <image-name>:version .
ex:docker build -t expose:1.0 .
docker images

How can you push the image into docker-hub:
docker push url/username/image-name:tag
ex: docker push <nexus-url>/pavan93v/expose:1.0

COPY vs ADD:
COPY and ADD both are to copy the files from workspace to docker image but copy had to have advantages

1. it can directly download content from internet into the image.
2. it can directly untart the files into the image.

ENTRYPOINT:
  dockerfile:
  FROM almalinux:9
  CMD [ "ping", "-c5", "google.com" ]

add the file into dockerfile main repo
git add .
after adding the file commit the file then push the same file into docker main repo 
git commit -m "commit msg"
git push origin main

then clone teh repository into aws 
git clone "repo path"
goto docker path folder 
cd <foldername>
the goto entrypoint path and give command 
docker build -t entrypoint:1.0 .
docker ps
docker images
what is instruction to run the docker image?
docker run -d entrypoint:1.0

CMD vsENTRYPOINT:
you can mix CMD and ENTRYPOINT for better results
CMD is used to supply defauls arguments to ENTRYPOINT, we can always override default args from runtime.

USER:
  dockerfile:
   FROM almalinux:9
   CMD ["sleep","100"]

git pull "repo path"
goto docker path folder 
cd <foldername>
the goto entrypoint path and give command 
docker build -t user:1.0 .
docker ps
docker images

docker run -d user:1.0
docker exec -it <container-id> bash

you should not let containers running with root access

whats are the best practices of docker?
the best practices you should not let docker container to run to the root access.

FROM almalinux:9
RUN adduser expense
USER walet
CMD ["sleep","100"]

git pull "repo path"
goto docker path folder 
cd <foldername>
the goto entrypoint path and give command 
docker build -t user:1.0 .
docker ps
docker images

docker run -d user:1.0

WORKDIR:
 Dockerfile:
   FROM almalinux:9
   RUN mkdir /app
   WORKDIR /app
   #setup the pwd
   RUN pwd
   CMD[ "sleep","100" ]

git pull "repo path"
goto docker path folder 
cd <foldername>
the goto entrypoint path and give command 
docker build -t foldername:1.0 .
docker ps
docker images

docker run -d foldername:1.0

ARG:
 Dockerfile:
   ARG version
   FROM almalinux:${version}
   RUN cat/etc/*release

goto docker path folder 
cd <foldername>
the goto entrypoint path and give command 
docker build -t foldername:1.0 . 
#if above command failed give below command like this
docker build -t arg:v1 --build-arg version=8 .
docker ps
docker images

docker run -d foldername:1.0

1. ARG can be first instructionin only one case, it can be used to supply the version for FROM instruction
2. you can have args in dockerfile, you can supply the values through command through option --build-args
3. you can always have default values to arg and override if required

ENV vs ARG:
1. ENV variables can be accessed both at the time build time and containers
2. ARG instruction can only be accessed at the time of build or image 

how can i access arg inside container?

ONBUILD:
 Dockerfile:
   FROM almalinux:9
   RUN dnf install nginx -y
   RUN rm -rf /usr/share/nginx/html.index.html
   ONBUILD COPY index.html /usr/share/nginx/html.index.html
   CMD [ "nginx","-g","daemon off;" ]

goto docker path folder 
cd <foldername>
the goto entrypoint path and give command 
docker build -t foldername:1.0 . 
docker ps
docker images

docker run -d foldername:1.0   

This is useful as a trigger, if some one is trying to use your image.
you can force them to keep somefiles in their workspace or some configuration

EXPENSE:
backend:
ec2-user) curl https://raw.githubusercontent.com/PavanV93/expense-docker/main/mysql/install%20docker.sh | sudo bash
logot and login again
sudo su -
lsblk

sudo growpart /dev/nvme0n1 4

sudo lvextend -l +50%FREE /dev/RootVG/rootVol

sudo lvextend -l +50%FREE /dev/RootVG/varVol

Frontend:

Docker compose:


