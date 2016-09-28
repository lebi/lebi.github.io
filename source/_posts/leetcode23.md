---
title: Merge k Sorted Lists
date: 2016-06-16 12:21:39
tags: [leetcode]
---
## Leetcode: Merge k Sorted Lists
Merge k sorted linked lists and return it as one sorted list. Analyze and describe its complexity.
## 题意
将k个有序链表合并成一个有序链表，分析算法的复杂度。
## 集体思路
对于k个有序链表的合并，使用分而治之的思路，先让两个链表合并得到一个新链表，再将新链表进行合并。算法的时间复杂度是log(k)\*(k\*n)，n表示链表长度的平均值。空间复杂度为O(1)。提交后在leetcode上运行时间在前30%。
#### 代码实现
```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
public class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
    	return merge(lists,0,lists.length-1);
    }
    
    public ListNode merge(ListNode[] lists,int a,int b){
    	if(lists.length==0) return null;
    	if(a<b){
    		int mid=(a+b)/2;
    		ListNode l1=merge(lists,a,mid);
    		ListNode l2=merge(lists,mid+1,b);
    		return mergerTwo(l1,l2);
    	}else 
    		return lists[a];
    }
    
    public ListNode mergerTwo(ListNode l1,ListNode l2){
    	ListNode head=new ListNode(0);
    	ListNode it=head;
    	while(l1!=null&&l2!=null){
    		int min;
    		if(l1.val>l2.val){
    			min=l2.val;
    			l2=l2.next;
    		}else{
    			min=l1.val;
    			l1=l1.next;
    		}
    		it.next=new ListNode(min);
    		it=it.next;
    	}
    	while(l1!=null){
    		it.next=new ListNode(l1.val);
    		l1=l1.next;
    		it=it.next;
    	}

    	while(l2!=null){
    		it.next=new ListNode(l2.val);
    		l2=l2.next;
    		it=it.next;
    	}
    	return head.next;
    }
}
```