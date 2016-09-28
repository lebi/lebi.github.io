---
title: Regular Expression Matching
date: 2016-06-08 13:30:23
tags: [leetcode]
---
## Leetcode: Regular Expression Matching
Implement regular expression matching with support for '.' and '*'.  
>'.' Matches any single character.  
'*' Matches zero or more of the preceding element.  
The matching should cover the entire input string (not partial).The function prototype should be:bool isMatch(const char *s, const char *p)  
Some examples:  
isMatch("aa","a") → false  
isMatch("aa","aa") → true  
isMatch("aaa","aa") → false  
isMatch("aa", "a*") → true  
isMatch("aa", ".*") → true  
isMatch("ab", ".*") → true  
isMatch("aab", "c*a*b") → true

## 题意
实现正则表达式中的'.'和'*'，输入两个字符串，返回是否匹配。

## 解题思路
&emsp;&emsp;使用DFS的方法，主要思路就是例如当a\*ab和aaab进行匹配时，第一个字符匹配且第二个字符为\*，那么先令ab和aaab进行匹配。因为ab和aaab匹配失败，再回溯到之前，并将s串后移一位，即匹配a\*ab和aab。使用递归，这就是一个很典型的DFS的实现。  
&emsp;&emsp;在实现时需要考虑几个特殊的测试用例：当s串为空；当p串为空。  
&emsp;&emsp;使用回溯的做法，当输入类似为a\*aab和aaaab时，需要进行多次的回溯，时间复杂度是O(n!)，最后算法在leetcode上提交约在50%。
#### 代码实现
```java
	public boolean check(char[] s,int s1,char[] p,int p1){
		int slen=s.length-s1,plen=p.length-p1;
		//s串为空，例如''和'a*c'
    	if(slen==0){
    		if(plen%2!=0)
    			return false;
    		for(int i=p1+1;i<p.length;i+=2)
    			if(p[i]!='*')
    				return false;
    		return true;
    	}
    	
    	if(plen==0)
    		return false;
    	
    	if(plen==1)
    		if(slen==1&&(s[s1]==p[p1]||p[p1]=='.'))
    			return true;
    		else
    			return false;
    	
    	if(p[p1+1]!='*'){
        	if(p[p1]=='.'||p[p1]==s[s1])
        		return check(s,s1+1, p,p1+1);
        	else return false;
    	}
    	if(p[p1]=='.'||p[p1]==s[s1])
    		if(check(s, s1,p,p1+2))
    			return true;
    		else
    			return check(s,s1+1, p,p1);
    	else
    		return check(s,s1, p,p1+2);
	}
    public boolean isMatch(String s, String p) {
    	return check(s.toCharArray(),0,p.toCharArray(),0);
    }
```