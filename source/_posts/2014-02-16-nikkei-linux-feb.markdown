---
layout: post
title: "日経Linux 2月号を読んだ"
description: "日経Linux 2月号を読んだ"
category: "kernel"
tags: ["kernel", "linux"]
---

## 概要
[日経Linux 2月号(2014)](http://www.amazon.co.jp/%E6%97%A5%E7%B5%8C-Linux-%E3%83%AA%E3%83%8A%E3%83%83%E3%82%AF%E3%82%B9-2014%E5%B9%B4-02%E6%9C%88%E5%8F%B7/dp/B00H8X84E6)
に、カーネルの特集記事があったので買って読んでみた。  
今まで知らなかった部分とか、便利そうなことについて、以下に記載する。  

## メモリ管理
buddy システム, スラブアロケータについて書かれていた。  
この本よりもっと詳しく書かれたページを発見したのでコチラを参照。  
[http://www.coins.tsukuba.ac.jp/~yas/coins/os2-2011/2012-01-17/](http://www.coins.tsukuba.ac.jp/~yas/coins/os2-2011/2012-01-17/)


## systemtap
system tap とは [https://access.redhat.com/site/ja/node/289453](https://access.redhat.com/site/ja/node/289453) にある通り  

{% blockquote %}
実行している Linux カーネルで簡易情報を取得できるようにする革新的なツールです。
{% endblockquote %}
である。  

### install 方法(@CentOS)
vagrant で建てた CentOS を使ってやってみる。  
Vagrantfile 的に  
config.vm.box = "Berkshelf-CentOS-6.3-x86_64-minimal"  
この box を使っている人は以下の通りでインストールできると思います。  

{% highlight bash %}
sudo yum -y install systemtap
cd /tmp
wget http://debuginfo.centos.org/6/x86_64/kernel-debuginfo-common-x86_64-2.6.32-279.el6.x86_64.rpm
wget http://debuginfo.centos.org/6/x86_64/kernel-debug-debuginfo-2.6.32-279.el6.x86_64.rpm
sudo yum install kernel-debuginfo-common-x86_64-2.6.32-279.el6.x86_64.rpm
sudo yum install kernel-debug-debuginfo-2.6.32-279.el6.x86_64.rpm

# これで stap がつかえる
stap

## 追記
(これだと probe.kernel が使えないっぽい.. 要調査。)
{% endhighlight %}

あとは
[http://sourceware.org/systemtap/examples/](http://sourceware.org/systemtap/examples/)  
ここから  
vm.tracepoints.stp や, sched_switch.stp を落として来て実行できる。  

(参考)  
[http://stts.hatenablog.com/entry/20090614/1244952417](http://stts.hatenablog.com/entry/20090614/1244952417)  

## プロセスのメモリ割付について
/proc/[processId]/maps (これをメモリーマップと呼ぶ。)を参照すれば、どのようにメモリが割り付けられているかがわかる。  

{% highlight bash %}

vi hoge.c
-----
#include <stdio.h>

int main(void) {
  char hoge[4096] = "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa";

printf("sleep start\n");
  sleep(500000);
printf("sleep stop\n");
  return 0;
}

ps aux | grep a.out
vagrant  19157  0.0  0.0   3928   408 pts/1    S+   04:41   0:00 ./a.out
vagrant  19164  0.0  0.0 107456   948 pts/0    S+   04:42   0:00 grep a.out


cd /proc/
cat /proc/19157/maps

00400000-00402000 r-xp 00000000 fd:00 3590                               /tmp/a.out
00601000-00602000 rw-p 00001000 fd:00 3590                               /tmp/a.out
7fdf912e5000-7fdf91470000 r-xp 00000000 fd:00 3071                       /lib64/libc-2.12.so
7fdf91470000-7fdf9166f000 ---p 0018b000 fd:00 3071                       /lib64/libc-2.12.so
7fdf9166f000-7fdf91673000 r--p 0018a000 fd:00 3071                       /lib64/libc-2.12.so
7fdf91673000-7fdf91674000 rw-p 0018e000 fd:00 3071                       /lib64/libc-2.12.so
7fdf91674000-7fdf91679000 rw-p 00000000 00:00 0
7fdf91679000-7fdf91699000 r-xp 00000000 fd:00 3437                       /lib64/ld-2.12.so
7fdf9188e000-7fdf91891000 rw-p 00000000 00:00 0
7fdf91896000-7fdf91898000 rw-p 00000000 00:00 0
7fdf91898000-7fdf91899000 r--p 0001f000 fd:00 3437                       /lib64/ld-2.12.so
7fdf91899000-7fdf9189a000 rw-p 00020000 fd:00 3437                       /lib64/ld-2.12.so
7fdf9189a000-7fdf9189b000 rw-p 00000000 00:00 0
7fff2a0dc000-7fff2a0f1000 rw-p 00000000 00:00 0                          [stack]
7fff2a1b4000-7fff2a1b5000 r-xp 00000000 00:00 0                          [vdso]
ffffffffff600000-ffffffffff601000 r-xp 00000000 00:00 0                  [vsyscall]
{% endhighlight %}

下から見ると、 systemcall が呼ばれる領域(カーネル仮想空間)があるのと、 stack が積まれる領域があるのと、  
shared object がリンクされている領域と、 テキスト(プログラム)が格納されている領域があるのが見て取れる。  

左端から、仮想アドレス、アクセス属性、データオフセット、 デバイスのメジャー番号とマイナー番号、 inode, ファイル名とのこと。  


## カーネル時計
Linux カーネルは 起動時には、PC の内蔵時計を使用するが、それ移行は独自のカーネル時計を使用する。  
ミリ秒からナノ秒の処理が必要であるため。  

## I/Oスケジューラー
入出力するブロックデータを並び替えて入出力を高速化できるもの。  

[http://city.hokkai.or.jp/~hachikun/IOScheduler.html](http://city.hokkai.or.jp/~hachikun/IOScheduler.html)  
[http://www.valinux.co.jp/technologylibrary/document/linuxkarnel/cfq0001/]([http://city.hokkai.or.jp/~hachikun/IOScheduler.html])
