---
layout: post
title: "Final keyword"
date: 2012-10-25 19:29
comments: true
categories: Java
---
Final data:java's constant,it can be fold into any calculation at run-time,the calculation can be perfomed at compile-time,eliminating some run-time overhead.Constants must be primitives and are expressed with the final keyword.With a primitive,final makes the value a constant,but with an object reference,final makes the reference a constant, it means it can never be changed to point to another object,however,the object itself can be modified;final data doesn't mean that its value is known at compile time,it can be initialized at run time(eg. give a randomly value).Java allows the creation of blank finals,which are fields that are declared as final but are not given an initialization value,but it must be initialized before it is used.A field that is both static and final has only one piece of storage that cannot be changed.

Final argument:java allows us to make arguments final by declaring them as such in the argument list.This feature is primaruly used to pass data to anonymous inner classes.
<!--more-->
Final methods:when the method declared with final it prevent any inheriting class from changing its and can't override it,if you create a public,protected,or package-access method with the same name in the derived class,you've just created a new method.

Final classes:if you declared a class with final keyword means no one can inherit it.Note the fields or method of a final class can be final or not,as you choose,but all methods of a final class are implicityly final.

In addition:today is my birthday,happy birthday to me!
