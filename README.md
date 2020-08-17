# A collection of Docker tips 🐳

## Create a Non-Root User inside a Dockerfile


## Dockerfile
#...
## create a non-root user with restricted privileges
RUN useradd --user-group --shell /bin/false newuser

## switch to newuser
USER newuser

# run next steps as new user
#...

-----------------

## How to Retrieve the process_id of a running container instance

### CLI usage example:

docker inspect -f '{{.State.Pid}}' <container_id>

### you can also create an alias for that:

alias .pid="docker inspect -f '{{.State.Pid}}' $1"

and use it like this:

.pid 123