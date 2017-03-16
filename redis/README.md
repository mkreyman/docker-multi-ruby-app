## Official dockerfile for redis
# https://github.com/docker-library/redis/tree/master/3.2/alpine

To build:

	   $ docker build -f ./redis/Dockerfile -t sherbet/redis ./redis

To run:

	   $ docker run -d -p 6379:6379 redis

To test:

     $ telnet localhost 6379
     Trying 127.0.0.1...
     Connected to localhost.
     Escape character is '^]'



