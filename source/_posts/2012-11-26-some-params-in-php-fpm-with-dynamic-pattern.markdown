---
layout: post
title: "Some params in php-fpm with dynamic pattern"
date: 2012-11-26 20:45
comments: true
categories: PHP
---
When we set pm = dynamic in php-fpm.conf,there are some important params.
    1. pm.max_children:This value sets the limit on the number of simultaneous requests that will be served.
    2. pm.start_servers: The number of child processes created on startup.
    3. pm.min_spare_servers: The desired minimum number of idle server processes.
    4. pm.max_spare_servers: The desired maximum number of idle server processes.
    5. pm.max_requests:The number of requests each child process should execute before respawning.
>The first and the second is easy to understand,how about the 3rd-5th?

The 3rd params which means: while forking full of subprocess arrive the 1st numbers,there also value of th 3rd sbprocess are idle.If pm.max_children was setted to 50,the highest number of concurrent request is limitted to (50 - the value of pm.min_spare_servers) in fact.
<!--more-->
The 4th params which means: With no request,the max subprocess is idle,after your concurrent arrived max_children,and the manager recovery of the subprocess till the values of pm.max_spare_servers.

The 5th params which means: AS its explanation said,if its value was too small,the edge of it is 1(0 representatives request unlimited),all subprocesses may occur to restart at the same time,this is a tragedy.While its value was too big,the stack may be overflowed.This params is very usefull in 3rd party libraries.

At last: the memory_limit in php.ini is very important,because it limitted the maximum memory that one php-fpm process  can be used.

Also there are other very important params,here I'm not listed all of them,the above parameters just with strong correlation.
