---
layout: post
title:  "Centos7-Min 安装KVM"
categories: kvm
tags: kvm
author: zhangxx
---


{:toc}


## centos7 安装KVM
```bash
yum -y install qemu-kvm python-virtinst libvirt libvirt-python virt-manager libguestfs-tools bridge-utils virt-install
yum -y install qemu-kvm libvirt virt-install bridge-utils 

[root@192 network-scripts]# vim ifcfg-br0 
  
TYPE=Bridge // 
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=static
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=br0
#UUID=12022180-fe57-4a11-bb08-567f392f4ed6 // 
DEVICE=br0
ONBOOT=yes

IPADDR=192.168.1.10
NWTMASK=255.255.255.0
GATEWAY=192.168.1.1
DNS1=8.8.8.8
DNS2=114.114.114.114

[root@192 network-scripts]# cat ifcfg-ens4s
TYPE=Ethernet
BRIDGE=br0
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=static
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=enp4s0
UUID=12022180-fe57-4a11-bb08-567f392f4ed6
DEVICE=enp4s0
ONBOOT=yes

#IPADDR=192.168.1.10
#NWTMASK=255.255.255.0
#GATEWAY=192.168.1.1
```

## 安装 Dhcp 服务端
```bash
yum -y install dhcp

[root@meigea network-scripts]# cat /etc/dhcp/dhcpd.conf 
#
# DHCP Server Configuration file.
#   see /usr/share/doc/dhcp*/dhcpd.conf.example
#   see dhcpd.conf(5) man page
#
#
#
#

subnet 172.18.32.0 netmask 255.255.255.0 {
        range 172.18.32.100 172.18.32.254;
        option domain-name-servers 172.18.32.1;
        option domain-name "kac.fun";
        option routers 172.18.32.1;
        option broadcast-address 172.18.32.255;
        default-lease-time 600;
        max-lease-time 7200;
}

```

- 端口NAT 使用网络
> 将192.168.8.158的网络转发给 172网段。
```bash
iptables -t nat -A POSTROUTING \
-s 172.18.32.0/24 -j SNAT --to 192.168.8.158
```


## 4）安装libvirt及kvm


```bash
yum -y install libcanberra-gtk2 qemu-kvm.x86_64 qemu-kvm-tools.x86_64 \ 
libvirt.x86_64 libvirt-cim.x86_64 libvirt-client.x86_64 \ 
libvirt-java.noarch  libvirt-python.x86_64 libiscsi-1.7.0-5.el6.x86_64 \
dbus-devel  virt-clone tunctl virt-manager libvirt libvirt-python python-virtinst

systemctl start libvirtd
systemctl enable libvirtd


#systemctl status libvirtd
#systemctl is-enabled libvirtd

```

## 安装第一个 centos7 

```bash
cd /mnt/ && wget http://mirrors.163.com/centos/7.6.1810/isos/x86_64/CentOS-7-x86_64-Minimal-1810.iso
virt-install \
--virt-type=kvm \
--name=centos7 \
--vcpus=1 \
--memory=1024 \
--location=/mnt/CentOS-7-x86_64-Minimal-1810.iso \
--disk path=/home/vms/centos7.qcow2,size=30,format=qcow2 \
--network bridge=br0 \
--graphics none \
--extra-args='console=ttyS0' \
--force
```
> 注意一步步安装过程中设置网络。


## 宿主机和内网主机端口映射
```bash
iptables -t nat -A PREROUTING \
-d 192.168.1.222 -p tcp --dport 2280 \
-j DNAT --to-dest 192.168.1.133:80
```

## 参考
- [https://www.cnblogs.com/liujiacai/p/9130701.html](https://www.cnblogs.com/liujiacai/p/9130701.html)
- [Centos7.4安装kvm虚拟机（使用virt-manager管理）](https://www.centos.bz/2018/02/centos7-4%E5%AE%89%E8%A3%85kvm%E8%99%9A%E6%8B%9F%E6%9C%BA%EF%BC%88%E4%BD%BF%E7%94%A8virt-manager%E7%AE%A1%E7%90%86%EF%BC%89/)


## KVM 命令行使用
```shell
virt-install \
--virt-type=kvm \
--name=ub1604 \
--vcpus=1 \
--memory=1024 \
--location=/mnt/ubuntu-16.04.6-server-amd64.iso \
--disk path=/home/vms/ub1604.qcow2,size=30,format=qcow2 \
--network bridge=br0 \
--graphics none \
--extra-args='console=ttyS0' \
--force
```

## 参考
- [KVM格式等](https://github.com/premepen/pen-others/issues/1)
- [KVM-VNC](https://github.com/xx-sec/xx-sec/issues/5)

## 后记
- 如果安装 KVM 不用这么麻烦，直接安装最全的桌面环境，可以将整套工具一次安装完。
- 强行修改网卡名称
```
/usr/lib/udev/rules.d/60-net.rules

修改位置 ATTR{address}='MAC'， NAME=eth0;

grub 修改无效、。
```


