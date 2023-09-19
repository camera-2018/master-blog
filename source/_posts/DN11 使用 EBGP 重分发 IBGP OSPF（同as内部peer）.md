---
title: DN11 使用 EBGP 重分发 IBGP OSPF（同as内部peer）
date: 2023-9-20 00:16:07
tags: DN11
---
# DN11 使用 EBGP 重分发 IBGP OSPF（同as内部peer）

## 连接 wg peer

1.本地机器

``` nginx
# wg_aws.conf
[Interface]
# 你的私钥
PrivateKey = <PRIVATEKAY>
# 你开的端口号（注意防火墙）
ListenPort = <PORT>
PostUp = /sbin/ip addr add dev %i <your ip> peer <peer ip>
# 不创建路由（由路由协议接管）
Table = off

[Peer]
# 你对端的ip和端口（外网机器）
Endpoint = <ENDPOINT>:<PORT>
# 你对端的公钥（外网机器）
PublicKey = <PUBLICKEY> 
AllowedIPs = 0.0.0.0/0

```

2.外网机器

``` nginx
# wg_ts_home.conf
[Interface]
# 你的私钥
PrivateKey = <PRIVATEKAY>
# 你开的端口号（注意防火墙）
ListenPort = <PORT>
PostUp = /sbin/ip addr add dev %i <your ip> peer <peer ip>
# 不创建路由（由路由协议接管）
Table = off
# wg 默认的 MTU 是 1420 （这里主要排除外网机器 MTU 异常的问题，洞里打洞不要配这个）
MTU = 1420

[Peer]
# 你对端的ip和端口（本地机器）
Endpoint = <ENDPOINT>:<PORT>
# 你对端的公钥（本地机器）
PublicKey = <PUBLICKEY> 
AllowedIPs = 0.0.0.0/0

```

## bird配置

创建

``` bash
/etc/bird.conf
/etc/bird/ospf.conf
/etc/bird/ibgp.conf
/etc/bird/bgp.conf
```

这四个文件

内网 openwrt 和外网机器上几乎需要相同的这四个文件（唯一不同的是 bgp neighbor 那里）

> 文中的 172.16.3.254、172.16.3.91 改为你本机（你的本地机器或者外网机器）的隧道ip
> 文中的 4211112243 改为你的 asn （别整出俩 as 号就行）

### /etc/bird.conf

``` nginx
log syslog all;
debug protocols all;

router id 172.16.3.254;

ipv4 table BGP_table;
ipv4 table OSPF_table;
ipv4 table IBGP_table;

protocol device{

}

protocol kernel{
    ipv4 {
        export all;
        import none;
    };
}

include "/etc/bird/ospf.conf";
include "/etc/bird/ibgp.conf";
include "/etc/bird/bgp.conf";

protocol pipe PIPE_OSPF_BGP{
    table BGP_table;
    peer table OSPF_table;
    import all;
    export none;
}

protocol pipe PIPE_BGP_MASTER4{
    table master4;
    peer table BGP_table;
    import filter {
        if source = RTS_STATIC then reject;
        krt_prefsrc = 172.16.3.254;
        accept;
    };
    export none;
}

protocol pipe PIPE_IBGP_BGP {
    table BGP_table;
    peer table IBGP_table;
    import filter {
	    if source = RTS_OSPF then reject;
        accept;
    };
    export filter {
        if source = RTS_STATIC then reject;
        accept;
    };
}

# 宣告 172.16.3.0/24 段
protocol static {
    ipv4 {
        table BGP_table;
        import all;
        export none;
    };

    # 只是为了让BGP的路由表里出现这条路由，不要担心 reject
    # 这个动作其实无所谓，reject 这个动作并不会被发到其他 AS
    # 后续将在导出到 master4 的时候删除这条路由，本地也不会有这一条
    # 请修改为你自己要宣告的段
    route 172.16.3.0/24 reject;
}
```

### /etc/bird/ibgp.conf
``` nginx
template bgp ibgp_peers {
    local as 4211112243;
    source address 172.16.3.254;
    multihop;
    ipv4 {
        next hop self;
        igp table OSPF_table;
        table IBGP_table;
        import all;
        export all;
    };
}

protocol bgp ibgp_aws from ibgp_peers {
    neighbor 172.16.3.91 internal;
}
```
## bgp.conf
```nginx
template bgp BGP_peers {
    local 172.16.3.254 as 4211112243;

    ipv4 {
        table BGP_table;
        import all;
        export filter {
            if source ~ [RTS_STATIC, RTS_BGP, RTS_OSPF] then accept;
            reject;
        };
    };
}

# collector 配置
protocol bgp collect_self {
    local as 4211112243;
    multihop;
    neighbor 172.16.255.1 as 4211110101;
    ipv4 {
        add paths tx;
        table BGP_table;
        import none;
        export filter {
            if source ~ [RTS_BGP,RTS_STATIC] then accept;
        };
    };
}

# 如下的 neighbor 配置应按照 wg 实际链接数量为准
# 从模板定义一个BGP邻居
# protocol bgp protocol 名称 from 模板名称
protocol bgp baimeow from BGP_peers {
   neighbor 172.16.4.254%baimeow as 4220084444;
}

# 如下为 如果你有物理链路的 BGP 配置 （没有的别抄）
# protocol bgp Meva_phy from BGP_peers {
#     source address 172.16.3.1;
#     neighbor 172.16.3.97%'br-lan' as 4211111024;
# 
#     ipv4 {
#     import filter {
#             if bgp_path ~ [= 4211111024 =] then bgp_local_pref = 120;
#             bgp_next_hop = 172.16.3.97;
#             accept;
#         };
#     };
# }
```

## ospf.conf
```nginx
protocol ospf v2 ospf_aws {
        ipv4 {
                table OSPF_table;
                export all;
                import all;
        };
        area 0.0.0.0 {
                interface "wg_aws" {
                        cost 20;
                        type ptp;
                };
                interface "lo" {
                        type bcast;
                };
        };
}
```