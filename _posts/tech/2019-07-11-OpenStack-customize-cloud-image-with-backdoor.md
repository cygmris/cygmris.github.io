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

sudo losetup -f -P cosmic-server-cloudimg-amd64.img (Not working fo qcow2 format image cuz it won't parse qcow2 header)

sudo modprobe nbd max_part=8
sudo qemu-nbd -c /dev/nbd0 ubuntu.qcow2
sudo qemu-nbd -d /dev/nbd0

```

## Customize Image

```
chroot /mnt/xx

virt-customize -a bionic-server-cloudimg-amd64.img --root-password password:yourpassword

ssh-keygen -f /etc/ssh/ssh_host_rsa_key -N '' -t rsa //for ssh into cloud image

sudo guestfish --rw -a $IMG_FILE

```

## Cloud Image partition layout

## Shrink qcow2 image and root partition

Some times the cloud image don't have enough space to be customized when hitting huge package via `apt install`. We have to shrink/increze its size to fill our package.

`qemu-img resize $IMG_FILE 4G`

`e2fsck -f -y -v -C 0 '/dev/nbd0p1'`
`resize2fs -p '/dev/nbd0p1'`

```text
GParted 1.0.0

configuration --enable-libparted-dmraid --enable-online-resize

libparted 3.2

========================================

Device:	/dev/nbd0
Model:	Unknown
Serial:	none
Sector size:	512
Total sectors:	8388608
 
Heads:	255
Sectors/track:	63
Cylinders:	522
 
Partition table:	gpt
 
Partition	Type	Start	End	Flags	Partition Name	File System	Label	Mount Point
/dev/nbd0p14	Primary	2048	10239	bios_grub		grub2 core.img		
/dev/nbd0p15	Primary	10240	227327	boot, esp		fat32	UEFI	
/dev/nbd0p1	Primary	227328	4612062			ext4	cloudimg-rootfs	
========================================

Grow /dev/nbd0p1 from 2.09 GiB to 3.89 GiB  00:00:01    ( SUCCESS )
    	
calibrate /dev/nbd0p1  00:00:00    ( SUCCESS )
    	
path: /dev/nbd0p1 (partition)
start: 227328
end: 4612062
size: 4384735 (2.09 GiB)
check file system on /dev/nbd0p1 for errors and (if possible) fix them  00:00:01    ( SUCCESS )
    	
e2fsck -f -y -v -C 0 '/dev/nbd0p1'  00:00:01    ( SUCCESS )
    	
Pass 1: Checking inodes, blocks, and sizes
Pass 2: Checking directory structure
Pass 3: Checking directory connectivity
Pass 4: Checking reference counts
Pass 5: Checking group summary information

88962 inodes used (32.45%, out of 274176)
30 non-contiguous files (0.0%)
53 non-contiguous directories (0.1%)
# of inodes with ind/dind/tind blocks: 0/0/0
Extent depth histogram: 76789/10
543158 blocks used (99.10%, out of 548091)
0 bad blocks
1 large file

64601 regular files
12048 directories
9 character device files
0 block device files
0 fifos
19 links
12295 symbolic links (12146 fast symbolic links)
0 sockets
------------
88972 files
e2fsck 1.45.3 (14-Jul-2019)
grow partition from 2.09 GiB to 3.89 GiB  00:00:00    ( SUCCESS )
    	
old start: 227328
old end: 4612062
old size: 4384735 (2.09 GiB)
new start: 227328
new end: 8386559
new size: 8159232 (3.89 GiB)
grow file system to fill the partition  00:00:00    ( SUCCESS )
    	
resize2fs -p '/dev/nbd0p1'  00:00:00    ( SUCCESS )
    	
Resizing the filesystem on /dev/nbd0p1 to 1019904 (4k) blocks.
The filesystem on /dev/nbd0p1 is now 1019904 (4k) blocks long.

resize2fs 1.45.3 (14-Jul-2019)
```


# My Script


```bash

#!/bin/bash

IMG_FILE=$(pwd)/disco-server-cloudimg-amd64.img
DISTRO=disco
USERNAME=chris
CLOUD_CONFIG=$(pwd)/disco-cloud.cfg
DOCKER_DEPLOYSCRIPT=$(pwd)/docker.sh
DOCKER_MIRRORS='https://mirrors.tuna.tsinghua.edu.cn/docker-ce'
BRANCH="stable" # testing nightly

sudo virt-sysprep -a $IMG_FILE \
--run-command "useradd $USERNAME && usermod -aG sudo $USERNAME"

sudo virt-sysprep -a $IMG_FILE \
--run-command "mkdir -p /home/$USERNAME/.ssh && chown $USERNAME:$USERNAME /home/$USERNAME && chmod 700 /home/$USERNAME/.ssh"


sudo virt-sysprep -a $IMG_FILE \
--ssh-inject chris:file:/home/chris/.ssh/id_rsa.pub


#sudo modprobe nbd max_part=8
#sudo qemu-nbd -c /dev/nbd0 $IMG_FILE
#sudo mkdir /tmp/cloud-target
#sudo mount /dev/nbd0p1 cloud-target
sudo virt-copy-in -a $IMG_FILE $(pwd)/.oh-my-zsh /root/
sudo virt-copy-in -a $IMG_FILE $(pwd)/.zshrc /root/
sudo virt-copy-in -a $IMG_FILE $(pwd)/.oh-my-zsh /home/$USERNAME
sudo virt-copy-in -a $IMG_FILE $(pwd)/.zshrc /home/$USERNAME

sudo mkdir /tmp/cloud-target
sudo guestmount -a $IMG_FILE -m /dev/sda1 /tmp/cloud-target

sudo chroot /tmp/cloud-target /bin/bash <<EOF
sed -i 's/security.ubuntu.com/mirrors.tuna.tsinghua.edu.cn/g' /etc/apt/sources.list
sed -i 's/archive.ubuntu.com/mirrors.tuna.tsinghua.edu.cn/g' /etc/apt/sources.list
sed -i 's/ubuntu/root/g' /root/.zshrc
sed -i 's/ubuntu/${USERNAME}/g' /home/${USERNAME}/.zshrc
chown -R root:root /root/
chown -R ${USERNAME}:${USERNAME} /home/${USERNAME}/
EOF

# sudo umount /dev/nbd0p1
sudo umount /tmp/cloud-target

sudo virt-customize -a $IMG_FILE --root-password password:yourpassword


sudo virt-customize -v -x -a $IMG_FILE \
    --timezone "Asia/Hong_Kong" 

sudo virt-customize -v -x -a $IMG_FILE \
    --update \
    --install vim,bash-completion,wget,curl,telnet,unzip
 
sudo virt-customize -v -x -a $IMG_FILE \
    --update \
    --install zsh,openjdk-8-jdk,maven

sudo virt-customize -v -x -a $IMG_FILE --upload $CLOUD_CONFIG:/etc/cloud/cloud.cfg
sudo virt-customize -v -x -a $IMG_FILE --upload $DOCKER_DEPLOYSCRIPT:/home/$USERNAME/docker.sh




sudo virt-customize -v -x -a $IMG_FILE \
    --run-command "/home/$USERNAME/docker.sh"


```


