

linux下的route命令，虽然在嵌入式开发中很少用到，但当涉及到简单静态路由开发时，还是有用的。此外，在配置linux网络时也有可能用到。本文旨在了解linux route命令及其使用。

###### 作用

route命令用于查看和配置linux内核路由表，也就是用来查看和配置linux的静态路由表。

###### 描述

route命令操作基于linux内核路由表，它的主要作用是创建一个静态路由让指定的一个主机或者一个网络通过一个网络接口，如eth0。当使用”add”或者”del”参数时，路由表被修改，如果没有参数，则显示路由表当前的内容。

###### 命令格式

route命令格式如下：

route [-nee]
route add [-net|-host] [网络或主机] netmask [mask] [gw|dev]
route del [-net|-host] [网络或主机] netmask [mask] [gw|dev]



###### 参数说明

- -n  ： 在输出的路由信息中，直接显示ip地址而不显示主机名，像netstat命令也有此参数；
- -ee ： 显示更详细的静态路由信息；

添加一条新路由 (add) 与删除一条路由 (del) 路由的相关参数：

- -net ：      目标地址是一个网络；
- -host ：     目标地址是一个主机；
- netmask ： 当添加一个网络路由时，需要使用网络掩码，决定网域的大小；
- gw ：       gateway 的缩写，路由数据包通过的网关，后面接的是IP地址，与dev不同；
- dev ：      如果只是要指定由哪一块网卡出去，则使用这个设定，后面接eth0 等网卡设备名称；

###### 实例

1、route add -net 192.168.2.0 netmask 255.255.255.0 dev eth0
添加一条到达192.168.2.0网络的路由，指定网络掩码为255.255.255.0,数据包通过网络接口eth0。

2、route add -net 192.57.66.0 netmask 255.255.255.0 gw 192.168.2.1
添加一条到达192.57.66.0网络的路由，指定网络掩码为255.255.255.0,数据包通过网关地址192.168.2.1。

3、route add -host 192.57.66.200 gw 192.168.2.1
所有去往192.57.66.200主机的数据包发往网关地址192.168.2.1。

4、route add default gw 192.168.1.1
添加一条默认网关，所有的数据包将被转发到192.168.1.1。

###### 路由表字段含义

[root@www ~]# route -n

```
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
192.168.1.0     0.0.0.0         255.255.255.0   U     0      0        0 eth0
169.254.0.0     0.0.0.0         255.255.0.0     U     1002   0        0 eth0
0.0.0.0         192.168.1.254   0.0.0.0         UG    0      0        0 eth0

[root@www ~]# route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
192.168.1.0     *               255.255.255.0   U     0      0        0 eth0
link-local      *               255.255.0.0     U     1002   0        0 eth0
default         192.168.1.254   0.0.0.0         UG    0      0        0 eth0
```

 Destination 目标

The destination network or destination host. 目标网络或目标主机（有-n参数时，只显示IP地址，否则显示主机名称，如上面的default）。

Gateway 网关
The gateway address or ‘*’ if none set.  网关地址，如果没有就显示星号。

Genmask 网络掩码
The netmask for the destination net; ‘255.255.255.255’ for a host destination and ‘0.0.0.0’ for the default route. 即，”255.255.255.255″表示一个主机。”0.0.0.0″表示网关。

 Flags Possible flags include 标志，常用的是U和G。

- ​       U (route is up) 路由启用
- ​       H (target is a host) 目标是主机
- ​       G (use gateway) 使用网关
- ​       R (reinstate route for dynamic routing)
- ​       D (dynamically installed by daemon or redirect)
- ​       M (modified from routing daemon or redirect)
- ​       A (installed by addrconf)
- ​       C (cache entry)
- ​       ! (reject route)

Metric 距离、跳数。暂无用。

The ‘distance’ to the target (usually counted in hops). It is
not used by recent kernels, but may be needed by routing dae-
mons.

Ref  不用管，恒为0。

Number of references to this route. (Not used in the Linux ker-
nel.)

 Use  该路由被使用的次数，可以粗略估计通向指定网络地址的网络流量。

Count of lookups for the route. Depending on the use of -F and
-C this will be either route cache misses (-F) or hits (-C).

Iface 接口，即eth0,eth0等网络接口名

Interface to which packets for this route will be sent.