---
date: 2016-10-18 10:10
status: public
title: 'GFW 封锁方式以及翻墙手段汇总（转）
layout: post
---

# GFW 的封锁方法

1. 国内 DNS 服务器的缓存污染
2. 发往国外的 DNS 解析请求的拦截或篡改
3. IP 黑名单
4. 端口封锁，针对 OpenVPN，SSH，shadowsocks 等，有用到 DPI，多次换端口后封 IP
5. 关键字封锁，利用深度包检测（DPI），是主要方法。可用全站 https 应对，不过 GFW 因为无法识别 https，所以会针对所有 https 连接进行随机的中断

***

# GFW 的设备

大量硬件设备来自 Cisco

1. 入侵检测设备，在北京、上海、广州搭在总交换中心上做旁路监听
2. 动态路由设备，放在 ISP 处

***

# 翻墙方式

1. hosts，只能翻部分被 DNS 投毒的网站，而且随着 Google IP 被封禁得越来越多，已经很难翻了
2. 第三方 DNS，作用同 hosts，有风险，可能被再劫持
3. HTTP 代理，主要风险是明文传输（试过在海外 VPS 直接搭 HTTP 代理，用来上百度没问题，一打开 Google 马上被封）
4. HTTPS 代理，比起 HTTP 代理，有了一层 SSL 加密，安全许多，但 SSL 其实并不适合用于翻墙，它并不是专用于混淆的协议，shadowsocks 作者 clowwindy 有一篇文章详细阐述了这个观点
5. Tor，P2P 方式，安全性高。但 GFW 会钓鱼，伪造成 Tor 客户端进入 Tor 网络（obfsproxy 可以应对）。本身网络传输速度不快，不好用
6. Latern 基本同上，就个人使用体验来说，速度太慢
7. GoAgent，基于 GAE，不过 Google IP 被封得差不多了，所以基本不可用
8. GoProxy，GoAgent 的继任者，用于自己部署在 VPS 上
9. SSH，虽然传输安全，但握手阶段特征太明显，会被监控流量和连接数，所以基本只能用一小会儿，一般需要数小时重连一次。2012 年 GFW 加入 DPI 功能之后被封锁得更为严重了，一旦有 HTTP 流量传输就会被墙
10. VPN，工作在数据链路层，流量特征非常明显，出于商业上的考虑（大量在华跨国公司需要用到）所以才还能存活。但是自建的话，L2TP/PP2P/OpenVPN 基本没办法存活多久，只有 Cisco AnyConnect （服务端用开源的 ocserv）还可以用
11. Shadowsocks，这个名气够大了，不详述。如有需要，服务端建议部署shadowsocks-libev，相比其他语言的实现，这个版本开发更为活跃
12. ShadowVPN, GoHop, SoftEther VPN，都是具有较为强大加密/混淆功能的 VPN 实现，其中 ShadowVPN 因为作者 clowwindy 被请喝茶而删除项目代码，GoHop 功能强大但暂时只支持 Linux，SoftEther VPN 使用不是很方便（而且已经能被 GFW 探测到，见参考链接 13），所以目前都不是很流行
13. V2Ray，支持多种代理协议的代理软件包，功能强大
14. LightSword，基于 Node.js 的 SOCKS5 代理 / Apple NE 服务器，有 Linux / iOS / OS X 客户端，其协议参考了 shadowsocks，性能一般，iOS 客户端也只支持全局代理，但聊胜于无
15. IPv6，据说 GFW 暂时还未能有效封禁 IPv6 地址，所以在教育网里还能通过 IPv6 访问 Google/Facebook 等。不过这个应该只是暂时的

***

# 自己搭建无缝无痛的翻墙服务

1. 翻墙路由器，刷 OpenWrt
  用于自己在家上网。
  * VPN / shadowsocks + chnroutes / cow / meow 自动分流国内外 IP
  * Dnsmasq + pdnsd / ChinaDNS
  * 如果用了 Airport Extreme 之类的无法刷系统的路由器的话，可以接两层路由器，第一层用刷过 OpenWrt 的路由先翻一遍墙
2. 翻墙（HTTP）代理
在命令行终端里只能通过配置 http_proxy/https_proxy 变量来翻墙，所以需要一个翻墙代理。而且下述 PAC 文件、运营商描述文件都需要有一个代理作为基础。
  * HTTP 代理只能用国内服务器中转，直接部署在国外肯定被墙
  * 建议在海外服务器部署 shadowsocks 服务端，国内服务器部署 shadowsocks 客户端
  * shdaowsocks 代理转 http 代理可用 privoxy / polipo / cow / meow
  * privoxy / polipo 生成的代理是纯 HTTP 代理，cow / meow 则会自动学习已翻墙/未翻墙网站并更新列表、生成 PAC 等。但是 cow 因为默认认为网站未被墙，所以很多网站会尝试多次连接才能使用代理，速度有一定影响；而 meow 是白名单模式，可能会影响部分国内网站的加载速度
  * Surge for Mac 作为二级代理也非常好用
