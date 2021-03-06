---
title: "二叉树 binary tree"
---

## 二叉树 binary tree

{{< expand "反转二叉树" "...">}}

```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func invertTree(root *TreeNode) *TreeNode {
    if root == nil {
        return nil
    }
    root.Right, root.Left = invertTree(root.Left), invertTree(root.Right)
    return root
}
```

{{< /expand>}}

{{< expand "二叉树的最大深度" "...">}}

```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
// 递归方式
func maxDepth(root *TreeNode) int {
    if root == nil {
        return 0
    }
    if root.Right == nil && root.Left == nil {
      return 1
    }
    leftDepth := maxDepth(root.Left)
    rightDepth := maxDepth(root.Right)
    if leftDepth > rightDepth {
        return 1+leftDepth
    }
    return 1+rightDepth
}

// BFS 迭代方式
func maxDepth(root *TreeNode) int {
    if root == nil {
        return 0
    }
    queue := []*TreeNode{root,}
    depth := 0
    for len(queue) > 0{
        size := len(queue)
        for i:=0;i<size;i++{
            node := queue[0]
            queue = queue[1:]
            if node.Left != nil {
                queue = append(queue, node.Left)
            }
            if node.Right != nil {
                queue = append(queue, node.Right)
            }
        }
        depth = depth+1
    }
    return depth
}
```

{{< /expand>}}

{{< expand "合并二叉树" "...">}}

```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func mergeTrees(t1 *TreeNode, t2 *TreeNode) *TreeNode {
    if t1 == nil {
        return t2
    }
    if t2 == nil {
        return t1
    }
    var nodeLeft *TreeNode
    var nodeRight *TreeNode
    if t1.Left == nil && t1.Right == nil {
        nodeLeft = t2.Left
        nodeRight = t2.Right
    }
    if t2.Left == nil && t2.Right == nil {
        nodeLeft = t1.Left
        nodeRight = t1.Right
    }
    if nodeLeft == nil {
        nodeLeft = mergeTrees(t1.Left, t2.Left)
    }
    if nodeRight == nil {
        nodeRight = mergeTrees(t1.Right, t2.Right)
    }
    node := TreeNode{
        Val: t1.Val+t2.Val,
        Left: nodeLeft,
        Right: nodeRight,
    }
    return &node
}
```

{{< /expand>}}

{{< expand "二叉树层次遍历2" "...">}}

```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func levelOrderBottom(root *TreeNode) [][]int {
    if root == nil {
        return nil
    }
    queue := []*TreeNode{root}
    result := [][]int{}
    for len(queue) > 0 {
        levelVal := []int{}
        size := len(queue)
        for i:=0;i<size;i++ {
            levelVal = append(levelVal, queue[i].Val)
            if queue[i].Left != nil {
                queue = append(queue, queue[i].Left)
            }
            if queue[i].Right != nil {
                queue = append(queue, queue[i].Right)
            }
        }
        queue = queue[size:]
        result = append([][]int{levelVal}, result...)
    }
    return result
}
```

{{< /expand>}}

{{< expand "将有序数组转化为高度平衡二叉搜索树" "...">}}

```go
func sortedArrayToBST(nums []int) *TreeNode {
    if len(nums) <=0 {
        return nil
    }
    if len(nums) == 1 {
        return &TreeNode{Val:nums[0]}
    }
    mid := len(nums)/2
    return &TreeNode{
        Val: nums[mid],
        Left: sortedArrayToBST(nums[:mid]),
        Right: sortedArrayToBST(nums[mid+1:]),
    }
}
```

{{< /expand>}}

{{< expand "对称二叉树" "...">}}

```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
// 递归遍历，传入双树，递归对比左树和右
func isSymmetric(root *TreeNode) bool {
    return checker(root, root)
}
func checker(left, right *TreeNode) bool {
    if left == nil && right == nil {
        return true
    }
    if left == nil || right == nil {
        return false
    }
    return left.Val == right.Val &&
        checker(left.Left, right.Right) &&
        checker(left.Right, right.Left)
}
// 迭代遍历，用队列缓存取一双一双自左树和右树的镜像节点。然后从队列中取出一双来进行节点值对比。相等就把两个对比完的节点的镜像值一双一双存进队列。
func isSymmetric(root *TreeNode) bool {
    queue := []*TreeNode{
        root,
        root,
    }
    for len(queue) > 0 {
        left, right := queue[0], queue[1]
        queue = queue[2:]
        if left == nil && right == nil {
            continue
        }
        if left == nil || right == nil {
            return false
        }
        if left.Val != right.Val {
            return false
        }
        queue = append(queue, left.Left)
        queue = append(queue, right.Right)
        queue = append(queue, left.Right)
        queue = append(queue, right.Left)
    }
    return true
}
```

{{< /expand>}}

{{< expand "二叉树深度" "...">}}

```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func maxDepth(root *TreeNode) int {
    if root == nil {
        return 0
    }
    if root.Left == nil && root.Right == nil {
        return 1
    }
    leftDepth := maxDepth(root.Left)
    rightDepth := maxDepth(root.Right)
    if leftDepth > rightDepth {
        return 1 + leftDepth
    }
    return 1 + rightDepth
}
```

{{< /expand>}}

{{< expand "二叉树中序遍历" "...">}}

```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
// 递归方式
func inorderTraversal(root *TreeNode) []int {
    if root == nil {
        return []int{}
    }
    if root.Left == nil && root.Right == nil {
        return []int{root.Val}
    }
    leftList := inorderTraversal(root.Left)
    rightList := inorderTraversal(root.Right)
    result := append(leftList, root.Val)
    result = append(result, rightList...)
    return result
}
// 迭代方式
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func inorderTraversal(root *TreeNode) []int {
    if root == nil {
        return []int{}
    }
    if root.Left == nil && root.Right == nil {
        return []int{root.Val}
    }
    result := []int{}
    stack := []*TreeNode{root}
    for len(stack) > 0 {
        node := stack[len(stack)-1]
        if node.Left != nil {
            stack = append(stack, node.Left)
            node.Left = nil
            continue
        }
        stack = stack[:len(stack)-1]
        result = append(result, node.Val)
        if node.Right != nil {
            stack = append(stack, node.Right)
            node.Right = nil
        }
    }
    return result
}
```

{{< /expand>}}

