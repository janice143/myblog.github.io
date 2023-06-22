---
title: 【leetcode124】Binary Tree Maximum Path Sum
date: 2023-05-23 23:02:27
tags:
academia: true
---

## Problem Description

Given a binary tree, return the maximun sum of the path. A path is a sequence of adjacent nodes which are connected by an edge. The node in a path can only show once. Particulary, a single node is a special path, and the sum of this path is the value of the node.

you can see the examples in the source link of this leetcode problem.

[https://leetcode.com/problems/binary-tree-maximum-path-sum/description/](https://leetcode.com/problems/binary-tree-maximum-path-sum/description/)

## Discussion

Any node can be a path, thus it has a sum value. If a node can not split to left or right, the maximum sum of the path starting from this node is the value of itself.

If a node can split to the next node, and the child node can no longer split, the maximum sum of path is max(left.val,0) + max(0,right.val) + root.val.

If a node can split to the next node, and the child node can also split, what is the maximun sum of path?

It’s definately not *max sum of the left node + max sum of right node*, because in some case there will a illigel path where a single node show more than once. Each node only allow to split to one direction. In order to get the maximun sum, we take the larger direction, that is max(pathSum(root.left)+root.val, pathSum(root.right)+root.val, this can determin which way to go to get the maximum value.

and the maximum sum of path is max(pathSum(root.left),0) + max(0,pathSum(root.right)) + root.val.

Note that the maximum result need to update every time you get a maximum number of a node.

## Solution

```jsx
/**
 * Definition for a binary tree node.
 * function TreeNode(val, left, right) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.left = (left===undefined ? null : left)
 *     this.right = (right===undefined ? null : right)
 * }
 */
/**
 * @param {TreeNode} root
 * @return {number}
 */
var maxPathSum = function(root) {
    const maxValue = [-Infinity];
    getMaxSum(root,maxValue)
    return maxValue[0]
};

const getMaxSum = (root, maxValue)=>{
    if(!root) return 0
    const left = Math.max(0,getMaxSum(root.left, maxValue))
    const right = Math.max(0,getMaxSum(root.right,maxValue))
    maxValue[0] = Math.max(maxValue[0],left+right+root.val)
    console.log(maxValue[0],left,right)
    return Math.max(root.val+left, root.val+right)
}
```
