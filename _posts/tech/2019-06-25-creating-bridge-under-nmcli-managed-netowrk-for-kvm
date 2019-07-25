---
layout: post
title: creating bridge under NetworkManager Managed network for kvm
category: tech
tags: tech
keywords: network bridge,kvm
---

```

nmcli con add ifname kvmbr0 type bridge con-name kvmbr0
// add bridge device as an interface like plugin the bridge with a cable and interface.

nmcli con modify kvmbr0 bridge.stp no
// disable stp for resons I don't know yet.

sudo ip addr add 172.172.172.1/24 dev kvmbr0
//customize cidr

iptables -t nat -A POSTROUTING -s 172.172.172.0/24 ! -o kvmbr0 -j MASQUERADE
// enable nat
```

# note and discovery

> This bridge doesn't work with VirtualBox. VM is not able to ping the host with connected network status