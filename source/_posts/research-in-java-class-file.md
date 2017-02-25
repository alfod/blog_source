---
title: research in java class file
tags:
  - java
  - jvm
categories: Technology
comments: true
date: 2017-02-25 21:40:59
---


# Prelude
java class file ( produced by java compiler ,  whose naming format is \*.class ) can be executed by *Java Virtual Machine*  ( also called **JVM**  ) .  For JVM is available for many platforms , java language obtain its precious features  **Platforms Independent** which means **once write , run anywhere**.
many java programmers can be curious about it , let's share a journey of exploration !

# Basic Knowledge
*  **Magic  Number**
Only java class file's first 4 bytes code is｜ "0xCAFEBABE" , which give JVM a fast way to verify a file's format  .

*   **Version Number**
The next 4 bytes indicate the java version of this class file , in which the first two bytes represented minor version name , while  the other two bytes represented major version .

*  **Constant pool**
This section can be broken into two parts ,  pool count in first 2 bytes and the rest  is constant pool table , which store Strings , numbers  and references to classes and methods .

#  Examples
*       **Start**
*  *javac  $SourceFilePath*
        javac will compile java code to class file .

*  *javap -verbose $ClassPath*      
        javap  will disassembles a class file to  human-readable  format , we will analyze the result file to understand how class file describe  the java source code

*       **Simplest Function**

```
package com.alfod;

public class JavaBiteCode {

    public void fun() {
    }
}

minor version: 0
major version: 52
flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
 #1 = Methodref          #3.#14         // java/lang/Object."<init>":()V
 #2 = Class              #15            // com/alfod/JavaBiteCode
 #3 = Class              #16            // java/lang/Object
 #4 = Utf8               <init>
 #5 = Utf8               ()V
 #6 = Utf8               Code
 #7 = Utf8               LineNumberTable
 #8 = Utf8               LocalVariableTable
 #9 = Utf8               this
#10 = Utf8               Lcom/alfod/JavaBiteCode;
#11 = Utf8               fun
#12 = Utf8               SourceFile
#13 = Utf8               JavaBiteCode.java
#14 = NameAndType        #4:#5          // "<init>":()V
#15 = Utf8               com/alfod/JavaBiteCode
#16 = Utf8               java/lang/Object
{
public com.alfod.JavaBiteCode();
  descriptor: ()V
  flags: ACC_PUBLIC
  Code:
    stack=1, locals=1, args_size=1
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return
    LineNumberTable:
      line 3: 0
    LocalVariableTable:
      Start  Length  Slot  Name   Signature
          0       5     0  this   Lcom/alfod/JavaBiteCode;

public void fun();
  descriptor: ()V
  flags: ACC_PUBLIC
  Code:
    stack=0, locals=1, args_size=1
       0: return
    LineNumberTable:
      line 6: 0
    LocalVariableTable:
      Start  Length  Slot  Name   Signature
          0       1     0  this   Lcom/alfod/JavaBiteCode;
}
SourceFile: "JavaBiteCode.java"

```
