---
layout: post
title: minikube with hyperkit hang after machine reboot
image: /img/minikube_logo.png
---

Currently, I have to work on minikube cluster on my Mac. It is ok, but I found it not able to start again after machine reboot (I always forget to run `minikube stop` before reboot, but I think this is a stupid way to fix.). Of course, It can be fixed by :

```bash
minikube delete
```

But this is not something I interested, as I have to initialize all the setup and pull all docker images which take a long time.
This is relating to several issues like this <https://github.com/kubernetes/minikube/issues/1926> and <https://github.com/kubernetes/minikube/issues/2430>.

After some troubeleshoot and googling, I am able to start the minikube wihtout destroy the minikube cluster with the following quick fix:
```bash
#/bin/bash

minikube stop && minikube start -v 10 &

rm -f ~/.minikube/machines/minikube/hyperkit.pid
sudo rm /private/var/db/dhcpd_leases
sleep 60
minikube ssh rm /etc/docker/.keep
minikube ssh sudo systemctl -f stop docker
```

Hope this help, a [gist]<https://gist.githubusercontent.com/bunnyyiu/6de36ccccd0c89d4e1eeb7ad29691900/raw/40eee5b5dd9b71f56d6488d44d449f9df86b5713/fix_minikube.sh> is avialble too.

# References
<https://github.com/jenkins-x/jx/issues/199>
<https://github.com/kubernetes/minikube/issues/1926>
<https://github.com/kubernetes/minikube/issues/2430>
<https://gist.githubusercontent.com/bunnyyiu/6de36ccccd0c89d4e1eeb7ad29691900/raw/40eee5b5dd9b71f56d6488d44d449f9df86b5713/fix_minikube.sh>
