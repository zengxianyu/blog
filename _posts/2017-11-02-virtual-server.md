---
layout: post
title: 虚拟服务器作代理
---

* 要求：edu邮箱、支持visa的信用卡。
<!--break-->
* https://education.github.com/pack
* https://github.com/shadowsocks/shadowsocks-libev
* https://www.digitalocean.com/

## 1 github promo code
* 用学校邮箱注册github账号。在[github education](https://education.github.com)　点 "request a discount"。填写信息等待审核。
* 拿到学生优惠之后，点"get the pack" --> "get your pack"。
* 在你的优惠里面找到digital ocean的promo code。

## 2 虚拟服务器
* 在 [digital ocean](https://m.do.co/c/13b5d8f335f9) 申请账号。据说用这个链接可以获得10美元代金券。
* 验证邮箱后，输入信用卡号码之类的。在填卡号的页面最底下有一行字"have a promo code?"，点这个，输入在上一步中拿到的github promo code。这样，账户里就又多了50美元了。

## 3 配置虚拟服务器
在digital ocean上新建一个droplet。选择ubuntu 14.04系统，选择最便宜的配置。

下面是在ubuntu 14.04虚拟服务器上安ss的例子。其他系统的安装方法在[ss的github仓库]( https://github.com/shadowsocks/shadowsocks-libev)

*我用的是ubuntu 14.04服务器。我同学用ubuntu 14.04服务器不好使：能ping通，但是作代理无法访问浏览网页。她换用Debian服务器就好使了。

### 1 在虚拟服务器上安装server
```shell
pip install shadowsocks
sudo apt-get install software-properties-common -y
sudo add-apt-repository ppa:max-c-lv/shadowsocks-libev -y
sudo apt-get update
sudo apt install shadowsocks-libev
```

### 2 在虚拟服务器上建立文件'ssconfig.jason'，内容：
```jason
{
	"server":"虚拟服务器ip",
	"server_port":2333,
	"local_port":1080,
	"password":"密码",
	"timeout":120,
	"method":"aes-256-cfb"
}
```

### 3 在虚拟服务器运行
```shell
ssserver -c ssconfig.jason -d start
```

## 4 在自己电脑上安装client
```shell
pip install shadowsocks
sudo apt-get install software-properties-common -y
sudo add-apt-repository ppa:max-c-lv/shadowsocks-libev -y
sudo apt-get update
sudo apt install shadowsocks-libev
```

## 5 在自己电脑（linux或osx）上运行
```shell
ss-local -s 虚拟服务器ip -p 2333 -l 1080 -k 密码 -m aes-256-cfb
```

## 6 chrome+switch omega插件
* chrome顶端的tool --> extensions。
* 在Developer mode那儿打钩。
* 在网上找个switch omega的crx文件。拖到chrome里即可安装。
* 在switch omega里新建profile。Protocal 选SOCKS5，server填127.0.0.1，port填1080，Bypass List填```<local>```。
* 点switch omega的图标，选择自动切换。

## 7 在手机上运行
https://github.com/shadowsocks/shadowsocks-android
