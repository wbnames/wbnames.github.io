---
layout: post
title: 在VPS上搭建shadowsocks来科学上网
category: shadowsocks
tags: [shadowsocks]
---

之前一直在使用免费的ss，但是访问外网的速度实在是不可恭维，索性决定自己搭建VPS来翻墙。本来准备在[搬瓦工](https://bandwagonhost.com/)购买，不过最低配置也要$19.9/Y。  

后来意外发现 GitHub Education 有面向学生的福利，一旦成功申请到Github Student pack，就可以获得 $50，加上充值的$5和小伙伴给的邀请链接 $10,此时账户一共有 $65，对于最低配置的 VPS $5 一个月，可以使用一年啊！！直到毕业之前都一直有 Promo Code 赠送，那就远远不止免费一年了。

推荐有能力，爱折腾，有科学上网需要的小伙伴食用此教程。
这是得到 Student Developer Pack 时Git发来的邮件的部分内容：

> Spread the word: we love giving educational discounts to students, teachers, administrators, and researchers! Please send them to:  
        https://education.github.com 

真是暖心啊 业界良心！！！
具体过程参考以下内容：

## 获取Student Developer Pack

如果你不是学生，请跳过第一步。如果你是学生的话，首先你要 [注册GitGub帐号](https://github.com/)，然后前往 [GitHub Education](https://education.github.com/)获取Student Developer Pack，点击 I am a student 填写学生验证信息， Verify academic status 一栏选择 I don't have a school-issued email （第一次我使用学校的 edu 邮箱提交申请当天就被拒绝了，貌似国内edu邮箱没有公信力），然后上传你的学生证照片或者能证明你是学生即可。剩下的就是漫长的等待了，不过这点等待还是很值得的（本人等了接近2天终于收到确认信件）~~~


##  DigitalOcean

### 注册DO帐号

首先注册 DigitalOcean 账号。可以点击我的[邀请链接](https://m.do.co/c/6d3c33c4b39e)注册，注册成功后你的 DigitalOcean 下会收到10美金的邀请奖励，然后去你的注册邮箱激活帐号即可。

![注册DO帐号](http://ocb0u6vn2.bkt.clouddn.com/2016-08-14-regist.png)

### 使用PayPal支付

帐号验证成功后，提示绑定信用卡或者使用 PayPal支付，作为国内用户建议不要使用国内信用卡。PayPal是海外最大的在线支付平台，类似于国内的支付宝，没有使用的过小伙伴去注册一个即可，然后支付5美金折合人民币大概34元。

![PayPal支付](http://ocb0u6vn2.bkt.clouddn.com/2016-08-14-paypal.png)

* 提示：如果使用学生身份你获得了Student Developer Pack，那么可以在注册成功后在 Settings->Billing 下找到 Promo Code，输入你在 Student Developer Pack 获得的学生优惠码，价值50美金，这对于屌丝学生来说可是笔不小的数目。

### 创建VPS

注册成功之后就可以登录DigitalOcean并创建你的VPS了。操作系统的话，我选择的是ubuntu的，看个人喜好选择吧。服务器选择 $5/mon 的最低端的配置就够了，如有你有建站或者其他需求的话另行选择。由于个人使用的是移动网，相对来说Singapore的机房延迟比较低100ms左右，而其他的的机房延迟都很高，最后点击Create创建。

提示:
- 贴一个测速地址，请根据测速结果选择**最佳带宽和延迟较低的机房**。 [Digitalocean服务器测速-新加坡节点](http://speedtest-sgp1.digitalocean.com/)
- 由于服务器在海外，地理位置原因导致延迟高是无法避免的，请根据测速情况选择合适的机房
- 此外，创建服务器时可以添加SSH密钥，配好公钥后就可以免密登录了

## ShadowSocks

### SS介绍

[ShadowSocks](https://github.com/shadowsocks)是科学上网的利器，在 Github 上接近1.4W的 Star，使用的人极多、影响极深。而且它对各个平台的支持也非常好，目前我在Windows/Android平台上都完成了科学上网环境的搭建。

### 配置shadowsocks服务端

创建成功后，在你的 droplets 控制面板上的服务器名后点击下拉 more ，再点击 accessv console 进入远程终端的连接。
![控制台](http://ocb0u6vn2.bkt.clouddn.com/2016-08-14-console.png)

*  Tips:记下你的服务器IP地址，稍后配置SS客户端时会用到。

![登录主机](http://ocb0u6vn2.bkt.clouddn.com/2016-08-14-login.png)

初次进入需要输入初始的的账号和密码，账号是 root，密码会创建完 droplet 后发送至你的邮箱（如长时间未收到，请注意是否在邮件垃圾箱里）。  
- 输入初始密码回车确认( 特别说明:输入密码时不会有暗文 "********" 显示，尽管输入然后回车确定就好。而且初始密码有点长，需要有点耐心。^-^)  
- (current) UNIX password 系统会让你再次输入初始的密码  
- Enter new UNIX password 此时输入你要修改新密码  
- Retype 确认修改的新密码  
至此，VPS远程登陆成功。

### 在VPS上安装Shadowsocks

输入以下命令安装Shadowsocks：

```shell
sudo apt-get update
sudo apt-get install python-pip
# 询问是否Continue，输入y确认
sudo pip install git+https://github.com/shadowsocks/shadowsocks.git@master
```

### 创建配置文件

```shell
sudo vim /etc/shadowsocks.json
```

按a键进入编辑模式，输入以下配置项

```json
{
    "port_password":{
        "443":"yourpassword"
    },
    "method":"aes-256-cfb",
    "timeout":600
}
```

然后按esc键退出编辑模式，输入  **:wq**  保存配置文件

### 启动SS服务

后台启动ss服务

```shell
sudo ssserver -c /etc/shadowsocks.json -d start 
```

关闭ss服务

```shell
sudo ssserver -d stop
```

或者直接在启动时加入相关配置参数，无需配置启动文件

```shell
sudo ssserver -p 443 -k yourpassword -m aes-256-cfb --user nobody -d start
```

###  SS客户端下载

前往GitHub托管的[ShadowSocks](https://github.com/shadowsocks)项目下载相应客户端。  

这里仅贴出windows版本：[下载地址](https://github.com/shadowsocks/shadowsocks-windows/releases)

###  客户端配置

客户端栏目填入以下项:

```shell
服务器地址：你的服务器IP
服务器端口：443    # 或者自定义端口，注意与开放的端口要一致
密码： "yourpassword"
加密： "aes-256-cfb"
```

配置完成后在记得托盘图标里勾选启动系统代理，就可以访问外网，获取你所需要的资源了。

贴张图表示下 ---> YouTube 1080P 毫无压力

![youtube](http://ocb0u6vn2.bkt.clouddn.com/2016-08-14-youtube.png)

## ShadowSocks加速

### 使用Google BBR实现TCP加速

只需简单的几步，即可实现ShadowSocks代理速度的提升，强烈推荐。

详细内容请查看： [使用Google BBR实现TCP加速](http://waynechu.cn/shadowsocks/2016/08/19/%E4%BD%BF%E7%94%A8Google-BBR%E5%AE%9E%E7%8E%B0TCP%E5%8A%A0%E9%80%9F.html)  


> 如有疑问请留言或者给我来信。 E-mail:  waynechu@waynechu.cn

