---
title: Alpine distro and installation of Anaconda 
tags: devops
---

If you are like me, and like the [Alpine](https://alpinelinux.org/) distro for containers and also would like to do Python stuff with Anaconda, the solution is to add the glibc compatibility layer in Alpine.

apk --no-cache add ca-certificates wget

wget -q -O /etc/apk/keys/sgerrand.rsa.pub https://alpine-pkgs.sgerrand.com/sgerrand.rsa.pub

wget https://github.com/sgerrand/alpine-pkg-glibc/releases/download/2.28-r0/glibc-2.28-r0.apk

apk add glibc-2.28-r0.apk
