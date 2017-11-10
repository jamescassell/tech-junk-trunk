---
layout:     post
title:      Traefik default server catch all rule
date:       2017-11-03 21:00
summary:    How to setup Traefik with a traditional default web server rule
categories: docker
thumbnail: docker
tags:
 - docker
 - proxy
 - traefik
---

Yesterday I found myself testing out [traefik](https://traefik.io/) for the first time after this [forum request piqued my interest](https://discourse.pi-hole.net/t/pi-hole-docker-docker-compose-x86-and-traefik-as-reverse-proxy/5321), mainly as a replacement for my [jwilder/nginx-proxy](https://github.com/jwilder/nginx-proxy) reverse proxy.  In order to block ads with my pi-hole image traefik's reverse proxy needed a critical feature, a defaut site / server...which I found surprisingly not well documented!  So here we are documenting it on my new blog.

[This stack overflow post](https://stackoverflow.com/questions/43171835/how-do-i-set-a-default-host-container-in-traefik-with-the-docker-backend) looked promising at first, but really the poster was reqesting a simple www+non www rule.  I needed default host functionality similar to nginx or apache webservers' concept of default servers: a low priority catch all server that unmatched domain requests will be directed to.

After I gave up googling I turned to the docs.  This is what I ended up trying that worked for me to have a true 'default host' that got rid of the default 404 everything behavior traefik ships with:

```
    # from my docker-compose.yaml service block
    labels:
      - "traefik.frontend.rule=HostRegexp:{catchall:.*}"
      - "traefik.frontend.priority=1"
```

If you don't speak regex `.*` translates to any character for any length.  The HostRegexp just follows traefik's (and go's) [rules for regex][1] which requires this style `{name:<regex>}`

[The priority setting is critical][2]!  Without it all my other docker containers' traefik proxies stopped working and the catchall took top priority.  Priority 1 was the lowest integer that worked for me while other containers had no priority set.  Setting priority to 0 just causes the the catchall regex to become top priority again.


After getting traefik, pi-hole, and another web container plexpy working together I decied to convert all my other containers over and will try runnig traefik as my new simple single host docker home servers' reverse proxy for now.  Hopefully it works out, if it goes well I may start recommending it to others in my docker READMEs.



  [1]: https://docs.traefik.io/basics/#matchers
  [2]: https://docs.traefik.io/basics/#priorities
