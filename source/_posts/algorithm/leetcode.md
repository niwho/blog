title: leetcode 
date: 2015-8-2 10:49:27
updated: 2015-08-02 15:39:51
tags: 
- 算法
- leetcode
categories:
- 算法 
---

>leetcode 刷刷刷 
>不贴代码，记录我的思路（解决）

<!--more-->

>以下5题一次解决（8.2 11：00~15：00）

###67  Add Binary 
>难度：简单
>Given two binary strings, return their sum (also a binary string).
>
>For example,
a = "11"
b = "1"
Return "100".

2个字符串表示的二进制数据(例如 "10101001")加法，关键处是进位的处理：1，对应位相加(空缺的位用0表示)再加上进位的值

###7 Reverse Integer
>难度：简单
>Reverse digits of an integer.
>
Example1: x = 123, return 321
Example2: x = -123, return -321

10进制整型数字反序化，一种解决的思路(accepted):负数取正简化后面溢出的判断，获取位数n，除余获取每一位的数字，如第i位(从0开始)，累加10^n-i-1，关键要注意加法和乘法的溢出

###9 Palindrome Number
>难度：简单
>Determine whether an integer is a palindrome. Do this without extra space.
>Some hints:
Could negative integers be palindromes? (ie, -1)
>
>If you are thinking of converting the integer to string, note the restriction of using extra space.
>
>You could also try reversing an integer. However, if you have solved the problem "Reverse Integer", you know that the reversed integer might overflow. How would you handle such case?
>
>There is a more generic way of solving this problem.

回文数的判断,取首位对应的数字比较,除法及余数的处理

###242 Valid Anagram
>难度：简单
>Given two strings s and t, write a function to determine if t is an anagram of s.
>
For example,
s = "anagram", t = "nagaram", return true.
s = "rat", t = "car", return false.
>
Note:
You may assume the string contains only lowercase alphabets.

比较2个字符串是否包含相同的字符。想起以前一个海量数字排序的问题：用固有的属性来表示某种关系。用2组26大小的char数组表示26个字符，并初始化为0，序号0 对应a，依次类推，遍历字符串统计各字符统计的次数，最后再比这个2组统计的结果，一旦某个位置的数字不等，则返回假，这样\\(O(n)\\)的时间复杂度。

###235 Lowest Common Ancestor of a Binary Search Tree 
>难度：简单
>Given a binary search tree (BST), find the lowest common ancestor (LCA) of two given nodes in the BST.
>
According to the definition of LCA on Wikipedia: “The lowest common ancestor is defined between two nodes v and w as the lowest node in T that has both v and w as descendants (where we allow a node to be a descendant of itself).”
        _______6______
       /              \
    ___2__          ___8__
   /      \        /      \
   0      _4       7       9
         /  \
         3   5
For example, the lowest common ancestor (LCA) of nodes 2 and 8 is 6. Another example is LCA of nodes 2 and 4 is 2, since a node can be a descendant of itself according to the LCA definition.

查找二叉搜索树的最近共同父节点，以前同学面试遇到过，关键点：首先确定从根节点到指定的子节点的路径，使用基本的后序遍历（递归返回时，pushfront元素到list)，这个list就是从根节点到某子节点的路径。这样得到2个list，遍历比较这2list，记录（覆盖）当前相等的元素，遇到第一处元素（指针）不相等的就break。最后记录的那个元素就是最近的共同父节点。\\(O(n*log_2n)\\)
