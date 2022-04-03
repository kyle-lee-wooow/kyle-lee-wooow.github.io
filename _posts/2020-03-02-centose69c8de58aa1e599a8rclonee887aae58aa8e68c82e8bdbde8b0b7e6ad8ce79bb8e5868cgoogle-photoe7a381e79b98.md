---
id: 12
title: 'CENTOS服务器rclone自动挂载谷歌相册Google photo磁盘'
date: '2020-03-02T05:44:58+08:00'
author: '帅 哥轩'
layout: post
guid: 'http://blog.liwenxuan.vip/?p=12'
permalink: /2020/03/02/centos%e6%9c%8d%e5%8a%a1%e5%99%a8rclone%e8%87%aa%e5%8a%a8%e6%8c%82%e8%bd%bd%e8%b0%b7%e6%ad%8c%e7%9b%b8%e5%86%8cgoogle-photo%e7%a3%81%e7%9b%98/
fifu_image_url:
    - 'https://cdn.pixabay.com/photo/2013/01/29/00/47/google-76517_960_720.png'
fifu_image_alt:
    - 'CENTOS服务器rclone自动挂载谷歌相册Google photo磁盘'
image: 'https://cdn.pixabay.com/photo/2013/01/29/00/47/google-76517_960_720.png'
categories:
    - linux
---

```
<pre class="wp-block-code">```
安装EPEL源（这一步国外VPS一般可不用操作）:
```bash
yum -y install epel-release

yum -y install wget unzip screen fuse fuse-devel

wget https://downloads.rclone.org/rclone-current-linux-amd64.zip 
unzip rclone-current-linux-amd64.zip
cd rclone-v*

./rclone config


第一步选择n,

n) New remote

然后回车输入一个name:

name> gp
下面选择挂载类型-谷歌相册，数字顺序可能会变，记得选google photo的项目

13 / Google Photos
Storage> 13

下面三个选项留空，直接回车
client_id>
client_secret>
read_only>
下面两项选n

Edit advanced config? (y/n)
y/n> n

Remote config
Use auto config?
 * Say Y if not sure
 * Say N if you are working on a remote or headless machine
y) Yes
n) No

y/n> n


这里会给出一个让你访问授权的网址，把它复制出来贴浏览器访问，然后一路下一步，会给你返回一串验证码
If your browser doesn't open automatically go to the following link: *
Log in and authorize rclone for access
把给你的验证码贴这里
Enter verification code> *******dsadfddsdfdsfdf


*** IMPORTANT: All media items uploaded to Google Photos with rclone
*** are stored in full resolution at original quality.  These uploads
*** will count towards storage in your Google Account.
这一步选y
--------------------
[gp]
type = google photos
token = {"access_token":"*******"}
--------------------
y) Yes this is OK
e) Edit this remote
d) Delete this remote

y/e/d> y

Current remotes:

Name                 Type
====                 ====
gp                   google photos
onedrive             onedrive


q) Quit config
这一步选q退出
e/n/d/r/c/s/q> q

然后挂载，还是先创建一个本磁盘，用于映射
mkdir -p /gp

手工挂载的话，输入下面命令
./rclone mount gp: /gp --allow-other --allow-non-empty --vfs-cache-mode writes

需要开机自动挂载的话，继续往下看
先把rclone的可执行文件复制到/usr/bin：

cp /root/rclone-v*/rclone /usr/bin/rclone

新建一个rclonegp.service文件：
vi /usr/lib/systemd/system/rclonegp.service
写入：

[Unit]
Description=rclonegp
    
[Service]
User=root
ExecStart=/usr/bin/rclone mount gp: /gp --allow-other --allow-non-empty --vfs-cache-mode writes
Restart=on-abort
    
[Install]
WantedBy=multi-user.target



重载daemon，让新的服务文件生效：
systemctl daemon-reload

systemctl start rclonegp
设置开机启动：

systemctl enable rclonegp
停止、查看状态可以用：

systemctl stop rclonegp
systemctl status rclonegp
重启你的VPS，然后查看一下rclone的服务起来没，接着查看一下盘子挂上去没：

reboot
systemctl status rclonegp
df -h
到这里就完成了
```
```
```