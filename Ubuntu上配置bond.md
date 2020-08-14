# Ubuntu上配置bond

## 两台机器直连

##### 配置设定文件

```
vim /etc/sysconfig/network-scripts/ifcfg-bond0  

DEVICE=bond0  
BOOTPROTO=none  
ONBOOT=yes  
IPADDR=192.168.100.1  
NETMASK=255.255.255.0  
NETWORK=192.168.100.0  
BROADCAST=192.168.100.255  
  
vim /etc/sysconfig/network-scripts/ifcfg-enp3s0  

DEVICE=enp3s0  
BOOTPROTO=none  
MASTER=bond0  
SLAVE=yes

vim /etc/sysconfig/network-scripts/ifcfg-enp4s0  

DEVICE=enp4s0 
BOOTPROTO=none  
MASTER=bond0  
SLAVE=yes 
```



##### 修改interfaces文件

```
vim /etc/network/interfaces

auto enp3s0
iface enp3s0 inet manual
bond-master bond0

auto enp4s0 
iface enp4s0 inet manual
bond-master bond0

auto bond0                            #绑定的网卡名
iface bond0 inet static               #静态地址
address xx.xx.xx.xx                   #IP
netmask xx.xx.xx.xx                   #子网
bond-mode 0                                                  
bond-slaves enp3s0 enp4s0

```



##### 修改modprobe相关文件

```
vim /etc/modprobe.d/bonding.conf 
[+]alias bond0 bonding 
[+]options bonding mode=0 miimon=200 

加载模块
modprobe bonding 

确认模块加载成功
lsmod | grep bonding 
bonding 100065 0 

找不到模块需要安装
cd /home/zhangzh/deb-4.15.18/
dpkg -i *.deb
```



##### 重启网络

```
/etc/init.d/networking restart 

cat /proc/net/bonding/bond0  
Ethernet Channel Bonding Driver: v3.7.1 (April 27, 2011)

Bonding Mode: load balancing (round-robin)
MII Status: up
MII Polling Interval (ms): 0
Up Delay (ms): 0
Down Delay (ms): 0

Slave Interface: enp3s0
MII Status: up
Speed: 1000 Mbps
Duplex: full
Link Failure Count: 0
Permanent HW addr: 00:90:0b:5f:03:c0
Slave queue ID: 0

Slave Interface: enp4s0
MII Status: up
Speed: 1000 Mbps
Duplex: full
Link Failure Count: 0
Permanent HW addr: 00:90:0b:5f:03:c1
Slave queue ID: 0
  
```



##### 修改route表

删除和多余网卡，仅保留bond

```
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         10.0.80.254     0.0.0.0         UG    0      0        0 enp2s0
10.0.80.0       *               255.255.255.0   U     0      0        0 enp2s0
172.17.0.0      *               255.255.0.0     U     0      0        0 docker0
[-]192.168.100.0   *            255.255.255.0   U     0      0        0 enp3s0
192.168.100.0   *               255.255.255.0   U     0      0        0 bond0
[-]192.168.100.0   *            255.255.255.0   U     0      0        0 enp4s0

```





```
-----------------------------------------------------------
Server listening on 5201
-----------------------------------------------------------
Accepted connection from 192.168.100.1, port 36858
[  5] local 192.168.100.2 port 5201 connected to 192.168.100.1 port 36860
[ ID] Interval           Transfer     Bitrate
[  5]   0.00-1.00   sec   206 MBytes  1.72 Gbits/sec
[  5]   1.00-2.00   sec   226 MBytes  1.90 Gbits/sec
[  5]   2.00-3.00   sec   226 MBytes  1.90 Gbits/sec
[  5]   3.00-4.00   sec   226 MBytes  1.90 Gbits/sec
[  5]   4.00-5.00   sec   226 MBytes  1.90 Gbits/sec
[  5]   5.00-6.00   sec   215 MBytes  1.81 Gbits/sec
[  5]   6.00-7.00   sec   215 MBytes  1.81 Gbits/sec
[  5]   7.00-8.00   sec   225 MBytes  1.89 Gbits/sec
[  5]   8.00-9.00   sec   226 MBytes  1.90 Gbits/sec
[  5]   9.00-10.00  sec   226 MBytes  1.90 Gbits/sec
[  5]  10.00-10.04  sec  9.43 MBytes  1.90 Gbits/sec
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate
[  5]   0.00-10.04  sec  2.18 GBytes  1.86 Gbits/sec                  receiver
-----------------------------------------------------------



Connecting to host 192.168.100.2, port 5201
[  4] local 192.168.100.1 port 36860 connected to 192.168.100.2 port 5201
[ ID] Interval           Transfer     Bandwidth       Retr  Cwnd
[  4]   0.00-1.00   sec   217 MBytes  1.82 Gbits/sec   24    456 KBytes
[  4]   1.00-2.00   sec   227 MBytes  1.90 Gbits/sec    0    456 KBytes
[  4]   2.00-3.00   sec   226 MBytes  1.90 Gbits/sec    0    456 KBytes
[  4]   3.00-4.00   sec   226 MBytes  1.89 Gbits/sec    0    456 KBytes
[  4]   4.00-5.00   sec   227 MBytes  1.90 Gbits/sec    0    456 KBytes
[  4]   5.00-6.00   sec   216 MBytes  1.81 Gbits/sec  196    483 KBytes
[  4]   6.00-7.00   sec   215 MBytes  1.80 Gbits/sec  171    436 KBytes
[  4]   7.00-8.00   sec   225 MBytes  1.89 Gbits/sec    0    452 KBytes
[  4]   8.00-9.00   sec   226 MBytes  1.90 Gbits/sec    0    459 KBytes
[  4]   9.00-10.00  sec   226 MBytes  1.90 Gbits/sec    0    459 KBytes
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Retr
[  4]   0.00-10.00  sec  2.18 GBytes  1.87 Gbits/sec  391             sender
[  4]   0.00-10.00  sec  2.18 GBytes  1.87 Gbits/sec                  receiver

iperf Done.

```

