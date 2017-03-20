## Docker setup for "multi-ruby" application development

### Start here...

https://docs.docker.com/docker-for-mac/

... to install Docker and to get an overview of available features and commands. You might want to remove a previous Docker installation on your machine, if it exists, as Docker would run native on your Mac now, which makes it run much faster.

### Then read the documentation for Docker Compose

https://docs.docker.com/compose/overview/

### Caching your github credentials

Some gems in Gemfile get downloaded from github.com using `https` protocol. Create a new `.env` in the docker repo directory and add the following env variables to it:

       COMPOSE_PROJECT_NAME=sherbet
       GITHUB_USERNAME=your_github_username
       GITHUB_PASSWORD=your_github_password

The `.env` file is in the `.gitignore` file, so it won't get exposed.

### Launching apps in docker

       # Assuming your sherbet repositories are under ~/workspace/, i.e.
       # ~/workspace/product-catalog
       # ~/workspace/stallone
       # ~/workspace/ottweb
       # etc.
       $ cd ~/workspace
       $ git clone https://github.com/sherbet/docker.git
       $ cd docker

You could get all the applications up, from building images to running containers, by just running the following command:

       $ docker-compose up

The `docker-compose up` command would do the following, based on instructions in `docker-compose.yml`:
 - build images for all pieces of the application,
 - create a Docker network called `sherbet_default`,
 - spin up containers based on the images,
 - link all containers together.

However, if you get any errors due to latencies, etc., it would be harder to troubleshoot. So, start by building individual images instead:

       $ docker-compose build redis
       $ docker-compose build db
       $ docker-compose build dbstore
       $ docker-compose build base             # the base image for the following ruby images
       $ docker-compose build ruby-1.9.3-p547  # the base image for the stallone image
       $ docker-compose build ruby-2.1.1       # the base image for the ottweb image
       $ docker-compose build ruby-2.2.2       # the base image for product-catalog image

Building images for the first time might take 10-15 minutes. Your code in `../ottweb`, `../stallone` and `../product-catalog` is not part of the images. It's just being mounted into containers when they get created. So, no need to rebuild any image, unless their respective Dockerfile was changed. Also, unless you delete all containers with `docker-compose down`, you don't need to spin them again. Just use `docker-compose stop` and `docker-compose start` for subsequent restarts of the apps in your Docker environment. Then attach to the logs with `docker-compose logs -f`.

#### Default configuration

`docker-compose up` brings up the following containers:

- product-catalog
- stallone
- ottweb
- mysql-server
- dbstore
- redis

The `dbstore` contains the persistent database files and doesn't need to be running, just built. You can access the `mysql-server` container directly with your favorite MySQL tool on port 6306 with user `root` and blank password. NOTE: Your changes to the database would persist only as long as the `dbstore`, `product-catalog` and  `stallone` containers still exist. The database gets dropped, recreated and all migrations run every time you rebuild `product-catalog` and  `stallone` containers.

### Useful commands

#### If you need to rebuild a particular container, i.e. `stallone`, then you could do the following:

      $ docker-compose rm stallone     # removes stallone container
      $ docker-compose build stallone  # rebuilds stallone image
      $ docker-compose up
      # ...or in detached mode
      $ docker-compose up -d
      $ docker-compose down  # this will remove all containers started with the "up" command. You might want to use the "stop" command instead.
      $ docker-compose stop
      $ docker-compose start

#### List and delete images:

      $ docker images
      $ docker rmi -f <image_id>

#### List containers, both running and stopped, and restart them:

      $ docker ps -a
      $ docker-compose start <container_label>

#### Access a running container's shell:

      $ docker exec -it <container_label> /bin/bash

#### Bring up only one application and its dependencies

      $ docker-compose up db
      # then in another terminal window
      $ docker-compose up product-catalog

#### Spin a _new_ standalone container and connect to its shell (make sure the volumes you're mapping actually exist):

      $ docker run -it \
        --name product-catalog \
        --hostname product-catalog \
        --net=sherbet_default \
        -p 3002:3002 \
        -v /Users/svcdgccodev/workspace/product-catalog/:/product-catalog/ \
        -v /Users/svcdgccodev/workspace/docker/product-catalog/database.yml:/product-catalog/config/database.yml \
        sherbet/product-catalog \
        /bin/bash

### List of aliases you might want to create in your ~/.bash_profile

        alias cdock='cd ~/workspace/docker'
        alias di='docker images'
        alias dlsv='docker volume ls -f dangling=true'
        alias drmi='docker rmi -f'
        alias drm='docker rm -f'
        alias drm-all='docker rm $(docker ps -a -q)'
        alias drmn='docker network rm'
        alias drmv='docker volume rm $(docker volume ls -qf dangling=true)'
        alias dps='docker ps -a'
        alias dcp='docker cp'
        alias dstop='docker stop'

### Docker Cheat Sheet

https://github.com/wsargent/docker-cheat-sheet


