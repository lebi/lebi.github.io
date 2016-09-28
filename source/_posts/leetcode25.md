---
title: Reverse Nodes in k-Group
date: 2016-06-17 15:53:11
tags: [leetcode]
---
## Reverse Nodes in k-Group
Given a linked list, reverse the nodes of a linked list k at a time and return its modified list.  
If the number of nodes is not a multiple of k then left-out nodes in the end should remain as it is.  
You may not alter the values in the nodes, only nodes itself may be changed.  
Only constant memory is allowed.  
For example,
Given this linked list: 1->2->3->4->5  
For k = 2, you should return: 2->1->4->3->5  
For k = 3, you should return: 3->2->1->4->5
## 题意
给定一个链表和一个值k，使链表以k为宽度进行反转，小于k的部分进行保留。在变换过程中不能修改节点的值，且只能使用常量的内存空间。  
例如：输入链表1->2->3->4->5  
如果k=2，需要返回2->1->4->3->5  
如果k=3，需要返回3->2->1->4->5
## 解题思路
这道题主要就是考察对于链表的操作，而且只能使用固定的内存空间，因此就是对链表进行遍历，每次遍历k个节点，将节点进行反转，然后将这部分链表的头节点指向下一部分链表的末尾。如果出现剩余节点小于k的情况，就需要调整头节点指向下部分链表的开头。  
例如1->2->3->4->5，k=2  
1.先定位头节点到2，这个节点将作为返回值  
2.将1->2进行翻转，翻转完成后向后遍历k个，如果能够遍历完k个，到第3步，否则到第4步  
3.将当前部分的头节点的next指向下一部分的末节点，即将1的next指向4，然后将当前的头移到下一部分的头节点即3这个位置。然后重复2,3两步  
4.此时剩余的节点个数小于k个，那么需要将当前部分的头节点的next指向下一部分的头，即将3的next指向5，然后返回head
```java
    public ListNode reverseKGroup(ListNode head, int k) {
        if(k==1||head==null)return head;
        ListNode start=head,end=head,next,it,itnext,itprev;
        it=head;
        for(int i=0;i<k-1;i++){
        	it=it.next;
        	if(it==null)return head;
        }
        head=it;
        end=head;
        while(true){
        	itprev=start;
			it=start.next;
			next=end.next;
    		for(int i=0;i<k-1;i++){
    			itnext=it.next;
    			it.next=itprev;
    			itprev=it;
    			it=itnext;
    		}
        	end=next;
        	for(int i=0;i<k-1;i++){
        		if(end==null||end.next==null) {
        			start.next=next;
        			return head;
        		}
        		end=end.next;
        	}
    		start.next=end;
    		start=next;
        }
    }
```