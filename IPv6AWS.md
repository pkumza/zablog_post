title: 用TunnelBroker给AWS绑定IPv6
date: 2015-11-23 14:44:52
toc: true
categories: ['技术指南']
description: Hurricane Electric Tunnel on AWS. 在互联网高速发展的今天，IPv4已经渐渐不能适应现代的需求,亟需IPv6的普及。AWS是优秀的云服务，然而它的虚拟主机产品EC2却不支持IPv6，这给使用者带来了一些困难。这里，就是用Hurricane Electric的TunnelBroker为AWS提供一个IPv6转发接口。
tags: [AWS, IPv6]
---

# 准备

1. 一个AWS虚拟机，本例子是一个ubuntu的。![aws虚拟机](http://i12.tietuku.com/aff5cee104cd34c9.png)
2. 最好有一个__弹性IP__，

# 动机

在互联网高速发展的今天，IPv4已经渐渐的不能适应现代的需求，IPv6是新一代技术，虽然已经提出了很多年，但是依然没有很好的实现。有使用ISP支持等，但是很多效果都不是特别好，使用Tunnel也成为了一种选择。
AWS是方便的云虚拟机，效率高，价格合适，性能优秀，是一个良好的选择。它的功能和阿里云等国内云服务类似。然而AWS可能是IPv4资源过于充足，在别的VPS已经有IPv6的时候，仍然迟迟不提供IPv4的支持。其实IPv6地址相对来说比较充足，比IPv4的成本低很多，不清楚为什么AWS不支持。
IPv6对于我们，尤其是教育网内的人来说非常重要，它在教育网内网速巨快，而且少有收费地址等条框限制，是不可多得的好资源。

# 开始设置

<!--more-->

## 调整AWS的安全组

1. 首先进入EC2控制面板，找到安全组选项，点进去。
2. 找到入站规则，添加Rules，添加All ICMP。这是为了地址能够被PING到。![Rules](http://i12.tietuku.com/514ea7076bc012c5.png)
3. 申请一个弹性IP（Elastic IP），这个是额外收费的，我的地址是52.5.128.0，绑定到这个机器上。

运行命令测试：

```bash
$ ping -c 5 52.5.128.0
PING 52.5.128.0 (52.5.128.0): 56 data bytes
64 bytes from 52.5.128.0: icmp_seq=0 ttl=42 time=303.848 ms
64 bytes from 52.5.128.0: icmp_seq=1 ttl=42 time=301.021 ms
64 bytes from 52.5.128.0: icmp_seq=2 ttl=42 time=298.133 ms
64 bytes from 52.5.128.0: icmp_seq=3 ttl=42 time=307.072 ms
64 bytes from 52.5.128.0: icmp_seq=4 ttl=42 time=306.857 ms

--- 52.5.128.0 ping statistics ---
5 packets transmitted, 5 packets received, 0.0% packet loss
round-trip min/avg/max/stddev = 298.133/303.386/307.072/3.436 ms
```

可以ping通。

## 注册 HE Tunnel

登陆网站 [http://tunnelbroker.net/](http://tunnelbroker.net/) ，填写注册表，注册自己的e-mail，登陆就可以了。登陆以后可以看到这个主页。
点击Create Regular Tunnel就可以创建了。
![HE](http://i12.tietuku.com/74a3539e951cbddc.png)
输入IPv4地址。这个地址填写你的AWS服务器的地址，而不是你自己电脑的地址。
这里我就把刚才的52.5.128.0填写进去，它会告诉你可以建立一个potential tunnel。
下面是选择Tunnel Service。这个选择就因人而异了。
![Alloc](http://i12.tietuku.com/d92e5c97efdfb68a.png)
因为我的AWS服务器是放在VA东，我在服务器上ping它这个服务器，Ashburn, VA, US是最快的，平均低于1ms，可以说是快到飞起，所以就选它了。
点击Create Tunnel
选择完成以后，就会完成绑定，给你这么一张表格。![Address](http://i12.tietuku.com/140b4a208b95302c.png)
Client IPv4 是你自己的AWS的IPv4的地址。
Client IPv6 是给你的AWS分配的IPv6的地址。
Server IPv4 是Tunnel Server 的IPv4的地址。
Server IPv6 是Tunnel Server 的IPv6的地址。
Routed IP Prefixes 是IPv6的前缀。
（我们知道IPv6的地址是很长的，比较丰富的，全地址是用七个冒号分隔的八个四位16进制数。一般用不完，所以现在随便一分配，就分配给一个前缀，后面还剩下64位自用，就是说你自己可以自己处理这2^64个地址。）

## 设置Tunnel

绑定Tunnel之后，并不能直接使用，AWS还需要进行一些设置。
不信可以在AWS试一下ping6 命令能否使用，我认为现在应该都是不行的。

### 自动找到自己的IP地址

建立一个脚本，来输出自己的本地IP（注意这个本地IP是内部IP，并不是127.0.0.1这个回环地址。）

在server上运行如下命令

```bash
sudo vim /usr/local/bin/checkipeth0
```

按下i（Vim的插入），然后把下面的命令粘贴进去。

```bash
#!/usr/bin/perl
$check=`ip addr show dev eth0|grep "inet "|awk '{print \$2}'|awk -F/ '{print \$1}'`;
print $check;
```

保存 （按下 ESC ， 冒号 ， wq ）

添加运行权限：

```bash
sudo chmod +x /usr/local/bin/checkipeth0

/usr/local/bin/checkipeth0  # Test.
```

### 建立 Tunnel Interface

这段代码在服务器启动的时候，建立一个虚拟的tunnel interface。
运行如下命令：

```bash
sudo nano /etc/network/interfaces
```

粘贴如下内容：

```bash
auto he-ipv6
iface he-ipv6 inet6 v4tunnel
  address $CLIENT_IPV6
  netmask 64
  endpoint $SERVER_IPV4
  local `/usr/local/bin/checkipeth0`
  up ip -6 route add default dev he-ipv6
  down ip -6 route del default dev he-ipv6
```

注意把
$CLIENT_IPV6 
和
$SERVER_IPV4
替换成自己的地址。

按下 ESC :wq 保存

最后运行如下命令：

```bash
sudo ifup he-ipv6

ifconfig he-ipv6

ping6 -c 4 google.com
```

第一个命令如果出问题了，遇到了“add tunnel sit0 failed: No buffer space available”错误，那多半是因为已经有he-ipv6，那么运行命令 sudo ip tun del he-ipv6就可以了。
参考[ASKUBUNTU](http://askubuntu.com/questions/109709/hurricane-ipv6-buffer-space-error)

设置静态的IPv6地址到eth0
当设置好了Hurricane Electric tunnel之后，其实已经可以ping通一些东西了，但是如何让AWS通过eth0联通到这个IPv6上呢，需要绑定静态的地址。

```bash
sudo vim /etc/network/interfaces
```

增加 

```bash
iface eth0 inet6 static
  address 2001:470:7:5bf::1
  netmask 64
```

当然这里的address一定要填写自己的。找到你自己的Routed IPv6 Prefixes，把/64删掉，加上1就可以了。

最重要的文件/etc/network/interface最终形态：

```bash
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

# The loopback network interface
auto lo
iface lo inet loopback

# Source interfaces
# Please check /etc/network/interfaces.d before changing this file
# as interfaces may have been defined in /etc/network/interfaces.d
# NOTE: the primary ethernet device is defined in
# /etc/network/interfaces.d/eth0
# See LP: #1262951

auto eth0
iface eth0 inet dhcp
iface eth0 inet6 static
	address 2001:470:8:5bf::1
	netmask 64

auto he-ipv6
iface he-ipv6 inet6 v4tunnel
        address 2001:470:7:5bf::2
        netmask 64
        endpoint 216.66.22.2
        local `/usr/local/bin/checkipeth0`
        up ip -6 route add default dev he-ipv6
        down ip -6 route del default dev he-ipv6
```

修改好文件以后，运行如下命令：

```bash
sudo /etc/init.d/networking restart

ifconfig eth0
```

这样就可以成功完成eth0的设置

## 保持tunnel常开

tunnel如果不适用可能会自己关掉，所以要弄一个脚本让它常开。最简单的方法就是，过一阵子发送一些ping请求。

```bash
sudo vim /etc/cron.d/he-ipv6
```

键入

```bash
*/2 * * * *    nobody  ping6 -c 3 -n -q $SERVER_IPV6 > /dev/null
```

按下 ESC :wq 保存

## 测试Tunnel连通性

```bash
sudo tcpdump -vv -i he-ipv6 ip6
```

用本地机器的命令行去ping服务器。
以前是 ping 52.5.128.0 可以通，但是直接没有IPv6的地址，现在IPv6也可以ping通了：

```bash
$ ping6 -c 5 2001:470:7:5bf::2
PING6(56=40+8+8 bytes) 2001:da8:201:1203:f812:bdb5:9228:67ed --> 2001:470:7:5bf::2
16 bytes from 2001:470:7:5bf::2, icmp_seq=0 hlim=50 time=210.542 ms
16 bytes from 2001:470:7:5bf::2, icmp_seq=1 hlim=50 time=210.216 ms
16 bytes from 2001:470:7:5bf::2, icmp_seq=2 hlim=50 time=210.410 ms
16 bytes from 2001:470:7:5bf::2, icmp_seq=3 hlim=50 time=210.301 ms
16 bytes from 2001:470:7:5bf::2, icmp_seq=4 hlim=50 time=210.216 ms

--- 2001:470:7:5bf::2 ping6 statistics ---
5 packets transmitted, 5 packets received, 0.0% packet loss
round-trip min/avg/max/std-dev = 210.216/210.337/210.542/0.125 ms
```

不知道读者有没有发现，ping IPv6地址的延迟更小。
这可能是因为IPv4和IPv6的线路有细微差别吧。

IPv6的地址还是好长一段，很难记忆。尤其是本地地址，我当前的本地地址是2001:da8:201:1203:f812:bdb5:9228:67ed，好长一段。
域名和DNS就是为了应对这种情况发明的，如果当初的人们能够像机器一样瞬间记住那些IPv4地址甚至IPv6地址的话，估计就不会有DNS的发明了。
IPv6的绑定域名和IPv4没有太差区别。IPv4是添加A记录，IPv6是添加一条AAAA记录就可以了。


Preference：
1. [https://samsclass.info/ipv6/proj/pHE1A-Tunnel.htm](https://samsclass.info/ipv6/proj/pHE1A-Tunnel.htm)
2. [http://askubuntu.com/questions/109709/hurricane-ipv6-buffer-space-error](http://askubuntu.com/questions/109709/hurricane-ipv6-buffer-space-error)
3. [http://blog.iphoting.com/blog/2012/06/02/ipv6-on-amazon-aws-ec2/](http://blog.iphoting.com/blog/2012/06/02/ipv6-on-amazon-aws-ec2/)

