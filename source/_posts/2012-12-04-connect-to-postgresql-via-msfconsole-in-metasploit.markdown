---
layout: post
title: "Connect to postgresql via msfconsole in Metasploit"
date: 2012-12-04 18:52
comments: true
categories: Security
---
Today I'm testing metasploit in bt5,there is a problem takes me some time,yes,this is a bug,show these codes:
    root@bt:~# cd /opt/metasploit/
    root@bt:/opt/metasploit# cat config/database.yml 
        development:
        adapter: "postgresql"
        database: "msf3dev"
        username: "msf3"
        password: "4bfedfc2"
        port: 7337
        host: "localhost"
        pool: 256
        timeout: 5

        production:
        adapter: "postgresql"
        database: "msf3dev"
        username: "msf3"
        password: "4bfedfc2"
        port: 7337
        host: "localhost"
        pool: 256
        timeout: 5
    root@bt:/opt/metasploit# netstat -tunpl | grep postgres
    tcp        0      0 127.0.0.1:7337          0.0.0.0:*               LISTEN      1321/postgres.bin
    tcp6       0      0 ::1:7337                :::*                    LISTEN      1321/postgres.bin
    root@bt:/opt/metasploit# cd msf3
    root@bt:/opt/metasploit/msf3# ./msfconsole

    # cowsay++
     ____________
    < metasploit >
     ------------
           \   ,__,
            \  (oo)____
               (__)    )\
                  ||--|| *


           =[ metasploit v4.5.0-dev [core:4.5 api:1.0]
    + -- --=[ 927 exploits - 499 auxiliary - 151 post
    + -- --=[ 251 payloads - 28 encoders - 8 nops

    msf > db_connect msf3:4bfedfc2@localhost:7337/msf3dev
          [-] No database driver installed. Try 'gem install pg'

msf3 is the username in database.yml; 
4bfedfc2 is the password in database.yml; 
msf3dev is the database in database.yml; 
and the 7337 is the port for postgresql.
<!--more-->
    msf > find / -iname pg_config
    [*] exec: find / -iname pg_config

    /opt/metasploit/postgresql/bin/pg_config
    msf > gem install pg -- --with-pg-config=/opt/metasploit/postgresql/bin/pg_config
    [*] exec: gem install pg -- --with-pg-config=/opt/metasploit/postgresql/bin/pg_config

    Building native extensions.  This could take a while...
    Successfully installed pg-0.14.1
    1 gem installed
    Installing ri documentation for pg-0.14.1...
    Installing RDoc documentation for pg-0.14.1...
    msf > db_connect msf3:4bfedfc2@localhost:7337/msf3dev
        [-] No database driver installed. Try 'gem install pg'//also

At last I've found out that the problem is related to the installed ruby version. 
The start script of msfconsole uses /usr/bin/env ruby as Parser. 
But metasploit seems to have an own version of ruby which you can use by changing msfconsole.
you can test it by yourself:

    root@bt:/opt/metasploit/msf3# /opt/metasploit/ruby/bin/ruby -v
    ruby 1.9.3p125 (2012-02-16 revision 34643) [i686-linux]
    root@bt:/opt/metasploit/msf3# /usr/bin/ruby -v
    ruby 1.9.2dev (2010-07-02) [i486-linux]

    vi /opt/metasploit/msf3/msfconsole
    change #!/usr/bin/env ruby to #!/opt/metasploit/ruby/bin/ruby in the top of msfconsole.
    msf > db_connect msf3:4bfedfc2@localhost:7337/msf3dev

Now it will work fine and show you a lot of text output as Metasploit sets up all the necessary tables;

Enjoy it :)
