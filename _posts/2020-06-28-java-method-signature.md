---
layout: post
title: jvm中的方法签名
categories: [java, jvm]
description: java方法签名
keywords: java, jvm
---

## java方法签名

### 函数前面
在编程语言中，通常使用名字来代表一个变量并和其他变量做区分。自然的，我们会想到函数是否也是使用函数名字来区分的呢。稍微想一下，我们目前常用的大部分编程语言都是支持函数重载的，相同的函数可能会有不同的实现，显然此时使用函数名字不能区分不同的实现，那么这些语言是怎么区分函数的呢？
实际上，我所了解的编程语言中，只有C语言是只用函数名字标识函数的，C++为了兼容C library还做了一些特殊处理（extern "C" {}）,其他的语言，如java都会根据函数名，入参和返回值生成一个唯一个函数签名。
### java语言 和 jvm
在使用java编程语言时，很多人都清楚，如果两个函数函数名相同，入参相同但是返回值不同，编译器也会认为这是两个相同的函数。
```java
    public Integer create(){
        return null;
    }
    // 报错，编译器认为重复定义了函数
    public Double create(){
        return null;
    }
```
但是实际上jvm的字节码中，不同返回值的函数是能够区分的，如果javac编译器允许函数名相同，入参相同但是返回值不同的函数正常编译，jvm也是能执行这样的字节码的。实际上java规范和jvm规范是两个不关联的文件，甚至java源码不一定要编译成字节码，而任何语言只要把源码编译成符合jvm规范的字节码，就能在jvm上执行。
### 函数签名
1. 我们先看一个例子，从例子看出 createInt 的签名是 creatInt:(II)I，I表示int，括号里的II表示入参，括号后面的
```java
public class I {
    public int creatInt(int a, int b){
        return a + b;
    }
    public static void main(String[] args) {
        I o = new I();
        o.creatInt(1, 2);
    }
}
// javap -verbose I.class
//   public int creatInt(int, int);
//     descriptor: (II)I
// invokevirtual #11           // Method creatInt:(II)I
```
2. 每一个原始类型java都有一个固定的符号表示，如果参数是如果参数是一个对象，签名是L+参数类型全限定名，并用;分隔，如下
```java
    public List createList(List a, List b){
        return new ArrayList();
    }
    //  invokevirtual #11      // Method createList:(Ljava/util/List;Ljava/util/List;)Ljava/util/List;
```
3. 如果参数是数组, 则在[ + 分数组参数的签名
```java
    public String createWithArray(String[] arr){
        return arr[0];
    }
    // invokevirtual #14        // Method createWithArray:([Ljava/lang/String;)Ljava/lang/String;
```
### 后记
之所以想到要写java函数签名的文字，主要是我偶然发现字节码中使用 I 标记 int 类型，自然就想到如果一个没有package的class I要如何标记呢，于是用javap测试了一下，于是就看到了java的这个解决方案，特此记录一下。