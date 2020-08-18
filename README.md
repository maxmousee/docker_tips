# A collection of Docker tips 🐳

## Create a Non-Root User inside a Dockerfile


### Dockerfile
#...
### create a non-root user with restricted privileges
RUN useradd --user-group --shell /bin/false newuser

### switch to newuser
USER newuser

### run next steps as new user
#...

-----------------

## How to Retrieve the process_id of a running container instance

### CLI usage example:

docker inspect -f '{{.State.Pid}}' <container_id>

### you can also create an alias for that:

alias .pid="docker inspect -f '{{.State.Pid}}' $1"

and use it like this:

.pid 123

-----------------

## Delete all not running containers

docker container rm $(docker ps -a -q)

-----------------

## Prune what we no longer need ✂️

### Remove dangling images

docker image prune

### Remove all images not used by existing containers

docker image prune -a

### Remove only images created in the last hour

docker image prune -a --filter "until=1h"

### Remove all stopped containers

docker container prune

### Remove all volumes not used by at least one container

docker volume prune

### Remove all networks not used by at least one container

docker network prune

### Docker system prune will remove
### - all stopped containers
### - all networks not used by at least one container
### - all dangling images
### - all build cache

docker system prune

### to bypass the prompt, use -f or --force

-----------------

## Check the environment variables of an image

docker run <IMAGE_ID> env

If you have problems, try also

docker exec <CONTAINER_ID> env

-----------------

## Docker inspect GO template

'docker inspect ' and other commands are useful but quite verbose. 

Docker uses Go templates to manipulate the output format.

For example, to get the IP of a running container:

docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <CONTAINER_ID>

-----------------

## Docker networks and attachable network

[Networks] & attachable flag

NONE: disable networking

HOST: removes containers-host isolation

BRIDGE: connects standalone containers

MACVLAN: assign a MAC address to a container that appears as a physical device

OVERLAY: connect multiple daemons, for swarm

docker network create -d overlay --attachable overlay-network

-----------------

## Docker top - port - kill

### 𝐝𝐨𝐜𝐤𝐞𝐫 𝐭𝐨𝐩:  Displays the container's running processes
$ docker top <container>

### 𝐝𝐨𝐜𝐤𝐞𝐫 𝐩𝐨𝐫𝐭: Lists container's port mappings
$ docker port <container>

### 𝐝𝐨𝐜𝐤𝐞𝐫 𝐤𝐢𝐥𝐥: Kills the process! (not ideal)
$ docker kill <container>

-----------------

## Docker image layers

To check the image layers:

$ 𝗱𝗼𝗰𝗸𝗲𝗿 𝗵𝗶𝘀𝘁𝗼𝗿𝘆 <𝗜𝗠𝗔𝗚𝗘_𝗜𝗗>

-----------------

## Docker diff

Handy command to check any changes to:
-files
-directories 

on the container’s filesystem

A: added file or directory
C: changed file or directory was 
D: deleted file or directory

Usage:

$ docker diff <container_ID/container_name>

-----------------

## Docker PS options

docker container ps [OPTIONS]
(you can use the shortcut: "docker ps [OPTIONS]")

-a: show all containers, even the stopped ones
-q: only show IDS
-l:  show the latest container
-s: show the size
-f: filter output
--format: show using GO template (example below)

#### Show all containers, even stopped ones, that has 'nginx' as ancestor

docker ps -a --filter "ancestor=nginx"

#### Format output using GO template with just name and image

docker ps --format "table {{.Names}}\t{{.Image}}

-----------------

## Docker Commit

Command:
docker commit [OPTIONS] <container> <new_image>

Result:
Create a new image with the changes on container

Options:
-a: author
-c: apply Dockerfile instructions
-m: commit message 

Note: No data in volumes inside the container will be saved!

#### Launch a Debian container, named debian1 based on a Debian image

$ docker run --name debian1 -it debian bin/bash

#### Inside the container, update apt-get
[from inside the container]: apt-get update

#### Let's install nmap into this container
[from inside the container]: apt-get install nmap

#### Exit the container
[from inside the container]: exit

#### Commit the current container into a new image
$ docker commit debian1 debiansaved

#### If we start a new debian container based on the new image, we already have nmap installed
$ docker run --name debianmod -it debiansaved bin/bash

-----------------

## Docker Jenkins Official Image

JENKINS: free, open source automation server.

You can just start using it using its official docker image"
$ docker run -p 8080:8080 -p 50000:50000 jenkins/jenkins:lts

To remove, just remove the container and the image:
$ docker rmi -f jenkins/jenkins:lts

Documentation:

https://github.com/jenkinsci/docker

-----------------

## Docker PostgreSQL and PSQL

In 2 commands, run a Postgres container and get into PSQL 

$ docker run --name testpostgres -p 5432:5432 -e POSTGRES_USER=admin -e POSTGRES_PASSWORD=admin -e POSTGRES_DB=testdb -d postgres:latest
 
$ docker exec -it postgresql psql -d testdb -U admin

-----------------

## Docker Tag

Command:
docker tag <image> <new tag>

Effect:
Create a tag image that refers to the source image

A tag must be: 
- valid ASCII 
- may contain letters, digits, underscores, periods, dashes. 
- may not start with a period or a dash 
- maximum of 128 characters.

Example:

pull busybox image from docker hub

$ docker pull busybox

tag busybox image with a different tag

$ docker tag busybox testtag

check the new tag

$ docker image ls

-----------------

## Docker Volumes

Preferred mechanism for persisting data for Docker containers

- Special directory for containers
- Designed to persist data
- No automatically deleted
- No garbage-collected when no more referenced

To display information:

docker volume inspect <volume>

HOST VOLUMES: 
It is on the Filesystem, and is accessible from the container

NAMED VOLUMES: 
Docker manages the creation, the name is given by the user

ANONYMOUS VOLUMES: 
Docker manages it. Having no name, referring to it can be difficult

-----------------

## Docker Portainer

Simplify container management in Swarm and K8s

$ docker volume create p_data

$ docker run -d -p 8000:8000 -p 9000:9000 --name=prt --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v p_data:/data portainer/portainer

open localhost:9000

-----------------

## Docker Run Options

-d: detached mode
-e: set/override env variable
-i: Keep STDIN open
--name: assign name
--network: define network
-p: publish ports
-P: publish all the ports to the host interfaces
--rm: remove on exit
-t: pseudo-tty
-u: user
-v: volume
-w: default workdir