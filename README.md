# A collection of Docker tips 🐳

## Create a Non-Root User inside a Dockerfile


## Dockerfile
#...
## create a non-root user with restricted privileges
RUN useradd --user-group --shell /bin/false newuser

## switch to newuser
USER newuser

## run next steps as new user
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