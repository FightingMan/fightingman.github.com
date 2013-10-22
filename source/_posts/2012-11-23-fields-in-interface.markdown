---
layout: post
title: "Fields in interface"
date: 2012-11-23 01:20
comments: true
categories: Java
---
Fields in interface are automatically static and final,which means the interface is a convenient tool for creating groups of constant value.
Since the fields are static,they are initialized when the class is first loaded,which happens when any of the fields are accessed for the first time.The fields,of course,are not part of the interface.The values are stored in the static storage area for that interface.
