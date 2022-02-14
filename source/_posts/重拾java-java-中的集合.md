---
title: '重拾java: jav中的集合（collection）'
date: 2022-02-14 16:33:41
tags:
---
集合
---
Java的中的 集合框架包含两种类型的容器: 集合（collection）,
图（map）。

###集合（collection）
collection有三种子类型：List,Set,Queue。 再是一些抽象是实现类。
最后是具体实现类。常用的有：ArrayList,LinkedList,HashSet等等、

###图（map）
遍历map的四种方法
1. iterator 遍历
collection接口要求所有的子类需要实现iterator()方法
返回iterator，注意：iterator()方法是java.lang.Iterable接口,被Collection继承。
```
        //通过 Iterator 遍历实现
        List<Integer> list = new ArrayList<Integer>();
        list.add(1);
        list.add(2);
        list.add(3);
        for(Iterator itr = list.listIterator();itr.hasNext();){
            System.out.println(itr.next());
        }
```
    

