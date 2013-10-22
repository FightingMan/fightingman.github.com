---
layout: post
title: "Name collisions when combining"
date: 2012-11-14 21:16
comments: true
categories: Java
---
{% codeblock Pitfall.java %}
interface I1 {
    void f();
}

interface I2 {
    int f(int i);
}

interface I3 {
    int f();
}


class C {
    public int f() {
        return 1;
    }
}

class C2 implements I1, I2 {
    public void f() {}
    public int f(int i) {
        return 1;
    }
}

class C3 extends C implements I2 {
    public int f(int i) {
        return 1;
    }
}

class C4 extends C implements I3 {
    public int f() {
        return 1;
    }
}
/**error:
*class C5 extends C implements I1 {}
*class I4 extends I1,I3 {}
*class C6 extends I2,I3 {} 
*/

{% endcodeblock %}
I think there are three rules at least:
>1. Methods differ isn't only by return type.
>2. When a derived class do inherit from a class with implements interface,if the method have a method with same name,these methods must have same return type.
>3. When a class implements interfaces,the methods which have same name must have its own return type and method signature.
