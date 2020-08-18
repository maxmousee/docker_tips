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