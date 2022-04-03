---
id: 171
title: '将/dev/centos/home 下个空间分配给/root'
date: '2021-11-20T17:34:54+08:00'
author: '帅 哥轩'
layout: post
guid: 'https://blog.liwenxuan.vip/?p=171'
permalink: /2021/11/20/%e5%b0%86-dev-centos-home-%e4%b8%8b%e4%b8%aa%e7%a9%ba%e9%97%b4%e5%88%86%e9%85%8d%e7%bb%99-root/
categories:
    - linux
tags:
    - linux
---

yum install -y psmisc  
fuser -km /home  
umount /home  
lvremove /dev/centos/home  
vgdisplay  
lvcreate -L 30G -n home centos  
vgchange -ay centos  
mkfs -t xfs /dev/centos/home  
mount /dev/centos/home /home/  
vgdisplay

lvextend -L +250G /dev/centos/root  
xfs\_growfs /dev/centos/root  
xfs\_growfs /dev/centos/root