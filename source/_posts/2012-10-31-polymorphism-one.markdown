---
layout: post
layout: post
title: "Polymorphism one"
date: 2012-10-31 21:19
comments: true
categories: Java
---

Polymorphism always perform the right behavior because of late-binding,as the last posts say.

Polymorphism also havs better extensibility,if a class inherited the base class,and another class has a method(the param's type is base class),now you can add any more derived class from 
the bass class without change the third class's method.

Pitfall:

a. If the derived class override the private method which in the base class,and call the method with upcasting to the base class ,unexpected the method will be called in the base class.
    {% codeblock pitfallOne.java %}
        public class pitfallOne {
            private void f() {
            System.out.println("sup");
            }
            public static void main(String[] args) {
                pitfallOne p = new sub();
                p.f();
            }
        }

        class sub extends pitfallOne {
            void f() {
                System.out.println("sub");
            }
        }//output:sup;
    {% endcodeblock %} b. If you access a field directly, the access will be resolved at compile-time,won't happen polymorphically.
<!--more-->
    {% codeblock pitfallTwo.java %}
        class sup {
            public int age = 24;
        }

        class sub extends sup {
            public int age = 12;
        }

        public class pitfallTwo {
            public static void main(String[] args) {
                sup s = new sub();
                System.out.println(s.age);
            }
        }//output:24;
    {% endcodeblock %} c. If a method is static, it doesn't behave polymorphically,because static methods associated with the class, and not the individual objects.
    {% codeblock pitfallThree.java %}
        class sup {
            static void staticGet() {
                System.out.println("super static method");
            }
        }

        class sub extends sup {
            static void staticGet() {
                System.out.println("sub static method");
            }
        }

        public class pitfallThree {
            public static void main(String[] args) {
                sup s = new sub();
                s.staticGet();
            }
        }//output: super static method.
    {% endcodeblock %} As we know that a constructor for the base class is always called during the construction process for a derived class,chaining up the inhreitance hierarchy. And if we 
do have cleanup issues,the order of cleanup should be the reverse of the order of initialization,in case one subobjects depends on another.

If you call a dynamically-bound method inside a constructor,the effect of this call can be rather unexpected because the overridden method will be called before the object is fully 
constructed.
    {% codeblock PolyConstructors.java %}
class Glyph {
    void draw() {
        print("Glyph.draw()");
    }
    Glyph() {
        print("Glyph() before draw()");
        draw();
        print("Glyph() after draw()");
    }
}

class RoundGlyph extends Glyph {
    private int radius = 1;
    RoundGlyph(int r) {
        radius = r;
        System.out.println("RoundGlyph.RoundGlyph(), radius = " + radius);
    }

    void draw() {
        System.out.println("RoundGlyph.draw(), radius = " + radius);
    }
}

public class PolyConstructors {
    public static void main(String[] args) {
        new RoundGlyph(5);
    }
}//output:
Glyph() before draw()
RoundGlyph.draw(), radius = 0
Glyph() after draw()
RoundGlyph.RoundGlyph(), radius = 5
    {% endcodeblock %}
At first we must know the actual process initialization:
    1 The storage allocated for the object is initialized to binary zero before anything else
    happens.
    2 The base-class constructors are called as described previously. At this point, the
    overridden draw( ) method is called (yes, before the RoundGlyph constructor is
    called), which discovers a radius value of zero(base on the its type), due to Step 1.
    3 Member initializers are called in the order of declaration.
    4 The body of the derived-class constructor is called.
I can think a benefit of this design: if we have app which use google searching interface and have been compilied,and now your leader told you that we must changed to yahoo searching interface, and we don't need to change the code also without recompiled it,we just adjust the yahoo interface.

