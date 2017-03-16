# Based on mysql/mysql-server:5.6
# https://hub.docker.com/r/mysql/mysql-server/

## MySQL server 5.6

### To build:

    $ docker-compose build db
    # $ docker build -f ./mysql-server/Dockerfile -t sherbet/mysql-server ./mysql-server

### Start a MySQL Server Instance:

    $ docker run \
        --name mysql-server \
        -e MYSQL_ALLOW_EMPTY_PASSWORD=yes \
        -d --hostname db \
        -p 3306:3306 \
        mysql-server

Alternatively, you could pass `MYSQL_ROOT_PASSWORD=my-secret-pw` instead. Note: choose a host port other than 3306 if you have a locally installed mysql instance, i.e. `-p 6306:3306`.

### Inspecting a container:

    $ docker exec -it mysql-server bash
    [root@79c90b3632aa /]# mysql

### An alternative way to connect to mysql server is via a mysql client container:

Create a docker network:

    $ docker network create sherbet-network

Start an instance:

    $ docker run \
        --name mysql-server \
        --hostname mysql-server \
        --net=sherbet-network \
        -e MYSQL_ALLOW_EMPTY_PASSWORD=yes \
        -d \
        mysql-server

Or the same with a persistent database:

    $ docker run \
        --name mysql-server \
        --hostname mysql-server \
        --net=sherbet-network \
        -p 6306:3306 \
        -e MYSQL_ALLOW_EMPTY_PASSWORD=yes \
        -d \
        -v ~/workspace/docker/dbstore/data:/var/lib/mysql \
        mysql-server

... assuming ~/workspace/docker/dbstore/data directory exists.


Connect to it from a client container (see the README for the mysql client image on how to build it first if you haven't):

    $ docker run -it --rm --net=sherbet-network --volumes-from mysql-server mysql-client mysql -h mysql-server -uroot


### Troubleshooting:

    $ docker exec -it mysql-server bash
