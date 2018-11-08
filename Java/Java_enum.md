---
title: java enum 枚举
date: 2017-09-26 23:01:00
tags: java enum
---
今天阅读阿里巴巴编码规范很是佩服马云公司这个分享的精神。正凑巧本人公司也正在规范开发规范，所以顺便拿过来拜读一下。这里关于编码规范不做展开，后续会有关于阿里巴巴编码规范的读后感。
当读到关于java enum命名规范时，突然想深入的了解一下这个常用的java 枚举类型到底是个什么东西。

#### 基础声明
 > enum 是在JDK 1.5 之后提供的，它提供了一组有意义常量的集合，而常量的范围很固定的。

具体声明:
```
public enum Week {
    MON,TUE,WED,THU,FRI,SAT,SUN;
}
```
#### 简单使用
```
//遍历
for(Week day : Week.values()){
    System.out.println(day);
}
		
//switch用法
Week day = Week.MON;
switch(day){
case MON:
    System.out.println("Today is Monday!");
    break;
case TUE:
	System.out.println("Today is Tuesday!");
	break;
case WED:
    System.out.println("Today is Wednesday!");
    break;
case THU:
    System.out.println("Today is Thursday!");
    break;
case FRI:
    System.out.println("Today is Friday!");
    break;
case SAT:
    System.out.println("Today is Saturday and the weekend!");
    break;
case SUN:
    System.out.println("Today is Sunday and the weekend!");
    break;
default:
    System.out.println("This is not one day of the week!");
}
```

#### 常用方法
> 需要注意的一点是，Enum的对象Week，而Week里面的所有声明的参数都是Week类型。例如下图所示：

![Week's method]($res/Week's%20method.png)
> 注意到Week的所有方法只有三个
```
valueOf(String arg0):Week
values():Week[]
static valuesOf(Class<T> enumType,String name):T
```
#### 实践案例
TODO
#### 刨根分析
 TODO
 