---
layout: post
title: scala练习4
date: 2015-3-02
categories: blog
tags: [scala]
description: 快学scala中的练习。

---

## scala课后练习4代码

```scala
package com.scala.practice4
//import scala.collection.mutable._
import scala.collection.JavaConversions._
object P4 {
  def main(args: Array[String]): Unit = {
    Equipment()
   // readWord1().keySet.foreach(k=>println(k+"出现"+readWord1()(k)+"次"))
    println("\n可变映射统计：")
    for((k,v)<-readWord1()) println(k+"出现了"+v+"次")
    println("\n不可变映射统计：")
    for((k,v)<-readWord2()) println(k+"出现了"+v+"次")
    println("\n排序的映射统计：")
    for((k,v)<-readWord3()) println(k+"出现了"+v+"次")
    println("\n利用java的映射统计：")
    for((k,v)<-readWord4()) println(k+"出现了"+v+"次")
    println("\n链式映射：")
    linkedHash().foreach(println)
    println("\nJava属性:")
    printJavaProperties()
  }
  def Equipment()={
    val equip:Map[String,Double]=Map("Equipment1"->27,"Equipment2"->20,"Equipment3"->17,"Equipment4"->28,"Equipment5"->19)
    val equip2=for((k,v)<- equip) yield (k->0.9*v)
    println("原始："+equip.mkString(" , ")+"\n9折："+equip2.mkString(" , "))
  }
  
  def readWord1()={
    val in=new java.util.Scanner(new java.io.File("resource/word.txt"))
    val count=new scala.collection.mutable.HashMap[String,Int]
    while(in.hasNext()){
      for(w<-in.nextLine().split("[ ,!.\"\'/]") if(!w.equals("")))
        count(w)=count.getOrElse(w, 0)+1
    }
    count
  }
  
  def readWord2()={
    val in=new java.util.Scanner(new java.io.File("resource/word.txt"))
    var count=Map.empty[String,Int]
    while(in.hasNext()){
      for(w<-in.nextLine().split("[ ,!.\"\'/]") if(!w.equals("")))
      count+=(w->(count.getOrElse(w, 0)+1))
    }
    count
  }
  
  def readWord3()={
    val in=new java.util.Scanner(new java.io.File("resource/word.txt"))
    var count=scala.collection.immutable.SortedMap.empty[String,Int]
    while(in.hasNext()){
      for(w<-in.nextLine().split("[ ,!.\"\'/]") if(!w.equals("")))
      count+=(w->(count.getOrElse(w, 0)+1))
    }
    count
  }
  
  def readWord4()={
    val in=new java.util.Scanner(new java.io.File("resource/word.txt"))
    var count=new java.util.TreeMap[String,Int]
    while(in.hasNext()){
      for(w<-in.nextLine().split("[ ,!.\"\'/]") if(!w.equals(""))){
        if(count.containsKey(w))
          count.put(w,count.get(w)+1)
        else count.put(w, 1)
      }
    }
    mapAsScalaMap(count)
  }
  def linkedHash()={
    val map=new scala.collection.mutable.LinkedHashMap[String,Int]
    map+=("Monday"->java.util.Calendar.MONDAY)
    map+=("Tuesday"->java.util.Calendar.TUESDAY)
    map+=("Thursday"->java.util.Calendar.THURSDAY)
    map+=("Wednesday"->java.util.Calendar.WEDNESDAY)
    map+=("Friday"->java.util.Calendar.FRIDAY)
    map+=("Saturday"->java.util.Calendar.SATURDAY)
    map+=("Sunday"->java.util.Calendar.SUNDAY)
    map
  }
  def printJavaProperties()={
    val pro:scala.collection.Map[String,String]=System.getProperties()
    val keysL=for(key<-pro.keySet) yield key.length()
    for(p<-pro.keySet){
      print(p+" "*(keysL.max-p.length())+"|"+pro.get(p)+"\n")
    }
  }
}
```
