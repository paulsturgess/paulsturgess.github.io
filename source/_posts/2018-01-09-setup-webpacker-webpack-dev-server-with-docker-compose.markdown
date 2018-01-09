---
layout: post
title: "Setup webpacker webpack-dev-server with docker-compose"
date: 2018-01-09 21:29
comments: false
categories:
---
The [rails/webpacker gem](https://github.com/rails/webpacker) is a fantastic way to configure [webpack](https://webpack.js.org/) for your Rails application. However, it has gone through quite a few different configuration options to get it working with Docker.

Initially you could set the host by using command line arguments, but as of version 3.0.2 and [this Pull Request](https://github.com/rails/webpacker/pull/843) the command line argument support was removed in favour of environment variables. By setting various environment variables you can override all dev server settings defined in `config/webpacker.yml`.

So now my `docker-compose.yml` config looks something like this:

```yml
web:
    <<: *app
    command: /src/docker/web.sh
    ports:
      - "3000:3000"
    depends_on:
      - webpack
    environment:
      - WEBPACKER_DEV_SERVER_HOST=webpack

  webpack:
    <<: *app
    command: ./bin/webpack-dev-server
    ports:
      - 3035:3035
    environment:
      - WEBPACKER_DEV_SERVER_HOST=0.0.0.0
```

To install yarn and nodejs I added the following to my Debian based dockerfile:

```
RUN apt-get update && apt-get install apt-transport-https

RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - \
  && echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list \
  && curl -sL https://deb.nodesource.com/setup_8.x | bash -

RUN apt-get update && \
  apt-get install -y \
  yarn
  nodejs
```