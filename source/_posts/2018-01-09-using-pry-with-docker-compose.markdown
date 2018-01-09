---
layout: post
title: "Debug Ruby using Pry and Docker compose"
date: 2018-01-09 21:11
comments: false
categories:
---
If you're using Docker and `docker-compose` you probably found that by default you can't debug your Ruby application with the `binding.pry` breakpoint.

However, if you comment out the `command` line for your web container. Then:

```
docker-compose up
```

Open a new tab and run:

```
docker-compose run --service-ports --rm web /the/command/you/commented/out
```

Then you will have interactive access to the command line with `binding.pry`!