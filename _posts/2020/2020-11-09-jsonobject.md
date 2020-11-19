---
layout: post
title: JSON、JSONObject 与 JSONArray 简单讲解
category: json
tags: [json]
excerpt: JSON是互联网开发过程中应用最广泛的一种数据类型，不管是在后端API接口中，还是在前端都能得到广泛应用。
---

# 前言

JSON是互联网开发过程中应用最广泛的一种数据类型，不管是在后端API接口中，还是在前端都能得到广泛应用。今天就给大家介绍一下JSON的几种数据类型结构 。  

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201109163050916.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0OTY3Nzcw,size_16,color_FFFFFF,t_70#pic_center)

# fastjson简介

fastjson 是阿里巴巴的开源JSON解析库，它可以解析 JSON 格式的字符串，支持将 Java Bean 序列化为 JSON 字符串，也可以从 JSON 字符串反序列化到 JavaBean。比较牛的是下面这句话：
>fastjson 相对其他 JSON 库的特点是快，从 2011 年 fastjson 发布1.1.x版本之后，其性能从未被其他 Java 实现的 JSON 库超越。

附上其 maven 库坐标：  
```
<dependency>
<groupId>com.alibaba</groupId>
<artifactId>fastjson</artifactId>
<version>1.2.61</version>
</dependency>
```
# 什么是 JSON

在最开始接触 JSON 的时候，不太明白 JSON 的概念，感觉很笼统。其实 JSON 就是一种轻量级的数据交换格式，被广泛应用于 WEB 应用程序开发。

## JSON 的优势
- JSON 的简洁 和 清晰的层次结构。  
- 易于人阅读和编写，同时也易于机器解析和生成。  
- 有效的提升网络传输效率。  
- 支持多种语言，很多流行的语言都对 JSON 格式有着很友好的支持。  

## JSON 对象

这就是一个平常使用的 JSON 对象，特征就是多个属性是被 {} 括起来的。  
```
{

"area": "山东菏泽",

"name": "马马马马马百万",

"age": 25

}
```
## JSON 数组

JSON 数组其实就是包含了多个 JSON 对象的一个集合，数组是以 数组括号 [] 括起来的。  
```
[{

"area": "广东",

"name": "山溪",

"age": 25

}, {

"area": "沙西",

"name": "马希泰",

"age": 26

}]
```
JSON 数组并一定是要相同的 JSON 对象的集合，也可以是不同的对象，不过我在开发过程中并没有这么使用过，感觉挺别扭的。因为如果将多个对象放进一个 JSONArray 中的话，下意识认为是相同类型的集合。    
下面这种类型也是正确的 JSON 数组格式：  
```
[{

"area": "江苏",

"name": "徐州",

"age": 25

}, {

"drugCode": "HXUDP000000000000MED0000342809",

"dosformName": "注射",

"drugName": "打点滴"

}]
```
JSON、JSON对象、JSON数组区别：  
JSON 呢只是一种宏观上的叫法，可以理解为是一种数据结构，就像 xml 结构一样，是一种规约性内容；而 JSON 对象则是对 JSON 的具体体现；JSON 数组则是将多个 JSON 对象进行存储的一个集合，可以想象成 Java 中的 List 和 Object 的关系。

# 什么是 JSONObject

JSONObject 是根据 JSON 形式在 Java 中存在的对象映射。  
各大 JSON 类库的 JSONObject 内部实现也是不太一样的。这里以 fastjson 举例：
```
public class JSONObject extends JSON implements Map<String, Object>, Cloneable, Serializable, InvocationHandler {

private static final long serialVersionUID = 1L;

private static final int DEFAULT_INITIAL_CAPACITY = 16;

private final Map<String, Object> map;

public JSONObject(){

this(DEFAULT_INITIAL_CAPACITY, false);

}

public JSONObject(Map<String, Object> map){

if (map == null) {

throw new IllegalArgumentException("map is null.");

}

this.map = map;

}

// 。。。。省略其余内容

}
```
可以看到 就是对 HashMap 的一层封装，并提供了一些个性化方法。有兴趣的话可以去研究下源码实现，这里就不一一解读了。  
其使用方式和 HashMap 并无太大区别。
```
JSONObject person = new JSONObject();

person.put("name", "马马马马马百万");

person.put("age", 25);

person.put("area", "山东菏泽");

JSONObject drug = new JSONObject();

drug.put("drugName", "盐酸丁卡因注射液");

drug.put("drugCode", "HXUDP000000000000MED0000342809");

drug.put("dosformName", "注射剂");
```
 
# 什么是 JSONArray

其实刚才拿 List 与 Object 关系举例是有迹可查的。  
仅针对于 fastjson 而言，因为没看别的具体实现。  
存放数据的容器就是一个 List 的类型，默认创建为 ArrayList ，但不仅限于。
```
public class JSONArray extends JSON implements List<Object>, Cloneable, RandomAccess, Serializable {

private static final long serialVersionUID = 1L;

private final List<Object> list;

protected transient Object relatedArray;

protected transient Type componentType;

public JSONArray(){

this.list = new ArrayList<Object>();

}

public JSONArray(List<Object> list){

this.list = list;

}

public JSONArray(int initialCapacity){

this.list = new ArrayList<Object>(initialCapacity);

}

// 。。。省略余下部分

}
```
JSONArray 的平常使用方法（内心独白：不就是一个 List 中 套了个 Map 类结构嘛 ）：
```
JSONObject person = new JSONObject();

person.put("name", "马马马马马百万");

JSONObject drug = new JSONObject();

drug.put("drugName", "盐酸丁卡因注射液");

JSONArray array = new JSONArray();

array.add(person);

array.add(drug);
```
# Fastjson 的简单应用

将对象转换为json字符串：
```
import com.alibaba.fastjson.JSON;

String jsonStr = JSON.toJSONString(person);
```
将json字符串转换为JSONObject对象：
```
JSONObject jsonObject = JSON.parseObject(jsonStr);
```
将JSONObject 转换为对象：
```
JSONObject person = new JSONObject();

person.put("name", "马马马马马百万");

person.put("age", 25);

Student stuObj = JSON.toJavaObject(person, Student.class);
```
# 结束语

为了前期理解，上述文章中并没有涉及到复杂 JSON 对象。下述 JSON 中涉及到了 数值、字符串、内置 JSON 对象、数组：
```
{

"article": {

"row": 1024,

"lastUpdateTime": "2019-12-13 19:27:59",

"Title": "Java 中 JSONArray 与 JSONObject 详细介绍结构及应用",

"images": {

"Url": "",

"Height": 256,

"Width": "256"

},

"sections": [17, 18, 19, 20]

}

}
```
  
---
> 本文为转载文章，转载请标明出处:
原文链接: https://baijiahao.baidu.com/s?id=1652966908554903975&wfr=spider&for=pc
本文链接: http://sfpyblog.com//json/2020/11/09/jsonobject.html





