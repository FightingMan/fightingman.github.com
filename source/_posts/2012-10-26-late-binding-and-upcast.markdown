---
layout: post
title: "Late binding &amp; upcasting"
date: 2012-10-26 19:08
comments: true
categories: Java
---
{% codeblock Music.java %}
class Instrument {
    public void play() {
        System.out.println("Instrument.play()");
    }
}

class Wind extends Instrument {
    public void play() {
        System.out.println("Wind.play()");
    }
}

public class Music {
    public static void tune(Instrument i) {
        i.draw();
    }

    public static void main(String[] args) {
        Wind w = new Wind();
        tune(w); // upcasting
    }
}//Output: Wind.play()
{% endcodeblock %}
<!--more-->
As you can see the output,because of dynamic binding(also called late binding).

Late bingding is needed when the compiler determines that thers more than one possible method that could be executed by a particular call.

Late binding has definition a mechanism by which, when the compiler can't determine which method implementation to use in advance at compile-time,the runtime system selects the appropriate method at runtime,base on the class of the object.

Late binding has definition the process of binding a call to particular method this is performed dynamically at run-time due wo the presence of polymorphism.
