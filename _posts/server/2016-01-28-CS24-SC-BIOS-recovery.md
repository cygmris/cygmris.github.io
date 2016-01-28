---
layout: post
title: CS24-SC BIOS Recovery
category: server
tags: CS24-SC virtualization
keywords: CS24-SC BIOS Recovery
---

  I bought a second-hand DellSC24-SC from taobao 2 years ago and put it aside for a long times beacuse there was a time i was as busy as a beaver as a hight school student. My first intention is to do some virtualization(vSphere,KVM, XEN,openvz...etc) research with it. But some how, i tried to deploy vSphere-suite for learning purpose on VMware Workstation and it fails because though SC24-SC support hardware virtualization but the the CPU(771pins) are not capable of nested virutalization for the lack of vt-x. In that case, i am not able to run the 64bit IOS(Cisco) on that platform.
  Recently, i have realized how docker may really change the world, i got ambtions to step into virtualization learning again. In this situation, it reminds me the SC24-SC on which i run VMs and use my macbook air to control them.


  I noticed there is a article( [http://robwillis.info/2014/05/dell-cs24-sc-bios-bmc-v2-5-firmware-download/](http://robwillis.info/2014/05/dell-cs24-sc-bios-bmc-v2-5-firmware-download/) ) written in 2014 share a newer ROM to flush BIOS of SC24-SC and rush at it. I flash FREEDOS to my 64g USB with uNetbootin but i didn't works to boot while i use chenfengPE(http://www.myfeng.cn/) which contain DOS tools kit to boot and follow the S45_3A25's readme instruction to flash.During the process , it was stuck at 57% erasing the flash. After waiting about 30 mins, i made a force-halt (long press power button) . That was a wrong decision and my server can't boot after what i had done. 


  I started to find ways to recovery my BIOS in Quanta_S45_Users_Manual motherboard.pdf. Then i moved the one jumper to 2-3 and back. After all, pull BIOS recovery jumper off. The build-in BIOS recovery program will be called in next time power on. The program required a floppy or a USB containing the AMIBOOT.ROM file on root directory. It was stuck again while reading AMIBOOT.ROM file on 64,16,8,4GB USB. I guess this is some pointer issue on the build-in program. so after i bought a 128MB one from taobao, it works and everything goes fine.




q


  ![](http://ww4.sinaimg.cn/large/6a87387agw1f0e5aqchxuj20qo0f0wfg.jpg)
