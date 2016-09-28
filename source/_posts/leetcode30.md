---
title: Substring with Concatenation of All Words
date: 2016-06-16 14:36:58
tags: [leetcode]
---
## Leetcode:Substring with Concatenation of All Words
You are given a string, s, and a list of words, words, that are all of the same length. Find all starting indices of substring(s) in s that is a concatenation of each word in words exactly once and without any intervening characters.  
For example, given:  
s: "barfoothefoobarman"  
words: ["foo", "bar"]  
You should return the indices: [0,9].
(order does not matter).
## 题意
输入一个字符串s和一个字母等长的字符串数组words，在s中找到一个由words中所有单词组成的子串的起始位置，且words的单词只在子串中出现一次，并且中间也没有插入其他字符。  
输入:  s:"barfoothefoobarman"和words:["foo", "bar"]  
s中符合条件的子串为barfoo和foobar，这两个子串的初始下标分别为0和9。
## 解题思路
&emsp;&emsp;题目中有一个很重要的点就是words中的单词都是等长的，针对这一点，从s串中截取一定长度的串，以map作为辅助检查这个子串是不是符合条件的串。按照这个思路写完代码后发现会超时，因为其中包含了太多的多余比较，例如示例中比较到第三个位置即foothe时，此时已知the已经不包含在words中，只要包含the的都一定不符合条件，希望直接能跳到第9的位置来比较foobar。  
**优化思路：**  
&emsp;&emsp;截取到子串后将其从后到前比较，如果检查到有不符合条件的单词，那么之前的单词也一定不符合条件，可以直接将前一个检查点作为截取字符串的开头：在检查到thefoo时，从后到前检比较，foo符合，the不符合，就直接从foo位置开始继续检查。  
**注意：**  
&emsp;&emsp;为了确保没有遗漏，还需要对字符串进行移位检查，移位范围为单词的长度。例如"aaa·aaa·b"和["aa","aa","ab"]，在检查完0-6子串后，还需要检查1-7子串，两次检查较小值作为下次截取字符串的开头。  
&emsp;&emsp;代码在leetcode上提交后运行速度在前5%左右。
#### 代码实现
```java
public class Solution {
	List<Integer> res=new ArrayList<Integer>();
	Map<String, Integer> mem=new HashMap<>();
	int len;
	public List<Integer> findSubstring(String s, String[] words) {
		if(words.length==0)
			return res;
		len=words[0].length()*words.length;
    	for(String i:words){
    		if(mem.containsKey(i))
    			mem.put(i, mem.get(i)+1);
    		else
    			mem.put(i, 1);
    	}
    	int pos=0;
    	while(true){
    		pos=find(s,pos,words);
    		if(pos+len>s.length()){
    			return res;
    		}
    	}
	}
  
	int find(String s,int index,String[] words){
    	Map<String, Integer> map=new HashMap<>();
		int clen=words[0].length();
		//min是函数返回值，代表下次截取字符串的开头位置
		int min=len+index;
		//移位检查
		for(int i=0;i<clen;i++){
			map.clear();
			for(int j=words.length-1;j>=0;j--){
			    //left从后往前，index表示字符串开头，i表示偏移
				int left=index+i+j*clen,right=left+clen;
				if(right>s.length())
					return min;
				String str=s.substring(left,right);
        		if(!mem.containsKey(str)){
        		    //min的值取比较较小值
        			min=Math.min(min, right);
    				break;
        		}
        		if(!map.containsKey(str))
        			map.put(str, 1);
        		else
        			map.put(str, map.get(str)+1);
        		if(mem.get(str)<map.get(str)){
        			min=Math.min(min, right);
        			break;
        		}
        		if(j==0){
        			res.add(index+i);
        			//如果成功匹配，那么后移一个单词
        			min=Math.min(index+clen, min);
        		}
			}
		}
		return min;
	}
}
```