# 前言

随着IPV6的逐渐普及，家宽也是可以获得至少4个IPV6子网而且可以直接从外网访问。理论上来讲内网穿透这类“过时技术”也会随着时间而逐渐消失。但是现实中总是有各种各样的问题。比如，80和443端口直接被运营商封禁；消费级路由器根本不给IPV6的配置入口；使用企业路由、openwrt后还是要定时检查日志防止黑客入侵；IPV6的IP会经常变动而一个DDNS服务只能获取当前设备的IP地址；等等。

现在来看最完美的方案还是组建虚拟局域网，可以同时解决大部分问题。但是由于“特殊的国情“，出国的VPN和回家的VPN协议并不通用，所以如何在软件中统一是最大的痛点。



# 代理网关方案

在家里配置openwrt并配置科学上网和IPsec之类的VPN服务。其他设备通过VPN服务组成虚拟局域网并把所有出国流量发送到openwrt统一处理。具体方案可以参考：[群晖搭建IPsec L2TP (让你的设备随时回家出国旅行)](https://www.bilibili.com/video/BV1mo4y1V7dL)

缺点是十分明显的，中心化的结构意味着只要openwrt节点下线直接无法正常使用。且出国流量受制于openwrt的吞吐量，家宽上传普遍只有30M，多个设备同时使用还是比较捉襟见肘的。

# 去中心化方案

[wireguard](https://www.wireguard.com/)是一个去中心化的VPN协议，轻量（简陋），快速，十分适合个人使用。

[clash meta](https://github.com/MetaCubeX/Clash.Meta)是从clash项目中fork的分支项目，继承了clash主要的功能并加入了一些新的特性。

clash meta支持添加wireguard出口，这一特性完美的解决了所有的问题。各设备的流量可以在本机完成分流并将回家流量直接转发到指定设备。去中心化确保了个别设备下线也不影响整个网络运行。

*由于wireguard的轻量（简陋）特点，没有公网IP的设备无法主动创建连接且每个设备的peers只能手动（配置起来十分繁琐）。十分建议假设几个中间节点保证网络畅通。更多wireguard的特性可以参考博客：[云原生实验室](https://icloudnative.io/)*

*clash meta可以作为服务器但是入口流量并不支持wireguard，即服务端只能使用一般的wireguard服务*

由于我使用的VPS走的AS9929线路，十分稳定且流量很多，所以我配置的中心节点服务。

## 服务端

如果不需要将服务器也加入虚拟内网则可以直接使用docker部署：[weejewel/wg-easy](https://github.com/wg-easy/wg-easy)

可以一件生成配置文件和二维码，十分方便

![](https://cdn.jsdelivr.net/gh/xxlgenius/blog_img/2023/07/02/ed362806295ee6b069ce9816c23ad50f.png)

如果需要将服务器纳入虚拟内网中则需要在linux中手动配置wireguard。[官方文档](https://www.wireguard.com/quickstart/)

- 安装wireguard

```bash
sudo sudo apt install wireguard
#开启IP转发
echo "net.ipv4.ip_forward = 1" >> /etc/sysctl.conf
sysctl -p
```

- 调整目录权限

```bash
cd /etc/wireguard/
sudo chmod 0777 /etc/wireguard

#调整目录默认权限
sudo umask 077
```

- 生成当前节点密钥

```bash
#生成私钥
wg genkey > server.key
#通过私钥生成公钥
wg pubkey < server.key > server.key.pub
```

- 生成客户端1&2的密钥（*密钥可以在任何wireguard客户端生成，可以将现有的密钥复制过来*）

```bash
#生成私钥
wg genkey > client1.key
#通过私钥生成公钥
wg pubkey < client1.key > client1.key.pub
```

- 将如下内容保存在`/etc/wireguard/wg0.conf`

```conf
[Interface]
PrivateKey = # 填写本机的privatekey 内容
Address = 172.16.0.1 #本机虚拟局域网IP

PostUp   = iptables -A FORWARD -i wg0 -j ACCEPT; iptables -A FORWARD -o wg0 -j ACCEPT; iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
PostDown = iptables -D FORWARD -i wg0 -j ACCEPT; iptables -D FORWARD -o wg0 -j ACCEPT; iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE
#注意eth0需要为本机网卡名称

ListenPort = 50814 # 监听端口
DNS = 1.1.1.1
MTU = 1420
[Peer]
PublicKey =   #客户端1的公钥
AllowedIPs = 172.16.0.0/24 #允许访问的ip段
```

- 设置开机启动

```bash
systemctl enable wg-quick@wg0
#启动wg0
wg-quick up wg0
#关闭wg0
wg-quick down wg0
```

- 客户端配置

```
[Interface]
PrivateKey = #client1的私钥
Address =  172.16.0.0/24#此处为peer规定的客户端IP
MTU = 1500

[Peer]
PublicKey =  #server的公钥
AllowedIPs =  172.16.0.0/24 #此处为允许的服务器IP
Endpoint = IP:50814 #服务器对端IP+端口
```

- 添加新的客户端

```bash
#生成私钥
wg genkey > client2.key
#通过私钥生成公钥
wg pubkey < client2.key > client2.key.pub
#将peer公钥加入wg0.conf配置
echo "
[Peer]
PublicKey =  $(cat client2.key.pub)  #公钥
AllowedIPs = 172.16.0.0/24 #客户端Client2所使用的IP" >> wg0.conf
```



## 客户端

参考wiki：[使用Meta的客户端](https://docs.metacubex.one/used)

## Clash 规则配置

