title: p2p 搜索
date: 2014-12-07 23:11:13
updated: 2014-12-07 23:11:22
tags:
- p2p
- web
categories: 
- p2p
---

###ed2k 协议
登录

>msg_log = struct.pack('<BIB16sIHIBHBIBHBI',0xe3,43,0x01,'1234567897654321',0,5662,2,3,1,0x11,0x3c,3,1,0xfb,3<<24|2<<17|3<<10|1<<7)  
0xe3 1byte protocol  
43 4字节 数据长度 不包括次字段之前及其自身的长度（总共5个字节）  
0x01 1字节 消息类型 这个表示 登录  
'1234567897654321' 16字节的hash 意义不明  
0 4字节 用户id 成功登录会返回一个id值  
5662 2字节的tcp端口值 服务器连接客户端（内网机器没有端口映射无法访问，导致分配一个 lowid ，区别highid）  
2 4字节的tag count（这里后面有2个 int型的tag）  
0x3c 所谓的edonkeyversion 规定值貌似  
3<<24|2<<17|3<<10|1<<7 这里必须设置此项表示客户端版本，否则会被拒绝连接服务器（ERROR : Your edonkey client is too old, please update it，后续的idchange消息等不会收到）

(to be continued...)
###服务器文件搜索
###kad网络搜索



