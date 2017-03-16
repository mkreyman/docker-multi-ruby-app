FROM ruby:2.1.1
MAINTAINER Mark Kreyman <mark@kreyman.com>

RUN apt-get update -qq && \
    apt-get install -qq -y --no-install-recommends curl && \
    rm -rf /var/lib/apt/lists/*

# clone nvm/node
ENV NODEJS_VERSION v6.9.0
ENV PATH $PATH:~/.nvm/versions/node/${NODEJS_VERSION}/bin:/bin/versions/node/${NODEJS_VERSION}/bin
ENV NVM_NODEJS_ORG_MIRROR http://nodejs.org/dist

RUN git clone https://github.com/creationix/nvm.git ~/.nvm && cd ~/.nvm && git checkout `git describe --abbrev=0 --tags`
RUN . ~/.nvm/nvm.sh && \
    nvm install $NODEJS_VERSION && \
    nvm alias default $NODEJS_VERSION && \
    npm config set registry=https://artifactory.global.dish.com/artifactory/api/npm/npm-remote && \
    npm config set strict-ssl=false

ENV APP_HOME /ottweb
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
ADD .npmrc ~/
RUN npm config list
RUN npm install -g redux react-redux react
RUN gem install bundler && \
    bundle install

# Running bundle install again to catch up on any changes in Gemfile
CMD bundle install && \
    npm install && \
    npm run postinstall && \
    npm run webpack && \
    bin/rails s -p 3000 -b 0.0.0.0

