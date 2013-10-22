---
layout: post
title: "Polymorphism two"
date: 2012-11-04 21:08
comments: true
categories: Java
---
Covariant return types
>An overridden method in a derived class can return a type derived from the type returned by the base class method.
    {% codeblock CovariantReturn.java %}
        class Grain {
            public String toString() {
                return "Grain";
            }
        }

        class Wheat extends Grain() {
            public String toString() {
                return "Wheat";
            }
        }
        
        class Mill {
            Grain process() {
                return new Grain();
            }
        }

        class WheatMill extends Mill {
            Wheat process() {
                return new Wheat();
            }
        }

        public class CovariantReturn {
            public static void main(String[] args) {
                Mill m = new Mill();
                Grain g = m.process();
                System.out.println(g);
                m = new WheatMill();
                g = m.process;
                System.out.println(g);
            }
        }//output:
        Grain
        Wheat
    {% endcodeblock %}
<!--more-->

Designing with inheritance

1.The better approach is to choose composition first,when it's not obvious which one you should use,composition is also more flexible since it's possible to dynamically choose a type. A general guideline is: Use inheritance to express diffeences in behavior, and fields to express variations in state, this is also called the State Pattern.

2.Substitution vs. extension
    "Is-a" relationship: the interface of a class establishes what it is, inheritance guarantees that any derived class will have the interface of the base class and nothing less.
    "Is-like-a" relationship: the derived class is like the base class it has the same fundamental interface but it has other features that require additional methods to implement.
3.Downcasting and runtime type information
>When we design with is-like-a and via an upcast,we must lose the specific type information.The solution is downcast, in java every cast is checked! so even though it looks you're just performing an ordinary parenthesized cast, at run time this cast is checked to ensure that it is in fast the type you think it is.
    {% codeblock Downcast.java %}
        class sup {}

        class sub extends sup {
            public void only() {}
        }

        public class Downcast() {
            public static void main(String[] args) {
                sup s = new sub();
                //!s.only() compile time : method not found in sup;
                ((sub)s).only();// Downcast/runtime type information.
            }
        }
    {% endcodeblock %}
