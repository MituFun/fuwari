---
title: Leetcode 3sum的一种较劣的解法
published: 2026-09-06
description: '如题'
image: ''
tags: ['杂谈','算法']
category: '竞赛'
draft: false 
lang: ''
---

[https://leetcode.cn/problems/3sum](题目链接)

几万年没碰 OI 了，闲的没事在学校写力扣的题玩玩，看到这道题压根想不起来双指针的事，然后口胡出了一个 $O(n^2\log n)$ 的做法，过了。

具体地，考虑原式变形：$\text{nums}_i+\text{nums}_j+\text{nums}_k=0$ 等价于 $\text{nums}_i+\text{nums}_j=-\text{nums}_k$。此外，对 $i,j,k$ 的具体下标无稳定性要求，可以对 $\text{nums}$ 进行排序，容易想到对于 $i$，$j$ 进行 $O(n^2)$ 的暴力枚举，对 $k$ 进行二分查找。

可以发现，这种算法复杂度为 $O(n^2\log n)$，对于极弱的 $n=3000$ 的数据完全可过。

关于一些小细节：

- 如何不重复 $i,j,k$？

  我为了不被 3000 个 0 卡掉，就将 $\text{nums}$ 压缩了，每个节点为 $\text{val}$——值，$\text{l}$——左端点，$\text{r}$——右端点，$\text{len}$——区间长度。准确地，$\text{len}=\text{r}-\text{l}+1$。

  好，那么如何不重复呢？很简单，你选用的点剩余长度够就行了。

- 去重吗？

  本做法显而易见地无需去重，因为不存在可以被保存的合法答案是完全一样的。



