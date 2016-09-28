---
title: Median of Two Sorted Arrays
date: 2016-06-07 19:02:54
tags: [leetcode]
---
## Leetcode: Median of Two Sorted Arrays
There are two sorted arrays nums1 and nums2 of size m and n respectively. Find the median of the two sorted arrays. The overall run time complexity should be O(log (m+n)).

## 题意
两个排序好的数组num1和num2的大小分别为m和n，找到数组的中位数，算法整体时间复杂度应该为O(log (m+n))。

## 解题思路
#### 方法1
&emsp;&emsp;找到两个排序数组的中位数，非常容易想到的方法就是遍历两个数组，从小到大的寻找。因为是中位数，可能出现在两个数之间，所以使用一个长度为(m+n)/2+1的数组保存遍历的数，最后直接取对应位置的数即可。  
&emsp;&emsp;这种方法的思路很简单，但是在效率上不高，虽然时间复杂度还是O(log (m+n))，但是其实存在很多不必要的比较，因为已经知道需要寻找的是第几个数了，最后在leetcode上运行时间为7ms，仅超过5%的提交。
#### 代码实现
```java
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
    	int m=nums1.length,n=nums2.length;
    	
    	int all=m+n;
    	int[] arr=new int[all/2+1];
    	int m1=0,n1=0,i=0;
    	
    	while(i<arr.length){
    		if(m1==nums1.length)
    			arr[i++]=nums2[n1++];
    		else if(n1==nums2.length)
    			arr[i++]=nums1[m1++];
    		else{
    			if(nums1[m1]>nums2[n1])
    				arr[i++]=nums2[n1++];
    			else
    				arr[i++]=nums1[m1++];
    		}
    	}

    	if(all==1)
    		return arr[0];
    	if(all%2==0)
    		return (arr[all/2-1]+arr[all/2])/2.0;
    	else
    		return arr[all/2];
    }
```
#### 方法2
&emsp;&emsp;还有一种使用参考二分法的做法来解这题。  
&emsp;&emsp;对于两个长度为m和n的数组，从中找到第k小的数，取M[k/2]和N[k/2]两个数做比较，如果M[k/2]较大，那么目标在N[k/2]之后，继续在两个长度为m和n-k/2的数组中寻找第k/2小的数，反之亦然。如果取第i(i>k/2)个数比较M[i]和N[i]，即使M[i]>N[i]，那么目标数也可能会出现在n[i]之前。  
&emsp;&emsp;在实现过程中还要考虑如果数组长度n小于k/2的时候，那就应该用n代替k/2。  
&emsp;&emsp;最后在leetcode上提交运行时间为5ms，与大部分提交时间相同。
#### 代码实现
```java
	public int find(int[] arr1,int s1,int m,int[] arr2,int s2,int n,int k){
		int r1=m-s1;  
		int r2=n-s2;
		//比较数组剩余长度，使arr2小于arr1
		if(r1<r2)
			return find(arr2, s2, n,arr1,s1,m, k);
		if(r2==0)
			return arr1[k+s1-1];
		if(k==1)
			return Math.min(arr1[s1], arr2[s2]);
		
		//arr2的向后移动距离
		int p2=Math.min(k/2, r2);
		int p1=k-p2;
		
		if(arr1[p1+s1-1]>arr2[p2+s2-1])
			//arr1较大，arr2向后移动
			return find(arr1,s1,m,arr2,s2+p2,n,k-p2);
		else if(arr1[p1+s1-1]<arr2[p2+s2-1])
			//反之，arr1向后移动
			return find(arr1,s1+p1,m,arr2,s2,n,k-p1);
		return arr1[p1+s1-1];
	}
	
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
    	int m=nums1.length;
    	int n=nums2.length;
    	
    	int all=m+n;
    	if(all%2==0){
    		return (find(nums1,0,m,nums2,0,n,all/2)+find(nums1,0,m,nums2,0,n,all/2+1))/2.0;
    	}else
    		return find(nums1,0,m,nums2,0,n,all/2+1);
    }
```