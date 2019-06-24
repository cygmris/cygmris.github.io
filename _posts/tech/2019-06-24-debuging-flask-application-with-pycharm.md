---
layout: post
title: debuging flask application with pycharm and interpreter inside container
category: tech
tags: tech
keywords: tech,flask,pycharm,docker
---
## base environment

![manjaro](/assets/images/201906/Screenshot_20190624_161839.png)

```
 ██████████████████  ████████     OS: Manjaro 18.0.4 Illyria
 ██████████████████  ████████     Kernel: x86_64 Linux 5.1.8-1-MANJARO
 ██████████████████  ████████     Uptime: 24m
 ████████            ████████     Packages: 1744
 ████████  ████████  ████████     Shell: zsh 5.7.1
 ████████  ████████  ████████     Resolution: 1920x2160
 ████████  ████████  ████████     DE: KDE 5.59.0 / Plasma 5.15.5
 ████████  ████████  ████████     WM: KWin
 ████████  ████████  ████████     GTK Theme: Breath [GTK2/3]
 ████████  ████████  ████████     Icon Theme: ePapirus
 ████████  ████████  ████████     Font: Noto Sans Regular
 ████████  ████████  ████████     CPU: Intel Core i7-8750H @ 12x 4.1GHz [56.0°C]
 ████████  ████████  ████████     GPU: GeForce GTX 1050 Ti with Max-Q Design
                                  RAM: 4565MiB / 31787MiB
```

- with docker installed
- with pycharm installed



## setting up remote interpreter and path mapping

![remote_interpreter](/assets/images/201906/Screenshot_20190624_162218.png)

## setting up Run/Debug Configuration of the project

![run_debug_config](/assets/images/201906/Screenshot_20190624_162357.png)

> The most important part is to tick **Run with Python console** on , which will bring you the call stack and variable scope panel.

> The forked project named open_dnsdb on the screenshot is lately I'm working on these days.

##### reference:
- https://github.com/cygmris/open_dnsdb