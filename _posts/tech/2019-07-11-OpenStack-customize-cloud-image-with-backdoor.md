---
layout: post
title: ! 'OpenStack customize CloudImage with backdoor #1'
category: tech
tags: tech
keywords: glance, openstack, cloudimage
---

> The topic is divided into 2 parts. This section is meant for Linux.


## Query Qcow2/raw Image info

```
fdisk cosmic-server-cloudimg-amd64.img
blkid cosmic-server-cloudimg-amd64.img
```

## Mount Image

```
guestmount -a cosmic-server-cloudimg-amd64.img -m xx /mnt/xx
sudo losetup -f -P cosmic-server-cloudimg-amd64.img

```

## Customize Image

```
chroot /mnt/xx

virt-customize -a bionic-server-cloudimg-amd64.img --root-password password:yourpassword

ssh-keygen -f /etc/ssh/ssh_host_rsa_key -N '' -t rsa //for ssh into cloud image
```