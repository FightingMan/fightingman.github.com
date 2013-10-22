---
layout: post
title: "Reinforcement php environment via php.ini"
date: 2012-12-03 10:59
comments: true
categories: [PHP,Security]
---
I'm reading \<White hat talk about security of Web\> written by aullik5,found that some generic option can reinforcement php env via php.ini,now share it.

1.register_globals.
    register_globals = Off
    Prevent from variables override.

2.open_basedir
    Limit the files that can be opened by PHP to the specified directory-tree, including the file itself.
    When you want to restrict access to only the specified directory, end with a slash. For example: open_basedir = /dir/incl/,if not it will set as prefix.

3.allow_url_fopen & allow_url_include 
    allow_url_include = Off
    allow_url_fopen = Off

4.display_errors & log_errors
    display_errors = Off
    log_errors = On
    If u turn it on,will give enough information to the attacker.The best way just put them into logs.

<!--more-->
5.magic_quotes_gpc [more](http://stackoverflow.com/questions/2610524/why-turning-magic-quotes-gpc-on-in-php-is-considered-a-bad-practice)
    magic_quotes_gpc = Off
    Many methods can bypass it.

6.cgi.fix_pathinfo [more](https://nealpoole.com/blog/2011/04/setting-up-php-fastcgi-and-nginx-dont-trust-the-tutorials-check-your-configuration/)
    cgi.fix_pathinfo = 0 

7.session.cookie_httponly & session.cookie_secure
    session.cookie_httponly = true
    Prevent cross site script,javascript can't get the cookie's value.
    session.cookie_secure = true
    if u use https pass through the site.

8.safe_mode & disable_functions
    safe_mode = 0
    disable_functions = "apache_child_terminate, apache_setenv, define_syslog_variables, escapeshellarg, escapeshellcmd, eval, exec, dl, fsocket, pfsockopen, fsockopen, fp, fput, ftp_connect, ftp_exec, ftp_get, ftp_login, ftp_nb_fput, ftp_put, ftp_raw, ftp_rawlist, highlight_file, ini_alter, ini_get_all, ini_restore, inject_code, mysql_pconnect, openlog, phpinfo, passthru, php_uname, phpAds_remoteInfo, phpAds_XmlRpc, phpAds_xmlrpcDecode, phpAds_xmlrpcEncode, popen, posix_getpwuid, posix_kill, posix_mkfifo, posix_setpgid, posix_setsid, posix_setuid, posix_setuid, posix_uname, proc_close, proc_get_status, proc_nice, proc_open, proc_terminate, shell_exec, syslog, system, system,proc_open, symlink, xmlrpc_entity_decode, popen, curl_exec, curl_multi_exec, parse_ini_file, show_source" and etc.

At last we must always take care of user's input data and set correct right of web app dir.
