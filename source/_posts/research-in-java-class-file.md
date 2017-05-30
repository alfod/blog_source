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
*       **For Loop**

*Source Code*
```
package com.alfod;

public class JavaByteCode {
    public int additionFromTo(int a, int b) {
            for (int i = a; i < b; i++) {
                a=b+a+i;
            }
            return a;
    }
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

*  **Accessor  And Mutator Of  Object**
*Source Code*
```
package com.alfod;

public class JavaByteCode {
    private String name;

    public void setName(String name) {
        this.name = name;
    }

    public String getName() {
        return this.name;
    }
}
```

*Byte Code*

```
public class com.alfod.JavaByteCode
  minor version: 0
  major version: 52
  flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
   #1 = Methodref          #4.#27         // java/lang/Object."<init>":()V
   #2 = Fieldref           #3.#28         // com/alfod/JavaByteCode.name:Ljava/lang/String;
   #3 = Class              #29            // com/alfod/JavaByteCode
   #4 = Class              #30            // java/lang/Object
   #5 = Utf8               name
   #6 = Utf8               Ljava/lang/String;
   #7 = Utf8               <init>
   #8 = Utf8               ()V
   #9 = Utf8               Code
  #10 = Utf8               LineNumberTable
  #11 = Utf8               LocalVariableTable
  #12 = Utf8               this
  #13 = Utf8               Lcom/alfod/JavaByteCode;
  #14 = Utf8               additionFromTo
  #15 = Utf8               (II)I
  #16 = Utf8               i
  #17 = Utf8               I
  #18 = Utf8               a
  #19 = Utf8               b
  #20 = Utf8               StackMapTable
  #21 = Utf8               setName
  #22 = Utf8               (Ljava/lang/String;)V
  #23 = Utf8               getName
  #24 = Utf8               ()Ljava/lang/String;
  #25 = Utf8               SourceFile
  #26 = Utf8               JavaByteCode.java
  #27 = NameAndType        #7:#8          // "<init>":()V
  #28 = NameAndType        #5:#6          // name:Ljava/lang/String;
  #29 = Utf8               com/alfod/JavaByteCode
  #30 = Utf8               java/lang/Object
{
  public com.alfod.JavaByteCode();
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
            0       5     0  this   Lcom/alfod/JavaByteCode;

public void setName(java.lang.String);
    descriptor: (Ljava/lang/String;)V
    flags: ACC_PUBLIC
    Code:
      stack=2, locals=2, args_size=2
         0: aload_0      //     load a reference from LocalVariableTable at index 0 ( this pointer )               
         1: aload_1   // load a reference from LocalVariableTable at index  1 ( method's argument "name"  )
         2: putfield      #2                  //assign the value of ' name ' to the attribute named 'name' of this  ;
         5: return  
      LineNumberTable:
        line 14: 0
        line 15: 5
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0       6     0  this   Lcom/alfod/JavaByteCode;
            0       6     1  name   Ljava/lang/String;

  public java.lang.String getName();
    descriptor: ()Ljava/lang/String;
    flags: ACC_PUBLIC
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0                     //load a  reference from LocalVariableTable at index 0 ( this pointer )
         1: getfield      #2                  // push the value of this's attribute 'name' onto stack
         4: areturn
      LineNumberTable:
        line 18: 0
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0       5     0  this   Lcom/alfod/JavaByteCode;
}
SourceFile: "JavaByteCode.java"
```

*       **Genericity**
*Source Code*
```
package com.alfod;

import java.util.LinkedList;
import java.util.List;

public class JavaGenericity {
    public List<String> toList(String... strings){
        List<String> list=new LinkedList<>();
        for(String s:strings){
            list.add(s);
        }
        return list;
    }
}

