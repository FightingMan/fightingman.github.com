---
layout: post
title: "Example of sharding cluster"
date: 2012-12-01 19:45
comments: true
categories: Mongodb
---
Previous article showed some basic theories of Mongodb sharding,now we'll demonstrate it.
Prepare 15 servers:172.16.103.200~172.16.103.214

{% img /images/demo.jpeg %}

Suppose you had been installed mongodb in /usr/local/mongdb and its structure:
    |-- GNU-AGPL-3.0
    |-- README
    |-- THIRD-PARTY-NOTICES
    |-- bin
    |   |-- bsondump
    |   |-- mongo
    |   |-- mongod
    |   |-- mongodump
    |   |-- mongoexport
    |   |-- mongofiles
    |   |-- mongoimport
    |   |-- mongorestore
    |   |-- mongos
    |   |-- mongosniff
    |   |-- mongostat
    |   `-- mongotop
    |-- db//Directory
    |-- mongodb.conf
    `-- mongodb.log
<!--more-->
1.Configuration servers.//200-202
    vi /usr/local/mongodb/mongodb.conf
    fork = true
    logpath = /usr/local/mongodb/mongodb.log
    dbpath = /usr/local/mongodb/db/
    logappend=true
    journal=true
    configsvr=true
    /usr/local/mongdb/bin/mongod --config /usr/local/mongodb/mongodb.conf #start it
2.Mongos.//203-205
    vi /usr/local/mongodb/mongodb.conf
    fork = true
    logpath = /usr/local/mongodb/mongodb.log
    logappend=true
    configdb=172.16.103.200,172.16.103.201,172.16.103.202
    /usr/local/mongdb/bin/mongos --config /usr/local/mongodb/mongodb.conf #start it
3.Shards.//206-214
    vi /usr/local/mongodb/mongodb.conf
    fork = true
    logpath = /usr/local/mongodb/mongodb.log
    dbpath = /usr/local/mongodb/db/
    logappend=true
    journal=true
    #206-208
    replSet=sd1
    #209-211
    replSet=sd2
    #212-214
    replSet=sd3
    /usr/local/mongdb/bin/mongod --config /usr/local/mongodb/mongodb.conf #start it
4.Replica Set.
    #connect to one of sd1's svr//default primary svr
    >/usr/local/mongodb/bin/mongo 127.0.0.1:27017/admin
    >config = {_id:"sd1",members:[
    ... {_id:0,host:'172.16.103.206:27017'},
    ... {_id:1,host:'172.16.103.207:27017'},
    ... {_id:2,host:'172.16.103.208:27017'}]
    ... }
    >rs.initiate(config);
    #connect to one of sd2's svr//default primary svr
    >/usr/local/mongodb/bin/mongo 127.0.0.1:27017/admin
    >config = {_id:"sd2",members:[
    ... {_id:0,host:'172.16.103.209:27017'},
    ... {_id:1,host:'172.16.103.210:27017'},
    ... {_id:2,host:'172.16.103.211:27017'}]
    ... }
    >rs.initiate(config);
    #connect to one of sd2's svr//default primary svr
    >/usr/local/mongodb/bin/mongo 127.0.0.1:27017/admin
    >config = {_id:"sd3",members:[
    ... {_id:0,host:'172.16.103.212:27017'},
    ... {_id:1,host:'172.16.103.213:27017'},
    ... {_id:2,host:'172.16.103.214:27017'}]
    ... }
    >rs.initiate(config);
5.Add shards.
    #connect to one mongos svr
    /usr/local/mongodb/bin/mongo 127.0.0.1:27017/config
    #set chunksize,default 64M
    db.setting.save({_id:"chunksize",value:64})
    #add shards,you can set the maxsize by yourself//default 2048M
    use admin
    db.runCommand({addshard:"sd1/172.16.103.206:27017,172.16.103.207:27017,172.16.103.208:27017",maxsize:20480})
    db.runCommand({addshard:"sd2/172.16.103.209:27017,172.16.103.210:27017,172.16.103.211:27017",maxsize:20480})
    db.runCommand({addshard:"sd3/172.16.103.212:27017,172.16.103.213:27017,172.16.103.214:27017",maxsize:20480})
    #test comperation success
    db.runCommand({listshards:1})#will list all the shards if successed.
    #Active DB
    db.runCommand({enablesharding:"mydemo"});
    #set shard key for collection:user,before you do it , you can create some indexer for the key except unique indexer,if don't mongodb will create one for you automatically.
    db.runCommand({shardcollection:"mydemo.user",key:{id:1,name:1}})
Now every thing have been done,enjoy it :)//Other way,you can test this demo with three svrs just change their default port.

Let's talk about the shard key,the most important thing in my mind. [Docs](http://docs.mongodb.org/manual/core/sharding/#shard-keys)

Choose a good key make you get twice the result with half the effort.//from scaling mongodb.

A general formula //twitter-like.
    {coarseLocalityfield:1,searchfield:1}
Before we choose one,think about the answers to these questions:
    What do writes looks like? What is the shape and size of the documents you'er inserting?
    How much data is the system writing per hour? Per day?And the peak?
    What fields are random, and which ones are increasing?
    what do reads look like? What data are people accessing?
    How much data is the system reading per hour? Per day?And the peak?
    Is the data indexed? Should it be indexed?
    How much data is there,total?
Before you shard, you should get to know your data very well.
