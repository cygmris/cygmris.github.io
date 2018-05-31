---
layout: post
title: make bootable win usb with udf iso
category: tech
tags: tech
keywords: tech,udf,iso,bootable usb
---
## ubuntu make bootable win usb with udf iso

> The key and easily be forgot step is to write a proper boot sector to the usb stick, which can be don from Linux with ms-sys or lilo -M.


ms-sys kili -M

sudo apt install gparted

> With my 16GB of usb,I use gparted to reserved 10gb space for ntfs to store data bigger than 4G and get 4 gb at beggining of disk for our UDF ISO image.

cfdisk /dev/sdb or fdisk /dev/sdb

mkfs.ntfs -f /dev/sdb1

mount -o loop win7.iso/UEFI+legacy_PE.iso /mnt/iso
mount /dev/sdb1 /mnt/usb

cp -r /mnt/iso/* /mnt/usb

Call**sync** to make sure all files are written

Literally we could backup what we've done the that block divce (our usb) to a raw image file for further instalation and get rid of the inconvience of udf formated iso file.

dd count=$(((`stat -c '%s' win7.iso` + 8*1024*1024) / 512)) if=/dev/sdb1 of=win7.img status=progress



## vital important poart of making boot sector/boot loader
sudo ms-sys -7 /dev/sdX


### other boot loader method

Example: syslinux

syslinux -i /dev/sdb1 (The first partition, pbr)
dd if=./syslinux/bios/mbr.bin of=/dev/sdb (write firmware to first 512 sector as mbr)


Mount the USB drive and create the directory boot/syslinux
Copy all \*.c32 files from /usr/lib/syslinux/bios to the boot/syslinux directory on the USB
Create a syslinux.cfg text file on boot/syslinux, with the text
```
LABEL win10
MENU LABEL Boot Windows 10 install
COM32 /boot/syslinux/chain.c32
APPEND label=win7fs ntldr=/bootmgr
```


#### make dd visualization with progress status
dd in GNU Coreutils 8.24+ (Ubuntu 16.04 and newer) got a new status option to display the progress.

We cloud just simply  append both status=progess and oflag=dsync parameters to dd.





> when it comes to pv, which is popular. It's actually not help me doing this at all. properble the command below would give you a beautiful progress window friendly.

( pv -n /dev/sdb1 | dd if=/mnt/nas/Images\ 系统镜像/UEFI+BIOS纯净PE.iso of=/dev/sdb1 bs=1M) 2>&1 | dialog --gauge "Running dd command (cloning), please wait..." 10 70 0


`pv /home/user/bigfile.iso | md5sum`


#### related utils
- mkusb-nox
- Unetbootion
- gparted
- parted

--------

##### reference:
- https://serverfault.com/questions/6714/how-to-make-windows-7-usb-flash-install-media-from-linux
- https://thornelabs.net/2013/06/10/create-a-bootable-windows-7-usb-drive-in-linux.html
- http://www.rmprepusb.com/tutorials/usbpreplinux