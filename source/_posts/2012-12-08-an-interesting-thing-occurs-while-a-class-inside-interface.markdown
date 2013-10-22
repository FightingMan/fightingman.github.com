---
layout: post
title: "An Interesting Thing Occurs While a Class Inside Interface"
date: 2012-12-08 22:08
comments: true
categories: Java
---
While I’m putting class inside interface with anonymous,something interested occurs.
{% codeblock As21.java %}
interface Comm {
    class Inner implements Comm {
        static void f(Comm c) {
            c.v();
        }
        public void v() {}
    }

    public void v();
}

interface Diff {
    class Differ implements Comm {
        static void g(Comm c) {
            c.v();
        }
        public void v() {}
    }
}

public class As21 {
    public static void main(String[] args) {
        Comm c = new Comm() {
            public void v() {
                System.out.println("as21");
            }
        };
        Comm.Inner.f(c);
    }
}

class As22 implements Comm {
    public void v() {
        System.out.println("as22");
    }

    public static void main(String[] args) {
        As22 a = new As22();
        Inner.f(a);
        Diff.Differ.g(a);
    }
}
{% endcodeblock %}

As you can see,As21 is the one which I wantted,but the interesting thing occurs in As22. First,any class you put inside an interface is automatically public and static. Then As22 implements the Comm interface so it can call f() method directly with using Inner.f() witout Comm in front of it, but the method f() in Diff interface cannot called directly,must use Diff.differ.f().

In addition,how to execute the program in command line while put classes inside interfaces?

e.g.
{% codeblock As20.java %}
interface As20 {
    class Inner implements As20 {
        public String toString() {
            return "test";
        }
        public static void main(String[] args) {
            Inner i = new Inner();
            System.out.println(i);
        }
    }
}
{% endcodeblock %}
>javac As20.java

>java As20\$Inner
