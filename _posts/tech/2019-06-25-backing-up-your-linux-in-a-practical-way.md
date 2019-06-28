---
layout: post
title: ! 'backing up your linux distro in a practical way #1'
category: tech
tags: tech
keywords: network bridge,kvm
---

> I've tried Clonezilla,which is cool I felt. But I don't need it at this time.

`rsync` is a better options for incremental backup but I won't talk about it at this time.

`rsync -aAXv /* /path/to/backup/folder --exclude={/dev/*,/proc/*,/sys/*,/tmp/*,/run/*,/mnt/*,/media/*,/lost+found}`

> This section I will take my manjaro linux as demostration.

> Doing this with multi process(pigz) is preffered.

```
time sudo tar -cvf \
    - \
    --exclude=$HOME/.ssh \
    --exclude=$HOME/.m2/* \
    --exclude=$HOME/.gradle/* \
    --exclude=$HOME/.android/* \
    --exclude=$HOME/.cache \
    --exclude=$HOME/.config \
    --exclude=$HOME/.local/share/Trash/ \
    --exclude=$HOME/.nutstore/* \
    --exclude=$HOME/.thunderbird/* \
    --exclude=$HOME/Android/Sdk/system-images/* \
    --exclude=$HOME/backup \
    --exclude=$HOME/baidunetdisk \
    --exclude=$HOME/Downloads/* \
    --exclude=$HOME/Desktop/* \
    --exclude=$HOME/Documents/* \
    --exclude=$HOME/Videos/* \
    --exclude=$HOME/Pictures/* \
    --exclude=$HOME/Music/* \
    --exclude=$HOME/tmp/ \
    --exclude=$HOME/workflow/* \
    --exclude=$HOME/envflow/* \
    --exclude=/chris \
    --exclude=/proc \
    --exclude=/lost+found \
    --exclude=/mnt \
    --exclude=/media \
    --exclude=/sys \
    --exclude=/tmp \
    --exclude=/run \
    --exclude=/var/lib/libvirt/images/* \
    --exclude=/var/lib/docker/* \
    --exclude=/var/cache \
    --exclude=/var/log \
    --exclude=/backup \
    --exclude=/backup_ssd \
    / \
    | pigz -9 -p 4 > manjaro-chris.tgz
```

## data size collector

```
sudo ncdu \
    --exclude=$HOME/.ssh \
    --exclude=$HOME/.m2/* \
    --exclude=$HOME/.gradle/* \
    --exclude=$HOME/.android/* \
    --exclude=$HOME/.cache \
    --exclude=$HOME/.config \
    --exclude=$HOME/.local/share/Trash/ \
    --exclude=$HOME/.nutstore/* \
    --exclude=$HOME/.thunderbird/* \
    --exclude=$HOME/Android/Sdk/system-images/* \
    --exclude=$HOME/backup \
    --exclude=$HOME/baidunetdisk \
    --exclude=$HOME/Downloads/* \
    --exclude=$HOME/Desktop/* \
    --exclude=$HOME/Documents/* \
    --exclude=$HOME/Videos/* \
    --exclude=$HOME/Pictures/* \
    --exclude=$HOME/Music/* \
    --exclude=$HOME/tmp/ \
    --exclude=$HOME/workflow/* \
    --exclude=$HOME/envflow/* \
    --exclude /mnt \
    --exclude /chris \
    --exclude /backup \
    --exclude /backup_ssd \
    --exclude=/proc \
    --exclude=/lost+found \
    --exclude=/mnt \
    --exclude=/media \
    --exclude=/sys \
    --exclude=/tmp \
    --exclude=/run \
    --exclude=/var/lib/libvirt/images/* \
    --exclude=/var/lib/docker/* \
    --exclude=/var/cache \
    --exclude=/var/log \
    --exclude=/backup \
    --exclude=/backup_ssd \
    /

```

## boot only
```
time sudo tar -cvf \
    - \
    /boot \
    | pigz -9 -p 4 > boot.tgz
```


## extract

```
time pigz -dc xx.tgz | tar -C /mnt/xx -xvf - 
```

## result

```
sudo tar -cvf
464.42s user 
118.95s system 
21% cpu 
46:02.59 total
pigz -9 -p 4 > manjaro.tgz  
9408.64s user 
96.50s system 
344% cpu 
46:02.59 total


sudo tar -cvf
212.14s user 
53.62s system 
23% cpu 
18:52.23 total
pigz -9 -p 6 > manjaro-chris.tgz  
5122.10s user 
44.75s system 
456% cpu 
18:52.24 total


```
    

## HOW - restore to bootable system back from extracting of tgz

### Take ubuntu19.10 disco as example, which is my friennds' requirement.

`chroot /tmp/nvmen0pX`
then
`update-grub2`

> ignore all warning and error

or just edit `/boot/grub/grub.cfg`

I am using `gpt` as partition table and EFI as boot manager

so I have to edit `/boot/efi/EFI/grub/grub.cfg` and replace the `UUID` with result came from `blkid` manually.

> If you get any question . Leave me a comment.