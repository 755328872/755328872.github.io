---
title: Java哈希表（Hash Table）
author: 花降
date: 2020-10-16 15:14:35
description: Java笔记1
categories:
- 花降的编程笔记
tags:
- Java
- 学习笔记
---
## Java哈希表(Hash Table)

最近做题经常用到哈希表来进行快速查询，遂记录Java是如何实现哈希表的。这里只简单讲一下利用Map和HashMap实现哈希表。

**首先，什么是Map和HashMap？**

 - Map<K, V>是一个以 键值(Key)-数值(Value) 对应形式存储数据的接口。
   在数组中我们是通过数组下标来对其内容索引的，而在Map中我们通过对象来对对象进行索引，用来索引的对象叫做key，其对应的对象叫做value。
 - HashMap是Map<K, V>的实现类。(Hashtable也是Map<K, V>的实现类) HashMap存储数据采用哈希表结构
   ，元素的存取顺序不能保证一致。但键值是唯一、不重复的。

导入Map和HashMap并建立哈希表

```java
import java.util.HashMap;
import java.util.Map;
```

下面演示如何创建一个哈希表

Map<K, V>在创建时，K和V应该填入对应Key和Value的数据类型。

```java
Map<String, Integer> Ages = new HashMap<String, Integer>(); 
```

// 这是用接口的形式实现哈希表
// Key是String类型，Value是Integer类型
那么有人会问了——诶？既然我HashMap就是一个实现类，那我干嘛不直接这么创建呢？

```java
HashMap<String, Integer> Ages = new HashMap<String, Integer>();
```

上面这种方法很不建议，因为Map接口下还有很多实现类，包括TreeMap等等。用第一种方法用接口实现，只需要替换后面的类。如果用第二种方法就需要重写，所以在这里推荐第一种方法创立哈希表。

**常用方法**                       
|Type|  Method |
|--|--|
|Value | get(Object key) 返回键映射的值，如果该键没有映射值，则返回null  |
|Value | put(K key, V value) 将键和值建立映射关系,如果键是第一次存储，就直接存储元素，返回                     null; 如果键不是第一次存在，就用值把以前的值替换掉，返回以前的值  |
|Value | remove(Object key) 如果对应键存在映射关系的值，则将其移除，并返回值  |
|boolean|   containsKey(Object key) 是否存在特定的key|
|boolean|  containsValue(Object value) 是否存在特定的value|
|boolean|   isEmpty() 判断集合是否为空|
|void |  clear()                     移除所有的Entry(键值对)|
|Set<Map.Entry<K,V>> | entrySet() 返回一个键值对的Set集合 |
|Set  |keySet()  获取集合中所有键的集合  |
|Collection  |values() 获取集合中所有值的集合  |
|int | size() 返回集合中的键值对的对数 |
|default V| getOrDefault(Object key, V defaultValue) Java8新特性，如果哈希表中有这个Key则获取这个值，如果没有则初始化Key的值为defaultValue |

例题：Leetcode 387题 First Unique Character in a String
Given a string, find the first non-repeating character in it and return its index. If it doesn't exist, return -1.

**Examples:**

```java
s = "leetcode"
return 0.
```

```java
s = "loveleetcode"
return 2.
```
哈希表解法：
```java
class First_Unique_Character_in_a_String {
    public int firstUniqChar(String s) {
        **//创建一个新的哈希表（其实就是python的字典……）**
        Map<Character,Integer> count = new HashMap<Character,Integer>();
        for(int i = 0; i < s.length(); i++){
            //逐个遍历字符串每个字母
            char c = s.charAt(i);
            //如果字母不在哈希表中，则通过getOrDefault方法创建，对应的值为0，再加1
            //哈希表中的put的方法可以创建新的键值对或者更新键对应的值。
            count.put(c, count.getOrDefault(c, 0) + 1);
        }
        //遍历每个哈希表中的键值对，如果只出现1次，则返回该值对应的索引。
        for (int r = 0; r < s.length(); r++){
            if (count.get(s.charAt(r)) == 1) {
                return r;
            }
        }
        //如果都重复了，就返回-1.
        return -1;
    }
}
```
