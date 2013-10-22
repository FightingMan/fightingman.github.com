---
layout: post
title: "Strategy & Factory design pattern with interface"
date: 2012-11-23 00:39
comments: true
categories: Java
---
Strategy design pattern
>When you write a method that performs certain operations,and that method takes an interface which you also specify,then the others can use this method with any object,as long as their object conforms to the interface.
{% codeblock RandomWords.java %}
import java.nio.*;
import java.util.*;
public class RandomWords implements Readable {
    private static Random rand = new Random(47);
    private static final char[] capitals = "ABCDEFGHIJKLMNOPQRSTUVWXYZ".toCharArray();
    private static final char[] lowers = "abcdefghijklmnopqrstuvwxyz".toCharArray();
    private static final char[] vowels = "aeiou".toCharArray();
    private int count;
    public RandomWords(int count) {
        this.count = count;
    }
    public int read(CharBuffer cb) {
        if(count-- == 0) return -1;
        cb.append(capitals[rand.nextInt(capitals.length)]);
        for (int i = 0; i < 4; i++) {
            cb.append(lowers[rand.nextInt(lowers.length)]);
            cb.append(vowels[rand.nextInt(vowels.length)]);
        }
        cb.append("|");
        cb.append(" ");
        return 1;
    }
    public static void main(String[] args) {
        Scanner s = new Scanner(new RandomWords(10));
        while (s.hasNext()) {
            System.out.println(s.next());
        }
    }
}
{% endcodeblock %}
<!--more-->
Factory design pattern
>Instead of calling a constructor directly, you call a creation method on a factory object which produces an implementation of the interface-this way,in theory,your code is completely isolated from the implementation of the interface,thus making it possible to transparently swap one implementation for another.
{% codeblock Decisions.java %}
import java.util.*;
interface Decision {
    void toss();
}

interface DecisionFactory{
    Decision getDecision();
}

class Coin implements Decision {
    private static final int point = new Random().nextInt(6)+1;
    public void toss() {
        System.out.println("Coin point = " + point);
    }
}

class CoinFactory implements DecisionFactory {
    public Decision getDecision() {
        return new Coin();
    }
}

class Dice implements Decision {
    private static final String[] s = {"head","flower"};
    private static final int point = new Random().nextInt(2);
    public void toss() {
        System.out.println("Dice point " + s[point]);
    }
}

class DiceFactory implements DecisionFactory {
    public Decision getDecision() {
        return new Dice();
    }
}

public class Decisions {
    public static void doToss(DecisionFactory factory) {
        Decision d = factory.getDecision();
        d.toss();
    }

    public static void main(String[] args) {
        doToss(new CoinFactory());
        doToss(new DiceFactory());
    }
}

{% endcodeblock %}
