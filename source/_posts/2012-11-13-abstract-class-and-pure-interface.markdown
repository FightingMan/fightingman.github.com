---
layout: post
title: "Abstract class &amp; pure interface"
date: 2012-11-13 01:04
comments: true
categories: Java
---
Interface and abstract class provide more structured way to seprate interface from implemention.Java support this mechanisms directly.

Abstract class and methods.
>A method that is incomplete; it has only a declaration and no method body.
    abstract void foo();
>A class contains abstract methods is called abstract class.
    abstract class bar{}
>If you inherit from an abstract class and you want to make objects of new type, you must provide method definitions for all the abstract methods in the base class. If you don't, then the derived class is also abstract, so you can't new the abstract class,or make object of it.

>An abstract class is a class either,so you can use extends keyword.
<!--more-->
Interface.
>The interface keyword just take the concept of abstractness one stp further which means the interface keyword produces a completely abstract class, ont that provides no implementation at all.it allows the creator to determine method names, argument lists, and return types, but no method bodies.The fiedls in it will be implicitly with static and final.

>You can choose to explicitly declare the method in an interface as public, but they are public even if you don't say it.So when you implement an interface, the methods from interface must be defined as public which means you can't use other keywords or give it package access in the implementation.
    interface bar{}
>If you want to implement it.You just allowed to use implements keyword.
    class foo implements bar{} //Note: you must implements all the methods in the interface:bar,also this class can be abstract.
