---
layout: post
title: "Early-binding &amp; late-binding"
date: 2012-10-26 14:22
comments: true
categories: Java
---
Connecting a method call to a method body is called binding.

when binding is performed befor the program is run,it's called early binding.

when binding occurs at run time,based on the type of object, also called dynamic binding or run time binding.

All method binding in java uses late binding unless the method is static or final(private methods are implicitly final).

It's best to only use final as design decision, and not as an attempt to improve performance, it won't make any extra performance.
