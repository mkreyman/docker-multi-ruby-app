FROM ruby:2.2.2
MAINTAINER Mark Kreyman <mark@kreyman.com>

RUN apt-get update -qq && \
    apt-get install -qq -y --no-install-recommends vim \
      autoconf \
      build-essential \
      curl \
      libxml2-dev \
      libxslt-dev \
      libssl-dev \
      openssl \
      libmagick++-dev \
      git && \
    rm -rf /var/lib/apt/lists/*

ENV APP_HOME /product-catalog
RUN mkdir -p $APP_HOME
WORKDIR $APP_HOME
VOLUME $APP_HOME

# Image build arguments for pulling gems from github non-interactively.
ARG github_username
ARG github_password

# How many different ways to fetch a gem from github could you come up with?
RUN git config --global url."https://$github_username:$github_password@github.com/".insteadOf git@github.com:
RUN git config --global url."https://$github_username:$github_password@github.com".insteadOf git://github.com

# Using a custom bundle location here, so we could then use the same image
# for the bundle container to cache all gems.
ENV BUNDLE_GEMFILE=$APP_HOME/Gemfile \
    BUNDLE_JOBS=2 \
    BUNDLE_PATH=/bundle

ADD Gemfile* $APP_HOME/
ADD database.yml $APP_HOME/config/database.yml

RUN gem install bundler && \
    bundle install

# Running bundle install again to catch up on any changes in Gemfile
CMD bundle install && \
    bin/rails s -p 3002 -b 0.0.0.0