---
layout: post
title: ! 'Linux - forward X11 display to and launching gui app from docker'
category: tech
tags: tech
keywords: Manjaro, x11, docker-gui , docker, firefox, xmind-zend
---

> Dockerfile

```Dockerfile
FROM ubuntu:18.04
RUN sed -i 's/archive.ubuntu.com/mirrors.aliyun.com/g' /etc/apt/sources.list
RUN sed -i 's/security.ubuntu.com/mirrors.aliyun.com/g' /etc/apt/sources.list
RUN apt-get update && \
apt-get install -y firefox && \
apt install -y sudo


# Replace 1000 with your user / group id
RUN export uid=1000 gid=1000 && \
    mkdir -p /home/chris && \
    echo "chris:x:${uid}:${gid}:chris,,,:/home/chris:/bin/bash" >> /etc/passwd && \
    echo "chris:x:${uid}:" >> /etc/group && \
    echo "chris ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/chris && \
    chmod 0440 /etc/sudoers.d/chris && \
    chown ${uid}:${gid} -R /home/chris

#USER chris
ENV HOME /home/chris
USER chris

CMD /usr/bin/firefox
```

> You have to change the uid and gid fits your own . 1000 for me with manjaro kde and username chris on host. Username inside the container and outside(host) can be different but `uid` and `gid` must be the same as your host's.


Then, `docker run -ti --net host --rm -e XAUTHORITY=/home/chris/.Xauthority -e DISPLAY=$DISPLAY -v /home/chris/.Xauthority:/home/chris/.Xauthority -v /tmp/.X11-unix/:/tmp/.X11-unix ubuntu-firefox:18.04 /bin/bash
`

**`--net` must be provided.**



## Running xmind-zend from docker to Linux X11 of host.

> For the latest pango on aur breaks the xmind-zend from core dump. We have to downgrade the pango package version or install the pango-ubuntu by replacing the fontconfig with ubuntu-fontconfig. Both solutions doesn't make our system configurable.

**Base on what I've demonstrated with lauching firefox from docker before. It's possible to run xmind-zen from docker container we built.**


> Dockerfile

```Dockerfile
FROM ubuntu:18.04
RUN sed -i 's/archive.ubuntu.com/mirrors.aliyun.com/g' /etc/apt/sources.list
RUN sed -i 's/security.ubuntu.com/mirrors.aliyun.com/g' /etc/apt/sources.list
ENV TZ=Asia/Hong_Kong
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN apt-get update && \
apt-get install -y firefox && \
apt install -y sudo git && \
apt install -y libgtk2.0-0 libxss1 libgconf2.0 libnss3 && \
apt install -y locales && \
locale-gen zh_CN.UTF-8 &&\
apt install -y fonts-noto-cjk fonts-noto-cjk-extra fonts-noto-color-emoji fonts-noto-mono && \
ap  install -y language-pack-zh-hans

# Replace 1000 with your user / group id
RUN export uid=1000 gid=1000 && \
    mkdir -p /home/chris && \
    echo "chris:x:${uid}:${gid}:chris,,,:/home/chris:/bin/bash" >> /etc/passwd && \
    echo "chris:x:${uid}:" >> /etc/group && \
    echo "chris ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/chris && \
    chmod 0440 /etc/sudoers.d/chris && \
    chown ${uid}:${gid} -R /home/chris

#USER chris
ENV HOME /home/chris
ENV LC_all zh_CN.UTF-8
USER chris

WORKDIR /home/chris
RUN git clone https://github.com/Xmader/xmind_zen_linux.git

CMD /home/chris/xmind_zen_linux/xmind
```

Then, `docker run -ti --net host --rm -e XAUTHORITY=/home/chris/.Xauthority -e DISPLAY=$DISPLAY -v /home/chris/.Xauthority:/home/chris/.Xauthority -v /tmp/.X11-unix/:/tmp/.X11-unix xmind-ubuntu:18.04
`

![docker_xmind_zend](/assets/images/201910/DeepinScreenshot_select-area_20191010182344.png)


## with media device

```bash
--device /dev/dri //3d acceleration support
--device /dev/dri \
--device /dev/snd \ //audio


--device /dev/dri \
--device /dev/snd \
--device /dev/video0 \ //webcam

--device /dev/input

```


## language support

```
sudo apt install locales
sudo locale-gen zh_CN.UTF-8

sudo apt-get install language-pack-zh-hans
sudo apt-get install -y --force-yes --no-install-recommends  fonts-droid-fallback ttf-wqy-zenhei ttf-wqy-microhei fonts-arphic-ukai fonts-arphic-uming
sudo apt install fonts-noto-cjk fonts-noto-cjk-extra fonts-noto-color-emoji fonts-noto-mono

```

## Reference

- https://medium.com/@SaravSun/running-gui-applications-inside-docker-containers-83d65c0db110
- http://wiki.ros.org/docker/Tutorials/GUI#Using_Audio
- http://somatorio.org/en/post/running-gui-apps-with-docker/