---
layout: post
title: minikube with hyperkit hang after machine reboot
image: /img/minikube_logo.png
---

Currently, I have to work on minikube cluster on my Mac. It is ok, but I found it not able to start again after machine reboot (I always forget to run `minikube stop` before reboot). Of course, It can be fixed by :

```bash
minikube delete
```

I don't want to initialize all the setup and pull all docker images which take a long time.
This is related to several issues like this <https://github.com/kubernetes/minikube/issues/1926> and <https://github.com/kubernetes/minikube/issues/2430>. After some troubeleshoot and googling, I am able to start the minikube wihtout destroy the minikube cluster with the following quick fix:
```bash
#/bin/bash

minikube stop

sudo rm -f ~/.minikube/machines/minikube/hyperkit.pid
sudo rm /private/var/db/dhcpd_leases

minikube start -v 10 &
sleep 60
minikube ssh "sudo rm -f /etc/docker/.keep"
minikube ssh "sudo killall dockerd"
```

Hope this help, a [gist](https://gist.githubusercontent.com/bunnyyiu/6de36ccccd0c89d4e1eeb7ad29691900/raw/179303889d99652df3bedebe805e6e909ade167c/fix_minikube.sh) is available too.

# References
<https://github.com/jenkins-x/jx/issues/199>
<https://github.com/kubernetes/minikube/issues/1926>
<https://github.com/kubernetes/minikube/issues/2430>
<https://gist.githubusercontent.com/bunnyyiu/6de36ccccd0c89d4e1eeb7ad29691900/raw/179303889d99652df3bedebe805e6e909ade167c/fix_minikube.sh>
