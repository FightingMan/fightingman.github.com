---
layout: post
title: "Basic syntax of inner class"
date: 2012-11-27 23:02
comments: true
categories: Java
---
Place a class definition within another class definition that is called an inner class.
{% codeblock Outer.java %}
public class Outer {
    private int i = 10;
    public void f() {
        System.out.println("outer's f()");
    }

    private class Inner {
        public Outer outer() { //!cannot with static
            return Outer.this;
        }

        public Outer outer(int i) { //!cannot with static
            return new Outer();
        }

        public void f() {
            System.out.println("inner's f()");
        }

        public Inner() {
            System.out.println(i);
        }
    }

    public Inner inner() { //!cannot with static
        return new Inner();
    }
}
{% endcodeblock %}
<!--more-->
{% codeblock InnerClass.java %}
public class InnerClass {
    public static void main(String[] args) {
        //! Outer.Inner in = new Inner();
        //! Outer.Inner in = new Outer.Inner();
        Outer outa = new Outer();
        outa.f();
        Outer.Inner ina = outa.inner();
        Outer.Inner inb = outa.new Inner();
        ina.outer().f();
        inb.outer().f();
        ina.f();
        inb.f();
        Outer outb = ina.outer(1);
        Outer outc = inb.outer(1);
        outb.f();
        outc.f();
        Outer.Inner inc = outb.inner();
        Outer.Inner ind = outb.new Inner();
        Outer.Inner ine = outc.inner();
        Outer.Inner inf = outc.new Inner();
        inc.outer().f();
        ind.outer().f();
        inc.f();
        ind.f();
        ine.outer().f();
        inf.outer().f();
        ine.f();
        inf.f();
    }
}//output:
outer's f()
10
10
outer's f()
outer's f()
inner's f()
inner's f()
outer's f()
outer's f()
10
10
10
10
outer's f()
outer's f()
inner's f()
inner's f()
outer's f()
outer's f()
inner's f()
inner's f()
{% endcodeblock %}
You can make an object of the inner class anywhere except from within a no-static method of the outer class(if u don't use .new). Inner class has access rights to all the elements in the enclosing class.
Notice: If you make a static inner class(called nested class), then it doesn't need a reference to the outer-class object.

