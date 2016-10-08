---
title: 详细解析DelayQueue
date: 2016-10-08 21:39:13
tags:
---
DelayQueue是java concurrent包下面的一个类，他的作用就是一个延迟队列。一个存入到队列的对象在延迟一段时间后才能在队列中被获取到，如果没有能够拿到的对象，队列就阻塞。
延迟队列的特性非常适合作为缓存，缓存的对象在存储一段时间后就通过延迟队列进行释放，避免造成内存泄露。这种方式做缓存也避免了存储数据过多时，进行遍历清理造成的性能损耗。
研究延迟队列的源码。
DelayQueue中存储的对象是Delayed接口，需要实现getDelay函数，返回目前对象还需要延迟多久。
DelayQueue中有以下几个成员变量：
private final transient ReentrantLock lock = new ReentrantLock();
//offer和take时对对象加锁
private final PriorityQueue<E> q = new PriorityQueue<E>();
//优先队列，所有对象都存在这个优先队列中，每次take得到的就是delay剩余时间最短的对象，优先队列顺序的通过最小堆维护。
private Thread leader = null;
//