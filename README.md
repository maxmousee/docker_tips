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

