# 第一章 在树莓派上：安装Ubuntu Server 20.04

## 01 什么是树莓派

树莓派是英国[树莓派基金会](https://www.raspberrypi.org)开发的卡片式电脑，采用高通的BCM2711 ARM64处理器，可用于机器人、物联网、边缘计算、通用计算等多种场景，由于性能不错、扩展丰富、价格便宜，很多开发者也会使用树莓派作为自己的实验性开发平台，或作为低成本服务器使用。

树莓派最新的硬件版本为4B，板载内存包括2GB、4GB和8GB三个版本，其他部分的硬件完全一致：1个ARM64（ARM v8）64位四核处理器，Cortex A72内核，运行在1.5GHz；1个USB Type-C供电口；两个Micro HDMI显示接口，支持双4K显示器；双通道MIPI DSI显示输出和双通道MIPI CSI摄像头输入；2个USB 2.0 Type A接口；2个USB 3.0 Type A接口；1个“真”千兆RJ45接口；支持IEEE 802.11ac标准的2.4G/5G无线网卡；支持Bluetooth 5.0；40针的GPIO针脚；1个用于引导系统的MicroSD（TF）卡插槽。核心板版本的树莓派4还支持选择板载eMMC芯片。

这一代树莓派4的供电需求比树莓派3多一些，官方建议使用5V3A或最低5V2.5A的供电。供电可以采用三种方式：使用USB Type-C供电口；GPIO供电针脚；使用PoE供电针脚。其中PoE供电需要外接HAT板，直接通过PoE交换机从网线供电。

从扩展能力上来说，树莓派4已经非常强悍，完全不比普通的电脑差，而且开箱即用，可以做到完全静音。

## 02 什么是Ubuntu Server

[Ubuntu](https://www.ubuntu.com)是2004年开始发布的一款Linux发行版，多年处于最流行的Linux发行版前几名，在Linux桌面用户中已经超越曾经的王者Redhat Fedora。随着云计算生态的发展，Ubuntu也推出了针对云服务器的Server版，专门针对云计算进行了优化。

树莓派官方有点用心做硬件用脚做软件的感觉，迟迟都没有推出64位版本的Raspberry Pi OS的系统固件，而经过第三方测试，64位环境下才能够真正发挥出树莓派4的全部能力。

自2020年4月发布20.04版起，Ubuntu对树莓派提供了更好的支持。如果我们想搭建LNMP服务、内网域服务、区块链节点、Git仓库服务、NAS服务、容器服务、arm集群、分布式开发环境等等，在树莓派4上安装Ubuntu Server也成为了一个更适合的选择。

今天我们就讲一下如何在树莓派4B上安装Ubuntu Server。

## 03 准备工作

首先，我们得有树莓派^_^。

国内无论是马爸爸家还是东哥家都可以买得到树莓派4B的硬件，大部分商家还提供各种套餐。如果是开发用，我个人建议直接买主板、32G内存卡（MicroSD）和读卡器、国产电源、1根MicroHDMI=>HDMI转接线、网线。另外根据环境来选择使用裸板、散热片还是带小风扇的散热“装甲”。如果重度使用，建议选择带小风扇的散热方案。

如果是用于嵌入式开发、或者直接当服务器使用可以考虑不买HDMI转接线。有触控产品开发需求可选择有7寸液晶屏的套餐。如果当机器人或者嵌入式控制板用，直接买裸板就好了。如果想进行分布式开发或者自建arm集群，可以考虑一些商家提供的堆叠外壳。

接下来我们要准备安装软件的环境。先去Ubuntu官网下载预制好的树莓派4固件系统 [https://ubuntu.com/download/raspberry-pi](https://ubuntu.com/download/raspberry-pi)，记得选择Raspberry Pi 4的64-bit版本，然后去树莓派官网下载烧录工具Raspberry Pi Imager [https://www.raspberrypi.org/downloads/](https://www.raspberrypi.org/downloads/)，根据自己的操作系统选择对应的版本下载。

当然我们也可以用其他烧录工具进行烧录，例如dd、imagewriter也都可以，只是Raspberry Pi Imager更省事一些。

## 04 烧录系统固件

将用于安装系统固件的内存卡通过读卡器插到安装Raspberry Pi Imager的电脑上。

安装好Raspberry Pi Imager后，打开的界面如下：

选择“Choose OS”，会弹出来一个支持树莓派的系统列表，我们把列表滚动到最下面，选择“Usecustom”，然后找到我们下载的UbuntuServer固件，文件名应该类似：ubuntu-20.04.1-preinstalled-server-arm64+raspi.img.xz。

然后点击“WRITE”，等一会之后系统就烧录好了。

把内存卡插入到树莓派主板背面的TF插槽，接上电源、显示器，我们就可以开始配置系统了。

## 05 配置Ubuntu Server系统

接通电源后，系统进入引导状态，第一次会进行很多自检工作，并且把内存卡上未被固件占用的空间重新划分为可用的磁盘空间。

当出现登录界面Login提示符的时候，请不要着急登录系统，等个半分钟，这时候系统自带的Cloud-init进程会自动监测内网是否有Nova Metadata服务，熟悉Openstack的小伙伴一定知道这是什么。也就是假如内网有一个私有云的环境的话，Ubuntu Server会自动获取包括主机名、用户设置、IP地址等相关的配置信息。

如果没有获取到metadata信息，系统才真正进入可以登录的状态。默认的用户名和密码都是ubuntu，登录之后第一件事系统就会提示修改密码，修改后，就可以开始使用系统了。

如何配置Ubuntu系统的IP地址，网上有很多教程，我就不复述了，记得系统默认自带的网络渲染器是networkd即可。

接下来大家可能会配置一下国内的Ubuntu软件源，阿里云、网易、清华Tuna都是不错的选择。不过大家要注意ARM64的Ubuntu源类似是 [https://mirrors.aliyun.com/ubuntu-ports](https://mirrors.aliyun.com/ubuntu-ports)，而不是 [https://mirrors.aliyun.com/ubuntu](https://mirrors.aliyun.com/ubuntu)。

由于树莓派4内存资源毕竟有限，例如我就买了几个2GB的版本，所以我会停掉一些比较消耗内存且对我没什么用的服务。

我会：
```bash
$ sudo apt remove snapd cloud-init
```

还会：
```bash
$ sudo systemctl stop unattended-upgrades && sudo systemctl disable unattended-upgrades
```

## 06 桌面环境

如果大家想安装一些桌面环境的话，Ubuntu官方也给出了选择：
```bash
$ sudo apt search ubuntu-desktop
```
可以选择kde环境、gnome环境、xfce环境、lxde环境，个人推荐`lubuntu-desktop`，资源占用少，性能不错。

不过大家不要对树莓派4上跑桌面环境期待太多，在图形性能上还是跟pc机差距巨大，看优酷爱奇艺网易视频什么的能卡到你怀疑人生。但是装个sublime text或者vscodium之类的编辑器，配置个双屏幕开发一下php、python、nodejs程序，还是毫无问题的。

## 07 系统装好之后干点什么

正如前面所言，树莓派4可以当作一个很不错的服务器环境使用，我就用5块树莓派搭建了个分布式开发环境，部署了`k8s`集群、`tidb`集群、`tdengine`集群、`sawtooth`区块链节点等。在后续的文章中，我会详细介绍我是如何安装部署相关环境的。

接下来会是“在树莓派上”系列的下两篇《在树莓派上：安装CoreDNS服务》、《在树莓派上：安装部署TDengine时序数据库集群》，敬请期待。