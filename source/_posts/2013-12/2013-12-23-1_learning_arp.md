---
layout: post
title: "ARP について調べた"
description: ""
category: "network"
tags: [network]
---
{% include JB/setup %}

## 目次
* 
{:toc}

## ARPとは
Address Resolution Protocol の略で、IP アドレスから MAC アドレスを求める時に  
使用されるプロトコルです。  
TCP などの通信を行うときには送信元、送信先の MAC アドレスが必要なので、  
事前に ARP プロトコルを用いた MAC アドレスの解決が行われています。  
ARP プロトコルをやりとりするコマンドが用意されていて, それが arp コマンドです。  
しかし、毎通信ごとに ARP プロトコルを用いて MAC アドレスを求めるのは無駄です。  
なので、通常、 内部的に arp テーブルを保持してそれを使用します。  
ARP のやり取りには broadcast が使われます。  

## arp コマンドについて

$ man arp


{% blockquote %}
 ARP(8)                    BSD System Manager's Manual                   ARP(8)
 
 NAME
      arp -- address resolution display and control
 
 SYNOPSIS
      arp [-n] [-i interface] hostname
      arp [-n] [-i interface] [-l] -a
      arp -d hostname [pub] [ifscope interface]
      arp -d [-i interface] -a
      arp -s hostname ether_addr [temp] [reject] [blackhole] [pub [only]] [ifscope interface]
      arp -S hostname ether_addr [temp] [reject] [blackhole] [pub [only]] [ifscope interface]
      arp -f filename
 
 DESCRIPTION
      The arp utility displays and modifies the Internet-to-Ethernet address translation tables used by the address
      resolution protocol (arp(4)).  With no flags, the program displays the current ARP entry for hostname.  The
      host may be specified by name or by number, using Internet dot notation.
{% endblockquote %}

## arp で実際に mac osx で実験
ここでは、 arp コマンドテーブルを表示したあと、  
一度削除して、http通信を行った後に、テーブルを再度確認してみる。  

{% blockquote %}
 /net% arp -a
 ? (192.168.1.1) at 0:1e:31:9e:c3:b4 on en0 ifscope [ethernet]
 ? (192.168.1.255) at ff:ff:ff:ff:ff:ff on en0 ifscope [ethernet]
 /net% arp -d 192.168.1.1
 arp: writing to routing socket: Operation not permitted
 /net% sudo arp -d 192.168.1.1
 Password:
 192.168.1.1 (192.168.1.1) deleted
 /net% arp -a
 ? (192.168.1.255) at ff:ff:ff:ff:ff:ff on en0 ifscope [ethernet]
 ? (192.168.1.1) at 0:1e:31:9e:c3:b4 on en0 ifscope [ethernet]
 
 /net% telnet  mixi.jp 80
 Trying 110.44.179.199...
 Connected to mixi.jp.
 Escape character is '^]'.
 ^]
 telnet> quit
 Connection closed.
 /net% arp -a
 ? (192.168.1.1) at 0:1e:31:9e:c3:b4 on en0 ifscope [ethernet]
 ? (192.168.1.255) at ff:ff:ff:ff:ff:ff on en0 ifscope [ethernet]
{% endblockquote %}

## 所感
arp -d のあとすぐに確認しても、192.168.1.1 のキャッシュは存在した。  
おそらく、他のアプリケーションによる通信があるせい。  

## 参考文献
マスタリングTCP/IP入門(5章)

## あわせてよみたい
[RFC826](ftp://ftp.rfc-editor.org/in-notes/rfc826.txt)

