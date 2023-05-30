---
title: Nas折腾记
date: 2023-05-28T23:05:03+08:00
draft: false
tags:
  - nas
categories:
  - nas
---

NAS(Network Attached Storage)是一种存储设备，它连接到局域网中的路由器或交换机，允许多台计算机同时访问和共享存储空间。
NAS设备通常运行专用操作系统，提供文件共享、数据备份、远程访问等功能。
它们还可以作为媒体服务器，通过 DLNA 或其他协议将音频、视频和图片共享到网络中的其他设备。

人们搭建NAS通常是为了集中存储和管理家庭或办公室中的数据，以便多台计算机或设备可以访问和共享这些数据。
此外，NAS还可以提供数据备份、远程访问和媒体共享等功能，使用户能够随时随地访问自己的数据。
相比于传统的外部硬盘或USB存储设备，NAS具有更高的可靠性和扩展性，可以满足用户不断增长的存储需求。

常见的搭建NAS的方案包括以下几种：

  1. 购买专用NAS设备：市面上有许多厂商提供各种规格和价格的NAS设备，用户只需要购买合适的设备并按照说明书进行设置即可。
  2. 将旧电脑改造成NAS：用户可以使用旧电脑，安装专门的NAS操作系统，如FreeNAS、OpenMediaVault等，将其转变为功能强大的NAS设备。
  3. 使用路由器自带的USB接口：一些路由器提供了USB接口，用户可以将外部硬盘或U盘连接到路由器上，通过路由器提供的NAS功能实现文件共享和远程访问。
  4. 使用云存储服务：用户可以使用云存储服务，如Dropbox、Google Drive、OneDrive等，将数据存储在云端，并通过客户端软件或Web界面访问和共享数据。

本文介绍将旧设备改造为NAS设备，实现数据存储、家庭影院和个人办公等功能。

## 旧设备介绍

- CPU: 4 x Intel(R) Core(TM) i5-7500 CPU @ 3.40GHz
- 内存: DDR4 8G X 2
- 主板: ASUS PRIME B250M-K
- 硬盘: 6X16T、2X14T
- 独显: GTX 1050Ti

## 系统搭建思路

NAS系统搭建分为两种思路，一种直接将NAS系统安装到裸机中，另一种则借助虚拟化系统，将NAS系统安装到虚拟化系统中。
个人使用的是第二种思路，裸机安装[PVE](https://www.proxmox.com/en/)，然后借助PVE的虚拟化功能安装[TrueNAS Scale](https://www.truenas.com/truenas-scale/)。
该方案有以下几种好处：

- 裸机安装PVE，将应用安装到虚拟系统中，实现了NAS系统和平台的隔离，保证了系统的稳定性，避免因为NAS系统的各种问题导致系统奔溃，需要反复折腾宿主机的情况。
- 借助PVE的便捷性，可以方便的安装各种系统和应用，不用仅仅依赖于NAS系统的功能，提升了应用的扩展性。

### PVE系统安装

1. 下载系统镜像，[PVE7.4](https://enterprise.proxmox.com/iso/proxmox-ve_7.4-1.iso)。
2. 利用[Rufus](https://github.com/pbatard/rufus/releases)将系统刻录进U盘中。
3. 通过U盘安装PVE系统到物理机中（安装的过程中，建议保证网络联通，可以直接设置PVE系统的ip地址）。
4. 安装完成之后，通过浏览器登录PVE管理端，通过Shell替换仓库源为国内镜像，建议使用[中科大源](https://mirrors.ustc.edu.cn/)。

![pve管理端](/img/nas/pve_dashboard.png)

### 安装NAS系统，TrueNAS Scale

1. 从官网下载系统镜像[TrueNAS Scale](https://www.truenas.com/download-truenas-scale/)。
2. 镜像下载完成后，通过PVE管理端，将镜像导入PVE系统中。
3. PVE新建虚拟机，安装TrueNAS Scale系统，在配置硬盘的时候，只创建系统的安装硬盘就好。
4. 在PVE管理端，通过shell，执行命令将数据硬盘整体映射到TrueNAS的虚拟机中。
5. 在PVE管理端，启动TrueNAS虚拟机，完成TrueNAS系统安装。
6. 安装完成之后，登录TrueNAS系统，完成系统的配置。

![truenas安装完之后效果](/img/nas/pve_truenas.png)

#### PVE新建TrueNAS虚拟机

![truenas虚拟机](/img/nas/pve_truenas_general.png)

![truenas虚拟机](/img/nas/pve_truenas_disk.png)

因为后续会将整个硬盘都映射到TrueNAS虚拟机中，所以硬盘界面，不用添加硬盘，使用默认设置就好。

![truenas虚拟机](/img/nas/pve_truenas_shell.png)

上述硬盘映射命令格式如下：

```shell
# xxxxx为硬盘的id
qm set <vmid> --sata[n] /dev/disk/by-id/xxxxx
```

![truenas虚拟机](/img/nas/pve_truenas_vm.png)

上图为虚拟机完成创建之后的效果，其中有六块SATA硬盘。

### 安装Ubuntu系统

Ubuntu系统做为常用的linux发行版，用户数量大，资料查找方便，作为我们的`应用`系统比较合适。
该系统的安装跟常规PVE系统的安装基本一致，这里不做过多记录。最终安装之后效果如下：

![Ubuntu系统](/img/nas/pve_ubuntu.png)

## 总结

本文描述了通过PVE系统，搭建TrueNAS Scale系统和Ubuntu系统，其中前者用来做数据存储保证稳定，后者用来做应用部署，保证使用灵活方便。
另外使用PVE的LXC功能，我们还可以将一部分对性能和稳定有要求的业务跑在LXC的容器中。
PVE系统不仅为我们提供了稳定性，同时也为我们的整体设计提供了极大的灵活性。
