---
layout: post
title: "Complete decoupling &amp; Multiple inheritance &amp; Extending interface"
date: 2012-11-14 20:49
comments: true
categories: Java
---
Complete decoupling.
>Whenever a method works with a class instead of an interface, you are limited to using that class or its subclass. If you would like to apply the method to a class that isn't in that hierarchy,you'er out of luck.An interface relaxes this constraint considerably.As a result it allows you to write more reusable code.

Multiple inheritance.
>Because an interface has no implementation at all--that is,there is no storage associated with an interface--there's nothing to prevent many interfaces from being combined.You can do inherit from a non-interface use extends keyword and implements interface as many as you want use implements keyword and separate them with commas. And then the derived class can upcast to anyone of them.
    class a extends b implements c,d,e,f{}

Extending an interface with inderitance.
>You can combine several interfaces into a new interface with inheritance.
    {% codeblock Demo.java %}
        interface A {}
        interface B {}
        interface C {}
        interface D extends A,B,C{}
    {% endcodeblock %}
