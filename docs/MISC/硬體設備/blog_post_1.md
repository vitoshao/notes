---
title: Router、Switch、Hub
layout: default
parent: 硬體設備
nav_order: 1
description: "Router、Switch、Hub"
date: 2013-03-12
tags: [MISC,硬體設備]
postid: "4560008318346650441"
---
http://www.wretch.cc/blog/dagron/9498732      
# Router ; Switch ; HUB 差異

#### Switch

中譯：交換器 或是 橋接器(Bridge)  
功能：傳送封包，連接PC  
作用層：TCP/IP的第二層(MAC)  
補充：市面上有所謂的無網管功能及有網管功能的交換器  
無網管功能的交換器有的地方會叫做Switch HUB  
跟一般的交換器少了網路管理、VLAN、STP及Multicast管理的功能  

#### HUB

中譯：集線器  
功能：連接多台PC  
作用層：TCP/IP的第一層  

#### Router

中譯：路由器  
功能：交換不同網段的封包、決定封包的傳送方向及路徑  
作用層：TCP/IP的第三層及第四層(IP和TCP/UDP Port)  
補充：市面上所售的IP分享器，也有人稱做ROUTER  
不過功能上面就差很多了....  

#### Layer3 Switch(MultiLayer3 Switch)

中譯：第三層交換器 或是 多層交換器  
功能：同Switch，不過多了 Routing(路由) 的功能  
作用層：TCP/IP的第二層及第三、四層(MAC、IP及TCP/UDP Port)  
補充：雖然Layer3 Switch也跟Router一樣有路由的功能  
不過，仍然無法當作Router  
因為現在的Layer3 Switch仍然無法做NAT(網路位置轉譯)功能  
除非你要用像是Cisco 65XX等級的Layer3 Switch