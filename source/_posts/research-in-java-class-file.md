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
Java class file ( produced by java compiler ,  whose naming format is \*.class ) can be executed by *Java Virtual Machine*  ( also called **JVM**  ) .  For JVM is available for many platforms , java language obtain its precious features  **Platforms Independent** which means **once write , run anywhere**.
Many java programmers can be curious about it , let's share a journey of exploration !
For purpose of  giving a profile to beginners , this blog will focus on the analysis  of source code . not  the comprehensive illuminate of JVM .

# Class File Structure
*  **Magic  Number**
Only java class file's first 4 bytes code is｜ "0xCAFEBABE" , which give JVM a fast way to verify a file's format  .

*   **Version Number**
The next 4 bytes indicate the java version of this class file , in which the first two bytes represented minor version name , while  the other two bytes represented major version .

*  **Constant pool**
This section can be broken into two parts ,  pool count in first 2 bytes and the rest  is constant pool table , which store Strings , numbers  and references to classes and methods .

#  Basic Tools
*  *javac  $SourceFilePath*
        javac will compile java code to class file .

*  *javap -verbose $ClassPath*      
        javap  will disassembles a class file to  human-readable  format , we will analyze the result file to understand how class file describe  the java source code

#  Examples
*       **Simplest Function**

*Source Code*
```
public int additionFromTo(int a, int b) {
        for (int i = a; i < b; i++) {
            a=b+a+i;
        }
        return a;
    }
```
*Byte Code*

```
  Compiled from "JavaByteCode.java"
public class com.alfod.JavaByteCode
  minor version: 0
  major version: 52
  flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
   #1 = Methodref          #3.#14         // java/lang/Object."<init>":()V
   #2 = Class              #15            // com/alfod/JavaByteCode
   #3 = Class              #16            // java/lang/Object
   #4 = Utf8               <init>
   #5 = Utf8               ()V
   #6 = Utf8               Code
   #7 = Utf8               LineNumberTable
   #8 = Utf8               LocalVariableTable
   #9 = Utf8               this
  #10 = Utf8               Lcom/alfod/JavaByteCode;
  #11 = Utf8               fun
  #12 = Utf8               SourceFile
  #13 = Utf8               JavaByteCode.java
  #14 = NameAndType        #4:#5          // "<init>":()V
  #15 = Utf8               com/alfod/JavaByteCode
  #16 = Utf8               java/lang/Object
{
  public com.alfod.JavaByteCode();
    descriptor: ()V                      // this is  the  default init function of the class , usually produced by java compiler , Types in () means the arguments  for this method , right words indicate  the return type.
    flags: ACC_PUBLIC              //method's access  permission is  public
    Code:
      stack=1, locals=1, args_size=1    //operand stack's depth is 1 , local variable  table's length  is 1 ,   arguments of this method is 1
         0: aload_0                                                    
         1: invokespecial #1                  //call initialization  Method java/lang/Object."<init>":()V
         4: return
      LineNumberTable:                        //this  table store the correspondence  of line numbers between  source code and byte code
        line 3: 0
      LocalVariableTable:                           // as the name suggests
        Start  Length  Slot  Name   Signature
            0       5     0  this   Lcom/alfod/JavaByteCode;


public int additionFromTo(int, int);
descriptor: (II)I
flags: ACC_PUBLIC
Code:
  stack=2, locals=4, args_size=3
     0: iload_1                                   //load  the second elememt ( value of  a )  of the LocalVariableTable  onto operand stack
     1: istore_3                      //store the top of  stact ( just the value of a , loaded in last instruction ) in LocalVariableTable  at the  froth index ( where stores the value of variable i );  equals code " i=a"
     2: iload_3        //push the  forth element  ( value of variable i ) of LocalVariableTable onto stack
     3: iload_2    //push the third element ( value of variable b )  of LocalVariableTable onto stack
     4: if_icmpge     19           //compare the value of the top two stack ,  for this instruction ,  if the result is false ( means "i < b" )  execute the next instruction , if the result is false ( means that "i >=  b" )   branch to instruction at number 19  
     7: iload_2                 //push the third element ( value of variable b )  of LocalVariableTable onto stack
     8: iload_1             //load  the second elememt ( value of  a )  of the LocalVariableTable  onto operand stack
     9: iadd            //add two ints  loaded just now  ( equivalent to "b+a ")
    10: iload_3         //push the  forth element  ( value of variable i ) of LocalVariableTable onto stack
    11: iadd             //add two ints  ( one is the result of " b+a " , the other is "i" ,  equivalent to "b+a+i " )
    12: istore_1     //store the result at the 1 in LocalVariableTable ( where stores the value of a )
    13: iinc          3, 1              // increment LocalVariableTable at index 3 by 1 ( equivalent to "i++" )
    16: goto          2                 //jump to line 2
    19: iload_1                         //  //load  the second elememt ( value of  a )  of the LocalVariableTable  onto operand stack
    20: ireturn.                //return stack top ( just the value of a loaded in last instruction )
  LineNumberTable:
    line 6: 0     //line number 6  ( "for (int i = a; i < b; i++) {"  ) in source code  correspondence to line number 0 ( " 0: iload_1" ) in  byte code
    line 7: 7             //line number 7  in source code (  " a=b+a+i;" ) correspondence to line number 7 ( " iload_2  ")  in  byte code   
    line 6: 13           //line number 6  ( "for (int i = a; i < b; i++) {"  ) in source code  correspondence to line number 0 ( "iinc          3, 1"  ) in  byte code
    line 9: 19               //line number 9  ( " return a; "  ) in source code  correspondence to line number 0 ( "iload_1"  ) in  byte code
  LocalVariableTable:
    Start  Length  Slot  Name   Signature
        2      17     3     i   I
        0      21     0  this   Lcom/alfod/JavaByteCode;
        0      21     1     a   I
        0      21     2     b   I
  StackMapTable: number_of_entries = 2
    frame_type = 252 /* append */
      offset_delta = 2
      locals = [ int ]
    frame_type = 250 /* chop */
      offset_delta = 16
}
```
