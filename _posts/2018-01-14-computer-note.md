---
category: 不知道
layout: post
title: 电脑笔记
---
## Too many open files
ulimit -a 查看open files

果然很小

在ubuntu16上直接``` ulimit -n 50000```

在ubuntu14上要这样：
```
sudo sh -c "ulimit -n 500000 && exec su $LOGNAME"
```

## GPU内存没有释放
今天用pytorch同时用两个gpu运行，用ctrl+c停止之后gpu内存没有释放。

（来自[yjl9122的博客](https://blog.csdn.net/yjl9122/article/details/78920986)）
使用PyTorch设置多线程（threads）进行数据读取（DataLoader），其实是假的多线程，他是开了N个子进程（PID都连着）进行模拟多线程工作，所以你的程序跑完或者中途kill掉主进程的话，子进程的GPU显存并不会被释放，需要手动一个一个kill才行，具体方法描述如下：

1.先关闭ssh（或者shell）窗口，退出重新登录

2.查看运行在gpu上的所有程序：

fuser -v /dev/nvidia*

3.kill掉所有（连号的）僵尸进程

## @
装饰器。[这位的博客](http://www.wklken.me/posts/2013/07/19/python-translate-decorator.html)

python3.5用@来表示矩阵相乘

## python调用bing搜索图片
先注册个microsoft azure，弄一个keys，然后[照抄这些](https://docs.microsoft.com/zh-cn/azure/cognitive-services/bing-image-search/quickstarts/python)

[参数怎么填](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/bing-images-api-v7-reference#query-parameters)

也不知道要不要收钱，反正现在还没收我的

## Ubuntu 忘了密码
https://askubuntu.com/questions/24006/how-do-i-reset-a-lost-administrative-password

如果root密码也忘了

https://www.howtogeek.com/howto/linux/reset-your-forgotten-ubuntu-password-in-2-minutes-or-less

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
proxychains 

这年头安装tensorflow都要用代理了

这才过了没多久，安装pytorch也要用代理了 遵纪守法的好孩子读个研究生没有代理简直寸步难行

下载proxychains之后，/etc里会有个proxychains.conf，把这个文件最后一行的sock4 127.0.0.1:xxxx 改成自己的代理的地址和端口

## 重装系统home不掉
可能装系统的时候不格式化/home/所在的那个分区也可以但是没敢试过。这种不是好办法但是能用。

查看有哪些磁盘```sudo lshw -C disk```

假设原来home/zeng在sdb1上，新装的在sdb2上，新的/home/zeng里什么也没有，希望恢复成重装系统之前的样子。
```shell
sudo mkdir /mnt/tmp
sudo mount /dev/sdb1 /mnt/tmp
sudo rsync -avx /home/zeng /mnt/tmp
sudo rm -rf /home/zeng/*
sudo umount -l /home/zeng
sudo mount /dev/sdb1 /home/zeng
```
然后把新装的home挂到某个地方，或者干脆不要了

设置启动自动挂载
```shell
sudo blkid
```
记住sdb1和sdb2的UUID和TYPE

编辑/etc/fstab，添加自动挂sdb1到/home/zeng，如果sdb2还要用的话也加上sdb2

这么做完了之后用户的家目录的所有者可能会变，导致无法登录（现象是在图形界面里切换用户，无法进入桌面，而是又回到了让你选择用户的地方）。手动修改一下家目录的所有者就行了。比如用ls -l /home/zeng，发现所有者不是zeng。改一下：```sudo chown -R zeng:zeng /home/zeng```
