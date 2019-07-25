---
layout: post
title: Hopper brute force log
category: tech
tags: tech
keywords: Hopper, reverse engineering, crack
---



```
Please locate the license file first

try the demo
buy a license
Offiline Activation
validate License

Your license file needs to be validated.
Please check your internet connection, and try again(This check needs to be done only once)

if you dont't have access to the Internet on this machine, please following code to bsr43@cryptic-apps.com:

LINUX_xxxx==

Once you received the validation code, pllease copy, and paste it here:

Cannot validate license file.

Please make sure that the license file is a valid Hopper v3/v4 license, which has not expired, and that it has not been installed on too many computers.
```

//502bc2 //2819
//502819
->//502827 detect jump

502a14 loc_502a14 //thank

loc_502908 //thank

// flashcode.cryptic-apps.com
// /etc/machine-id

The offline activation response seems to be invalid.

// sub_504c40 machine code

## Timer
```
sub_63f800->sub_63bab0 //timeout execute
loc_5823f
```



501848 //islocalfile

sub_502780
->sub_5024E0 //
->sub_50457 // hopper dir

/etc/hopperv4 //0x38420a8
575S10KOTAVT

`/home/chris/GNUstep/Library/ApplicationSupport/Hopper/License.hopperLicense`

sub_502e70 //online validation
loc_502ee2 //validate license with https://www.hopperapp.com/valicczzdate_license_v4.php

#online validation

```
502780+84->502804                           504550+b->50455b
|                                                       |
|                                                       |
sub_502e70(online validation func)                 <----|
```

## offline validation

```
65b850->
50e480-> //base64
50e553
50E553 //noped
loc_50E56B:
050E581 //offline activation
0000000000502829 loc_502829 //offline
loc_506ec1 //validating
```

## save

```
63ccb2->
504550->sub_5024E0->eax,3->=sub_502e70(ov)->5024e0(check again)
                  !->504565
63cd50->msgbox
```

#about menu

Address : `4c212f`

## solution

sub_5024E0 is the key function on which many other module rely.

replace retn with mov eax,0x01 and retn at the end of sub_5024E0 break all limitation of The Demo. 

![Hopper validation Ref](/assets/images/201907/DeepinScreenshot_plasmashell_20190722041923.png)

## sub_5024E0

**switch table**

`case 3`
>
Cannot validate license file.
Please make sure that the license file is a valid Hopper v3/v4 license, which has not expired, and that it has not been installed on too many computers.

`case 1 2`

**pass**