```
*Byte Code*
```
public class com.alfod.JavaGenericity
  minor version: 0
  major version: 52
  flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
   #1 = Methodref          #6.#32         // java/lang/Object."<init>":()V
   #2 = Class              #33            // java/util/LinkedList
   #3 = Methodref          #2.#32         // java/util/LinkedList."<init>":()V
   #4 = InterfaceMethodref #34.#35        // java/util/List.add:(Ljava/lang/Object;)Z
   #5 = Class              #36            // com/alfod/JavaGenericity
   #6 = Class              #37            // java/lang/Object
   #7 = Utf8               <init>
   #8 = Utf8               ()V
   #9 = Utf8               Code
  #10 = Utf8               LineNumberTable
  #11 = Utf8               LocalVariableTable
  #12 = Utf8               this
  #13 = Utf8               Lcom/alfod/JavaGenericity;
  #14 = Utf8               toList
  #15 = Utf8               ([Ljava/lang/String;)Ljava/util/List;
  #16 = Utf8               s
  #17 = Utf8               Ljava/lang/String;
  #18 = Utf8               strings
  #19 = Utf8               [Ljava/lang/String;
  #20 = Utf8               list
  #21 = Utf8               Ljava/util/List;
  #22 = Utf8               LocalVariableTypeTable
  #23 = Utf8               Ljava/util/List<Ljava/lang/String;>;
  #24 = Utf8               StackMapTable
  #25 = Class              #36            // com/alfod/JavaGenericity
  #26 = Class              #19            // "[Ljava/lang/String;"
  #27 = Class              #38            // java/util/List
  #28 = Utf8               Signature
  #29 = Utf8               ([Ljava/lang/String;)Ljava/util/List<Ljava/lang/String;>;
  #30 = Utf8               SourceFile
  #31 = Utf8               JavaGenericity.java
  #32 = NameAndType        #7:#8          // "<init>":()V
  #33 = Utf8               java/util/LinkedList
  #34 = Class              #38            // java/util/List
  #35 = NameAndType        #39:#40        // add:(Ljava/lang/Object;)Z
  #36 = Utf8               com/alfod/JavaGenericity
  #37 = Utf8               java/lang/Object
  #38 = Utf8               java/util/List
  #39 = Utf8               add
  #40 = Utf8               (Ljava/lang/Object;)Z
{
  public com.alfod.JavaGenericity();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: return
      LineNumberTable:
        line 9: 0
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0       5     0  this   Lcom/alfod/JavaGenericity;

  public java.util.List<java.lang.String> toList(java.lang.String...);
    descriptor: ([Ljava/lang/String;)Ljava/util/List;
    flags: ACC_PUBLIC, ACC_VARARGS
    Code:
      stack=2, locals=7, args_size=2
         0: new           #2                  // initialize the class java/util/LinkedList and push the reference onto stack
         3: dup         //duplicate the  top element of stack
         4: invokespecial #3                  // perform the initialization of LinkedList and push the reference onto the stack
         7: astore_2        //store the reference of LinkedList on the stack in LocalVariableTable at index 2
         8: aload_1     //load the element of LocalVariableTable at index 1 onto stack
         9: astore_3    //store the reference on stack in LocalVariableTable at index 3
        10: aload_3      //load the element of LocalVariableTable at index 3 onto stack
        11: arraylength     //get the length of  array loaded last instruction  and push on to stack
        12: istore        4         //store the length in LocalVariableTable at index  4
        14: iconst_0        //load the int value 0 onto the stack
        15: istore        5         //store the int value 0 in LocalVariableTable  at index 5
        17: iload         5             //load the element from LocalVariableTable at index 5 onto stack
        19: iload         4         //load the length of arguments array length
        21: if_icmpge     45        //compare the arguments array length and the value in LocalVariableTable at index 5 , if array lenght is less than the value in LocalVariableTable at index 5 , then goto line number 45
        24: aload_3         //load  the tmp String reference 's'
        25: iload         5         //load the element of LocalVariableTable at index 5
        27: aaload          // get the value of element   arguments array whose index is the value of element from LocalVariableTable at index 5
        28: astore        6         //store the value get in last instruction in LocalVariableTable at index 6
        30: aload_2         //push the reference of list  onto stack
        31: aload         6         //push the value in LocalVariableTable at index 6 onto stack
        33: invokeinterface #4,  2            //the list add the value in LocalVariableTable at index 6  
        38: pop     //pop the top element of stack
        39: iinc          5, 1      //the tmp value in LocalVariableTable at index 5 add 1
        42: goto          17            //goto line number 17
        45: aload_2         //load the list reference
        46: areturn     //return the list reference
      LineNumberTable:
        line 11: 0
        line 12: 8
        line 13: 30
        line 12: 39
        line 15: 45
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
           30       9     6     s   Ljava/lang/String;
            0      47     0  this   Lcom/alfod/JavaGenericity;
            0      47     1 strings   [Ljava/lang/String;
            8      39     2  list   Ljava/util/List;
      LocalVariableTypeTable:
        Start  Length  Slot  Name   Signature
            8      39     2  list   Ljava/util/List<Ljava/lang/String;>;
      StackMapTable: number_of_entries = 2
        frame_type = 255 /* full_frame */
          offset_delta = 17
          locals = [ class com/alfod/JavaGenericity, class "[Ljava/lang/String;", class java/util/List, class "[Ljava/lang/String;", int, int ]
          stack = []
        frame_type = 248 /* chop */
          offset_delta = 27
    Signature: #29                          // ([Ljava/lang/String;)Ljava/util/List<Ljava/lang/String;>;
}
SourceFile: "JavaGenericity.java"


```
