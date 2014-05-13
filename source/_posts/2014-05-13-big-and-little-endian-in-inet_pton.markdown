---
layout: post
title: "big and little endian in inet_pton()"
date: 2014-05-13 21:42
comments: true
categories: C/C++ Tcp/Ip
---

今天弄懂了昨天那篇文章[Socket编程遇到字节排序的诡异问题](http://www.fightingman.org/blog/2014/05/13/doubt-of-host-byte-order/)里的疑惑:
{% codeblock main.c %}
#include <stdlib.h>
#include <stdio.h>
#include <arpa/inet.h>

int main() {
    struct in_addr addr;
    inet_pton(AF_INET, "127.0.0.1", &addr);
    printf("%x\n", addr.s_addr);
    return 0;
}
{% endcodeblock %}
    输出：100007f
开始就觉得很不解，127.0.0.1转为网码不是应该7f000001吗，为什么这里会这么奇怪而且头文件里面定义的
    # define INADDR_LOOPBACK    ((unsigned long int) 0x7f000001)
也确实是7f000001，后来再次翻了inet_pton()函数的源码
{% codeblock inet_pton.c %}
int
inet_pton(af, src, dst)
	int af;
	const char *src;
	void *dst;
{
	switch (af) {
	case AF_INET:
		return (inet_pton4(src, dst));
	case AF_INET6:
		return (inet_pton6(src, dst));
	default:
		__set_errno (EAFNOSUPPORT);
		return (-1);
	}
	/* NOTREACHED */
}
libc_hidden_def (inet_pton)

static int
internal_function
inet_pton4(src, dst)
	const char *src;
	u_char *dst;
{
	int saw_digit, octets, ch;
	u_char tmp[NS_INADDRSZ], *tp;

	saw_digit = 0;
	octets = 0;
	*(tp = tmp) = 0;
	while ((ch = *src++) != '\0') {

		if (ch >= '0' && ch <= '9') {
			u_int new = *tp * 10 + (ch - '0');

			if (saw_digit && *tp == 0)
				return (0);
			if (new > 255)
				return (0);
			*tp = new;
			if (! saw_digit) {
				if (++octets > 4)
					return (0);
				saw_digit = 1;
			}
		} else if (ch == '.' && saw_digit) {
			if (octets == 4)
				return (0);
			*++tp = 0;
			saw_digit = 0;
		} else
			return (0);
	}
	if (octets < 4)
		return (0);
	memcpy(dst, tmp, NS_INADDRSZ);
	return (1);
}
{% endcodeblock %}
<!--more-->
看到inet_pton4函数里面实际是按127.0.0.1的顺序直接存到内存的：
    127.0.0.1转为网码为7f 00 00 01 ，由于我电脑是pc是小端，
    将这个变量写到变量里会存为 01 00 00 7f,但是这个函数直接通过内存地址存入的7f 00 00
    01,我们通过printf函数打印的时候又因为计算机本来是小端字节序，
    会从右往左读就会是01 00 00 7f了，因为相当于inet_pton这个函数帮我们做了一次htonl()。
这就是为什么INADDR_LOOPBACK被定义为0x7f000001,却任然需要显示调用htonl(),  
而通过inet_pton()函数转换过的地址不能调用inet_htonl()的原因了.  
非常感谢laeder礼刚平时对我的帮助，良师益友，虽然我们再过1个多来月就要各奔东西了。
