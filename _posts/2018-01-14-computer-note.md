---
layout: post
title: 电脑笔记
---
## Ubuntu 忘了密码
https://askubuntu.com/questions/24006/how-do-i-reset-a-lost-administrative-password

如果root密码也忘了

https://www.howtogeek.com/howto/linux/reset-your-forgotten-ubuntu-password-in-2-minutes-or-less/
## ssh隧道
用来访问校园网防火墙里的电脑、应该也可以观看大长城外的站点
来自[I&ME的博客](http://arondight.me/2016/02/17/%E4%BD%BF%E7%94%A8SSH%E5%8F%8D%E5%90%91%E9%9A%A7%E9%81%93%E8%BF%9B%E8%A1%8C%E5%86%85%E7%BD%91%E7%A9%BF%E9%80%8F/)

A是公网电脑，B是内网电脑，B的ssh端口号是6991。

在B上```autossh -M 6992 -NR 6991:localhost:6991 userA@ipA```

然后别的电脑要访问B时，先ssh到A，再在A上```ssh -p 6991 userB@localhost```

坑：如法炮制映射网络应用的端口8888```autossh -M 8889 -NR 8888:localhost:8888 ubuntu@ipA```结果遇到了问题：公网电脑在127.0.0.1听这个端口。在别的电脑上用浏览器打不开ipA:8888。网上说在sshd_config里加上GateWayPorts yes就行了，然而并不行。于是在公网电脑再用```socat TCP4-LISTEN:2222,reuseaddr,fork TCP4:localhost:8888```，再转发一次这样就可以在别的电脑用浏览器打开2222端口了（来自[这位雷锋的博客](http://log4think.com/corss_firewall_by_ssh_tunnel_on_vps/)）

不过，我简直是傻了才整这破玩意整到半夜😂 假期在家肯定不会连学校电脑假装学习 😂 而且公网ip电脑我也租不起啊

## 从视频保存帧
avconv

```avconv -i 视频文件名 -r 1（每秒1帧） -f image2 保存目录名/%%06d.jpg```
<!--break-->

## 终端走代理
proxychains 这年头安装tensorflow都要用代理了

## 重装系统home不掉
可能装系统的时候不格式化那个分区也可以但是没试过。这种不是好办法但是能用。假设原来home/zeng在sdb1上，新装的在sdb2上。
```shell
mkdir /mnt/tmp
mount /dev/sdb1 /mnt/tmp
rsync -avx /home/zeng /mnt/tmp
rm -rf /home/zeng/*
umount -l /home/zeng
mount /dev/sdb1 /home/zeng
```
然后把新装的home挂到某个地方，或者干脆不要了

设置启动自动挂载
```shell
sudo blkid
```
记住sdb1和sdb2的UUID和TYPE

编辑/etc/fstab，添加自动挂sdb1到/home/zeng，如果sdb2还要用的话也加上sdb2
