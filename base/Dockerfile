FROM ubuntu:14.04
MAINTAINER Mark Kreyman <mark@kreyman.com>

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
      autoconf build-essential libxml2-dev libxslt-dev libssl-dev \
      libcurl4-openssl-dev libdb-dev libmysqlclient-dev libmagick++-dev \
      libyaml-dev libffi-dev libedit-dev openssl ca-certificates \
      curl git unzip vim wget && \
    rm -rf /var/lib/apt/lists/*

# Generate a self-signed key to avoid warnings later
RUN openssl genrsa -des3 -passout pass:x -out server.pass.key 2048
RUN openssl rsa -passin pass:x -in server.pass.key -out server.key
RUN rm server.pass.key
RUN openssl req -new -key server.key -out server.csr -subj '/CN=www.slingtv.com/O=Dish/C=US'
RUN openssl x509 -req -sha256 -days 365 -in server.csr -signkey server.key -out /etc/ssl/certs/ca-certificates.crt
RUN update-ca-certificates