---
layout: post
title: "Summary of Understanding Mongodb Sharding"
date: 2012-11-28 22:16
comments: true
categories: Mongodb
---
Sharding is the method Mongodb uses to split a large collection across several servers(called a cluster).

A Mongodb cluster basically consists of three types of processes:the shards for actually storing data, the mongos processes for routing requests to the correct data,and the config servers,for keeping track of the cluster's state.

{% img /images/MongodbCluster.jpeg %}

>1. The Config Servers:
     Config servers hold the definitive information about the cluster for everyone's access (shards,mongos processes, and system administrtors).
>2. Mongos:
     Mongos is the interaction point  between users and the cluster..Its job is to hide all of the gooey internals of sharding  and present a clean, single-server interface to the user.Mongos can help you to use targeted query if you query involves the shard key.
>3. Shards:
     A shard is one or more servers in a cluster that are responsible for some subset of the data(more than one server used for replica set usually).
<!--more-->
Shard key And Chunk
>The key you choose to use for chunk ranges is called a shard key(can be any field or combination of fields).e.g.
{% codeblock %}
Suppose our collection had documents that looked like this (_ids omitted):
{"username" : "paul", "age" : 23}
{"username" : "simon", "age" : 17}
{"username" : "widdly", "age" : 16}
{"username" : "scuds", "age" : 95}
{"username" : "grill", "age" : 18}
{"username" : "flavored", "age" : 55}
{"username" : "bertango", "age" : 73}
{"username" : "wooster", "age" : 33}
If we choose the age field as a shard key and end up with a chunk range [15, 26),
thechunk would contain the following documents:
{"username" : "paul", "age" : 23}
{"username" : "simon", "age" : 17}
{"username" : "widdly", "age" : 16}
{"username" : "grill", "age" : 18}
{% endcodeblock %}
When you first shard a collection , Mongodb creates a single chunk for whatever data is in the collection,and the has a range of (-∞,+∞),until you inserted more data,-∞ is the smallest value ,+∞ is the largest value.
If you insert enough data into the shards,Mongodb would split the initial chunk into two chunks around the midpoint.So,if approximately half of the documents had a an age field less than 15 and halst were greater than 15(above demo),Mongodb might choose 15 as th midpoint.Then we had two chunks:(-∞,15),[15,+∞).If we continued to insert data into the [15,+∞)chunk,it could be split again,so we have three chunks:(-∞,15),[15,midpoint),[midpoint,+∞).As we insert more data,Mongodb will continued to split existing chunks to create new chunks.
We also can hava a chunk with a single value,but every chunk's range must be distincti.you cannot have overlapping chunks,each chunk's range must exactly meet the next chunk's range.e.g.
    if you split a chunk with the range[1,10),you can't have [1,5),[6,10);[1,5],[5,10);[1,5),[4,10).
    keep in mind: each document must belong to one and only one chunk.
Also you can use different type for the documents,but can't with empty values,Mongodb would sort it according to its type:
    null<numbers<strings<objects<arrays<binary data<ObjectIds<booleans<dates<regular expressions

Balancer
>If there are multiple shards available,Mongodb will start migrating data to other shards once you have a sufficient number of chunks.This migration is called balancing and is perfomed bt a process called the balancer.
The balancer moves chunks from one shard to another automatically.
>The goal of the balancer is not only to keep the data evenly distributed but also to minimize the amount of data transferred.

Multi-range shards
>Allowing multiple,non-consesutive ranges in a shard allow us to pick and choose data and move it.

{% img /images/move1.jpeg %}

>When a new shard is added,everyone can contribute data to it directly.

{% img /images/move2.jpeg %}

>For a balancing round to occur, a shard must have at leat nine more chunks than the least-populous shard,so chunks will be migrated off of the crowed shard until it is even with the rest of the shards,because Mongodb wants to avoid sending the same data back and forth.Also keep in mind nine chunks is not even that much of an imbalance-it is less than 2GB of data,as Mongodb want minimize pointless data transfers.

{% img /images/avoid.jpeg %}

Notice:In a real system, chunks are only 64MB by default,also you can change it in megabytes(db.settings.save( { _id:"chunksize", value: <size>}))
