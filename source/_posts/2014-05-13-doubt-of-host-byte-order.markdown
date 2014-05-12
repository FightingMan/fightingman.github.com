---
layout: post
title: "socket编程字节排序的诡异问题"
date: 2014-05-13 03:00
comments: true
categories: Tcp/Ip C/C++
---
通常大家这样绑定某个地址
{% codeblock server.cpp %}
    ...
    serv_addr.sin_addr.s_addr = htonl(INADDR_ANY);
    ...
{% endcodeblock %}
如果网卡多，只想在某一网卡的某个地址上bind怎么办呢？就需要指定s_addr的地址
比如:192.168.1.102

{% codeblock main.cpp %}
    //main.cpp
    include <iostream>
    #include "server.h"
    using namespace std;

    int main(int argc, char* argv[]) {
        try {
            if (argc != 4)  {
                cerr << "Usage: ftsrv <address> <port> <doc_root>\n";
                return 1;
            }
            ftsrv::server s(argv[1], argv[2], argv[3]);
            s.run();
        } catch(exception& e) {
            cerr <<
                "exception:" <<e.what()<< "\n";
        }   

        return0;
    }
    //server.h
    ...
    explicit server(const string& address_, const string& port_, const string& doc_root_);
    ...
{% endcodeblock %}
<!--more-->
接着我们在server.cpp具体函数里面实现转换，将addr转为in_addr_t类型（uint32）
{% codeblock main.cpp %}
    ...
    struct in_addr address;
    if ((inet_pton(AF_INET, address_.c_str(), &address)) != 1) {
        cerr << "Error on inet_pton";
    }
    //由于s_addr必须是大端字节序
    address.s_addr = htonl(address.s_addr);
    ...
    然后起socket执行一系列函数
    ...
    struct sockaddr_in serv_addr, cli_addr;
    serv_addr.sin_family = AF_INET;
    serv_addr.sin_addr = address;
    serv_addr.sin_port = htons(port);
    if (bind(sockfd, (struct sockaddr *) &serv_addr, sizeof(serv_addr)) != 0) {
        cerr << "Error on binding";
    }
    ...
{% endcodeblock %}
然后始bind不上,找了半天原因，我电脑主机字节序默认是按小端字节排序的.
如何检查自己的系统的主机字节序很简单：
{% codeblock main.c %}
    union {
        short  s;
        char   c[sizeof(short)];
    } un;

    un.s = 0x0102;
    if (sizeof(short) == 2) {
        if (un.c[0] == 1 && un.c[1] == 2)
            printf("big-endian\n");
        else if (un.c[0] == 2 &&
                un.c[1] == 1)
            printf("little-endian\n");
        else
            printf("unknown\n");
    } else {
        printf("sizeof(short) = %d\n", sizeof(short));
    }
{% endcodeblock %}
最后在inet.h的宏定义中看到
{% codeblock inet.h %}
    #ifndef INADDR_ANY
    # define INADDR_ANY ((unsigned long int) 0x00000000)
    #endif /* INADDR_ANY */

    #ifndef INADDR_LOOPBACK
    # define INADDR_LOOPBACK    ((unsigned long int) 0x7f000001)
    #endif /* INADDR_LOOPBACK */

    #ifndef INADDR_NONE
    # define INADDR_NONE    0xffffffff
    #endif /* INADDR_NONE */
{% endcodeblock %}
默认都是按小端存储的，其实ANY和NONE怎么都一样,每个位都是一样的，容易出错的就在于LOOPBACK这个回环网卡地址是必须显示调用htonl()函数转换才能使用。
但是最让我奇怪的是：为什么pton()函数返回的值是按大端排序的,很是奇怪，也就是说通过pton()函数转换过的地址不能调用htonl()函数转换，否则会出错。
下面给段demo程序：
{% codeblock demo.c %}
    #include <stdlib.h>
    #include <stdio.h>
    #include <arpa/inet.h>
    int main() {
        char s[] = "127.0.0.1";
        struct in_addr address;
        if ((inet_pton(AF_INET, s, &address)) != 1) {
            return -1;
        }

        printf("%i --- %i --- %i", address.s_addr,
                htonl(address.s_addr), INADDR_LOOPBACK);
        return 0;
    }
{% endcodeblock %}
翻过pton()和memcpy(),aton()函数源码还是没找到.
记录到这里，以免以后忘了，有空再研究.
