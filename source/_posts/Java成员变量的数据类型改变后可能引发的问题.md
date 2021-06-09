---
title: Java成员变量的数据类型改变后可能引发的问题
date: 2021-06-09 15:16:32
tags: java
categories:
- Java
- Java 基础
toc: false
---

类型改变，如 Integer 改为 String 或 Integer 改为 Long。

## if (xxx.equals(Object obj))

当成员变量的数据类型改变后，直接量与成员变量比较时，比较结果可能和之前的结果不同。如：

```
new Integer(1).equals(new Long(1)) // 返回 false
new Integer(1).equals("1") // 返回 false
new Long(1).equals(1) // 返回 false
```

发生错误的场景，如：

```
Integer type1 = 1;
if (type1.equals(type)) {
	...
}
```

解决方法：与该成员变量的比较的值，数据类型要保持一致。

## map.get(Object key)

若该变量作为 map 的 key 时，可能出现新的数据类型在 map 中找不到对应的 key。

HashMap put(key, value) 方法有指定数据类型的约束，而 get(Object key) 方法没有对应的数据类型约束。字段类型改变后，put 方法有错误提示，而 get 方法没有提示。在 map 对象中不同的数据类型的 key 是不同的 key，从而会出现改完数据类型后找不到对应的 key。

```
Map<Integer, String> map = new HashMap()
```

解决方法：put(key, value) 和 get(Object key) 的 key 的数据类型要保持一致。

## 方法的重载 

若该变量作为方法的参数时，存在多个重载方法，改完类型后，可能调用的不是之前的方法。如存在重载方法 getById(Integer id) 和 getById(Serializable id)。

解决方法：检查该成员变量作为参数调用方法的地方，跳转的方法是不是之前的方法。
