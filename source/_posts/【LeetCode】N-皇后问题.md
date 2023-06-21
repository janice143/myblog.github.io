---
title: 【LeetCode】N 皇后问题
date: 2022-03-18 19:10:47
tags: JavaScript
categories: LeetCode
---

## 问题描述

N 皇后问题是指在 n \* n 的棋盘上要摆 n 个皇后。

要求：任何两个皇后不同行，不同列也不在同一条斜线上，求给一个整数 n ，返回 n 皇后的摆法数。

#### 例如

```markdown
输入：8
返回值：92

输入：4
返回值：2
```

## 题解思路

看懂此题解之前，需要会做 [【BM55 没有重复项数字的全排列】](https://www.nowcoder.com/practice/4bcf3081067a4d028f95acee3ddcd2b1?tpId=295&tqId=701&ru=/exam/oj&qru=/ta/format-top101/question-ranking&sourceUrl=%2Fexam%2Foj)。

如果 BM55 已经会做了，下面开始看本题解。

#### 👇👇👇

#### 大概思路

1. n 个皇后分别在 1，2,3...n 行的不同列处，列的下标用数组`arr`表示
2. 要确定皇后的位置，其实就是确定列的位置，因为行已经固定了
3. 进一步讲，也就是如何摆放 数组`arr`[0,1,2,3,...,n-1]
4. 如果没有【不在同一条斜线上】要求，这题其实只是单纯的全排列问题，代码很容易些出来
5. 但是对于【不在同一条斜线上】要求，全排列得到的`res`结果有些并不能用，比如说皇后的列位置不能这样排列：[0,1,2,3...,n-1]
6. 所以现在问题变成了，在全排列的基础上，根据 N 皇后的问题，去除一些结果
7. 下面开始声明一些变量

   - `arr` n 个皇后的列位置
   - `res` n 皇后排列结果
   - `ruler` 记录对应的列位置是否已经占用（也是是否有皇后），如果有，那么设为 1，没有设为 0
   - `setPos` 哈希集合，标记正斜线（从左上到右下）位置，如果在相同正斜线上，坐标(x,y)满足 y-x 都相同
   - `setCon` 哈希集合，标记反正斜线（从 y 右上到左下）位置，如果在相同反斜线上，坐标(x,y)满足 x+y 都相同

     8. 大致的代码思路如下所示

```markdown
arr = [0,1,2,3,...,n-1]
res = []
ruler = [n]
setPos = new Set()
setPos = new Set()

backtrack(路径, arr):
if 满足结束条件:
result.add(路径)
return
for 选择 in 选择列表(arr):
做选择
backtrack(路径, arr)
撤销选择
return res.length
```

#### 一些解释

首先，N 个皇后肯定得在不同行，不同列处（由题意 “任何两个皇后不同行，不同列” 可知）。

要实现这个方案，其实思路和 `BM55 全排列`一样。把 N 个皇后的列坐标定义成数组`arr = [0,1,2,...,n-1]`。然后全排列该数组即可，得到的排列方式`res`的长度，就是排列方案的总数。

但是 N 皇后问题，还需要满足 N 皇后不在同一条斜线上。这就更复杂了一点，需要对斜线位置进行判断。

怎么判断呢？

如果`setPos`里不包含正斜线位置，`setCon`里不包含反斜线位置，那么就是我们要的【满足结束条件】。

在回溯函数里，我们先确定第 0 排皇后的列位置，然后回溯递归，确定第 1 排的皇后的列位置。

所以每次回溯，坐标`(x,y)`其实是`(row,i)`

#### 最终的代码

```javascript
const arr = Array.from({ length: n }, (item, index) => index); // 列的位置
let res = [];
let ruler = new Array(n).fill(0); //用来记录num的皇后下落后，对角线位置，如果在对角线位置，那么为1，否则0
let setPos = new Set(); //标记正对角线
let setCon = new Set(); // 标记反对角线

const backTrace = (row, path) => {
  if (path.length === n) {
    res.push(path.slice());
    return;
  }
  for (let i = 0; i < n; i++) {
    // i表示列
    if (ruler[i] == 0 && !setPos.has(i - row) && !setCon.has(i + row)) {
      path.push(arr[i]);
      ruler[i] = 1;
      setPos.add(i - row);
      setCon.add(i + row);
      backTrace(row + 1, path);
      path.pop();
      ruler[i] = 0;
      setPos.delete(i - row);
      setCon.delete(i + row);
    }
  }
};
backTrace(0, []);
return res.length;
```
