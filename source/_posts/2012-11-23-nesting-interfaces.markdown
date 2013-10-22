---
layout: post
title: "Nesting interfaces"
date: 2012-11-23 01:27
comments: true
categories: Java
---
These features may seem like they are added strictly for syntactic consistency,maybe I will use it sometime :).
There are 5 Rules:
>1. Nesting an interface within a class,these can have public package-access protected private visibility.
>2. Nesting an interface within an interface they just have public or package-access visibility but all the elements must be public because an interface nested with another interface is automatically public(whether you specify it).
>3. Nesting an interface within a class with any visibility,it can also be implemented any visibility.And if you implements a private interface with any visibility it can only be used as itself.It also means without allowing any upcasting.
>4. The only thing that works is if the return value is handed to an object that has permission to use it.
>5. When you implement an interface, you are not required to implemtns any interface nested within.

Let me show the codes:
<!--more-->
{% codeblock NestingInterfaces.java %}
class A {
    interface B {
        void f();
    }
    public class BImp implements B {
        public void f() {}
    }
    private class BImp2 implements B {
        public void f() {}
    }
    public interface C {
        void f();
    }
    class CImp implements C {
        public void f() {}
    }
    private class CImp2 implements C {
        public void f() {}
    }
    private interface D {
        void f();
    }
    private class DImp implements D {
        public void f() {}
    }
    public class DImp2 implements D {
        public void f() {}
    }
    public D getD() { return new DImp2(); }
    private D dRef;
    public void receiveD(D d) {
        dRef = d;
        dRef.f();
    }
}

interface E {
    interface G {
        void f();
    }
    // Redundant "public":
    public interface H {
        void f();
    }
    void g();
    // Cannot be private within an interface:
    //! private interface I {}
}

public class NestingInterfaces {
    public class BImp implements A.B {
        public void f() {}
    }
    class CImp implements A.C {
        public void f() {}
    }
    // Cannot implement a private interface except
    // within that interface’s defining class:
    //! class DImp implements A.D {
    //! public void f() {}
    //! }
    class EImp implements E {
        public void g() {}
    }
    class EGImp implements E.G {
        public void f() {}
    }
    class EImp2 implements E {
        public void g() {}
    }
    class EG implements E.G {
        public void f() {}
    }

    public static void main(String[] args) {
        A a = new A();
        // Can’t access A.D:
        //! A.D ad = a.getD();
        // Doesn’t return anything but A.D:
        //! A.DImp2 di2 = a.getD();
        // Cannot access a member of the interface:
        //! a.getD().f();
        // Only another A can do anything with getD():
        A a2 = new A();
        a2.receiveD(a.getD());
    }
}
 ///:~
{% endcodeblock %}
