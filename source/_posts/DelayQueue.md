---
title: 详细解析DelayQueue
date: 2016-10-08 21:39:13
tags:
---
DelayQueue是java 的concurrent包下面的一个类，他的作用就是一个延迟队列。一个存入到队列的对象在延迟一段时间后才能在队列中被获取到，如果没有能够拿到的对象，队列就阻塞。
延迟队列的特性非常适合作为缓存，缓存的对象在存储一段时间后就通过延迟队列进行释放，避免造成内存泄露。这种方式做缓存也避免了存储数据过多时，进行遍历清理造成的性能损耗。
研究延迟队列的源码。
DelayQueue中存储的对象是Delayed接口，需要实现getDelay函数，返回目前对象还需要延迟多久。
DelayQueue中有以下几个成员变量：
private final transient ReentrantLock lock = new ReentrantLock();
//offer和take时对对象加锁
private final PriorityQueue<E> q = new PriorityQueue<E>();
//优先队列，所有对象都存在这个优先队列中，每次take得到的就是delay剩余时间最短的对象，优先队列顺序的通过最小堆维护。
private Thread leader = null;
//是在线程做take操作的优化，因为一次仅有一个线程能够take到数据，其他线程就一直await直到被leader线程唤醒，否则会进行竞争，而竞争失败的线程又需要重新await，会造成性能损耗。
private final Condition available = lock.newCondition();
//用condition做线程通信
	
    public boolean offer(E e) {
        final ReentrantLock lock = this.lock;
        lock.lock();
        try {
            q.offer(e);
            if (q.peek() == e) {
                leader = null;
                available.signal();
            }
            return true;
        } finally {
            lock.unlock();
        }
    }

为阻塞队列添加新元素
1.先加锁保证线程安全
2.在优先队列中添加元素。优先队列通过最小堆的方式维护，保证每次take到的元素是最小的
3.如果原队列为空，将leader置为空，并唤醒await的线程
4.最后释放锁

    public E take() throws InterruptedException {
        final ReentrantLock lock = this.lock;
        lock.lockInterruptibly();
        try {
            for (;;) {
                E first = q.peek();
                if (first == null)
                    available.await();
                else {
                    long delay = first.getDelay(NANOSECONDS);
                    if (delay <= 0)
                        return q.poll();
                    first = null; // don't retain ref while waiting
                    if (leader != null)
                        available.await();
                    else {
                        Thread thisThread = Thread.currentThread();
                        leader = thisThread;
                        try {
                            available.awaitNanos(delay);
                        } finally {
                            if (leader == thisThread)
                                leader = null;
                        }
                    }
                }
            }
        } finally {
            if (leader == null && q.peek() != null)
                available.signal();
            lock.unlock();
        }
    }

获取到队列的第一个元素，如果没有元素到达延迟时间就进行等待直到有元素达到延迟时间
1.申请可中断锁
2.循环获取队列元素，获取到元素才跳出循环
3.如果优先队列为空，则阻塞，等有元素插入被唤醒
4.获取队列最小元素的延迟时间，如果延迟小于0，则表示该元素以达到延迟时间，可以被取出。
5.如leader不为空，表示当前线程不是leader线程，当前线程要永久等待，直到被leader线程唤醒。
6.如果leader为空，那么此线程是第一个获取元素