3. 提供远程 PAC 文件
Windows / Mac / Linux，以及 iOS / Android 5+ 在 WiFi 网络下都可以配置 PAC 代理，Android 4.x 可以用 SmartProxy 这款 App
  * 如果没什么特殊需求，最简单的是利用 gfwlist 生成，然后开个静态文件的 http 服务以便客户端访问
  * 不过更优的方法是使用 cow 生成的 PAC
4. 制作运营商描述文件
用于在 2G/3G/4G 下翻墙。不过不是太建议这个方法，根据个人经验，不论设置文件怎么写，总有些时候会出现莫名其妙的问题，到时候就只能删掉描述文件，在需要翻墙时再加回来，很是麻烦
而且对于使用了 HttpDNS 服务的各大 App 都无法兼容，包括但不仅限于微信朋友圈小视频、阿里旅行、滴滴出行地图、虾米、淘宝电影选座、网易云音乐等
5. AnyConnect
未越狱 iOS 设备，所有免费方案中，AnyConnect 是最方便的
  * 服务端使用 ocserv
  * AnyConnect 用路由表做分流，所以不太精确
  * ocserv 默认限制路由表最长为 64 条，但其实客户端最长可接受 200 条，所以可以通过修改源代码后编译的方式调整这个上限，参看这个帖子
  * 这里有个 CentOS & RHEL7 的安装脚本（已调整过路由表上限），即使不用这个脚本而自行安装，也可以参考其中给出的路由表
6. Surge
iOS / OS X 翻墙首选，支持 http、shadowsocks 代理，支持类 PAC 的配置，支持路由表，支持根据 IP 地址分流，在 OS X 上作为二级 HTTP 代理使用，同时也提供 SOCKS 代理
虽然配置麻烦，但是配置好之后可以说是一劳永逸
不过 $99 的售价有点让人难以承受
7. Potatso / Shadowrocket
Surge for iOS 的替代品，价格比 Surge 便宜得多
总的来说，功能不如 Surge 丰富、稳定性也较 Surge 稍微欠缺，但是基本还是够用的，不想花太多钱在翻墙上的话可以考虑这两者之一
8. 国际网络线路优化
  * 如果有国内服务器，可以直接用前述 shadowsocks 转 http 代理的方法，也可以直接设置 haproxy 转发 shadowsocks 代理
  * 如果不想买国内服务器的话，可以使用 微林的 vxTrans 服务 将代理进行端口转发，流量转发至电信 CN2 精品网，解决直连海外 VPS 太慢的问题
9. TCP 加速（防丢包）（不建议使用）
  * net-speeder，开源，简单粗暴地通过两倍发包来防止丢包，对丢包严重的网络有一定改善作用，不过有一些缺点：
     （1）双倍发包会造成流量翻倍
     （2）net-speeder 会造成 pptpd 等不支持双倍发包的网络软件无法正常使用
对小文件加速效果不明显
     （3）这种 TCP 优化机制一直存在争议，因为它实际实际上加剧了网络的拥堵，浪费掉了大量没必要的带宽
  * 锐速，比较老牌的 TCP 加速服务，闭源，比 net-speeder 智能，但不支持所有 VPS，闭源还要求 root 权限也让人有点不放心。而且，仍然会增加流量消耗，仍然被认为是不道德的，参见 shadowsocks 作者 clowwindy 在 V2EX 上的评论  

***

# 收费翻墙服务（不建议使用免费服务）

1. HTTP 代理以及 AnyConnect
  * 轻云
使用两年半，换过两次域名，另外还挂过两次，不过恢复时间较快。如果发现网站上不去了可以发任意内容邮件到 theqingyun@gmail.com 获取最新地址
  * 土行孙
使用一年，挂过两次。文档极为完善，对于不怎么翻墙、不熟悉翻墙工具的小白用户十分友好，另外学生凭校园邮箱有七折优惠
  * 熊猫翻滚
价格略贵，有微博客服，一般没什么问题，但小坑不断，典型的互联网服务。最近半年挂的次数不少……从微博时间线上就可以看出来
2. VPN
  * 云梯 VPN
  * Astrill，目前似乎速度已经不怎么样了，不推荐
  * VyprVPN
3. EurekaVPT
  * 没用过，口碑不错，价格略贵，需要邀请