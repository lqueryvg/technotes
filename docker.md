# Docker

## Commands
```
sudo systemctl start docker
usermod -aG docker john     # to run docker as non-root user
docker info
docker run hello-world
docker run docker/whalesay cowsay boo
docker images                   # image list
docker rmi {image}              # image remove
docker build -t docker-whale .  # build image called docker-whale
docker run -it busybox sh       # interactive shell (-i = interact, -t = pty)
docker ps                       # currently running containers
docker ps -a                    # including those which have stopped
docker ps --no-trunc            # don't truncate output
docker ps --format '{{.ID}} {{.Command}}'   # only show certain fields
docker kill {id}
docker exec -it {id} bash       # attach command in running container
docker inspect {image}          # details
docker port {container}
docker run -d -p 9090:80 -t nginx   # to connect to 9090 on local machine

docker run -v $(pwd):/mnt -it ubuntu bash   # map/mount dir into container

docker rm $(docker ps -a -f status=exited -q)     # tidy exited containers
docker rmi $(docker images -a -q)                 # tidy (does not delete in-use images)
                                                  # above will tidy overlay dirs too
docker rmi $(docker images -f dangling=true -q)   # tidy dangling images

docker inspect --format='{{.Id}} {{.Parent}}' \
 $(docker images --filter since=$id -q)          # list child containers
docker build --no-cache .


docker login {some-registry} -u {user} -p {password}
image=my_image
docker build -t $image .
docker tag newimage {my_registry}/{some_path}/${image}:latest
docker push {my_registry}/{some_path}/${image}:latest
```

# Docker Compose

```
# in same dir as docker-compose.yml

docker-compose ps
```

# Discovering docker networking
```
docker network ls               # list nets
docker run --net=host ...       # share host interfaces
docker run --net=bridge ...     # container gets it's own interface and ip
docker network inspect bridge   # shows containers and ips
brctl show
brctl showmacs docker0   # is local yes = mac is on interface on docker host
                         # is local no  = mac learnt from connected container
grep . /sys/class/net/docker0/brif/veth*/port_no
```

## Commit

```
exit container
docker ps -a                    # get {id} of exited container
docker commit {id} my/myname    # commit your changes to new image
docker run -it me/myname        # carry on where you left off
```

## Notes
- container ids abbreviate to anything unique
- container primary process pid=1
- container exits when primary process dies

## Dockerfile

#### CMD
- default command for `docker run`
- overridden when `docker run` is given a command
- string syntax gets prepended with `sh -c`
- use array syntax to be precise
- should only be one; if multiple, last one wins

#### ENTRYPOINT
- base command which precedes CMD
- override with `docker run --entrypoint`

#### WORKDIR
- set dir for next `RUN`, `CMD` or `ENTRYPOINT`
- can be used multiple times override with `-w`

#### ENV
- set environment variables
- e.g. `ENV VAR1 value`
- persists into containers created from image
- see also `-e` flag

#### USER
- specify user the image runs as
- see also `-u` flag
- default is root

#### VOLUME
- add volumes to container
- changes are made directly
- not affected when you update an image
- persist until no containers use them
- share data between containers
- e.g. source code, database

#### ADD
- copies files into container with tar & http support
- trailing `/` on target specifies source is a directory

#### COPY
- like add but no support for tar or http

#### ONBUILD
- execute trigger when this image is used as base for of another


#### ARG
defines a variable for user can to pass with `build` with optional default
```
ARG DEBIAN_FRONTEND=noninteractive
```

# Filesystem types

# Device mapper
- used by default when AUFS is not available (e.g. on Centos 7)

# AUFS
- AUFS CoW works at file level => small changes to large files = slower
- search order through layer = top to bottom => slower for files at bottom
- delete places "whiteout" file in top layer

# Cache miss
Failure to use a cached image, because `docker build` thinks
the next Dockerfile directive will cause the image to change.
