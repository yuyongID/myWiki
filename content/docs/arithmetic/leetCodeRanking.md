---
最长公共前缀对称二叉树bookFlatSection: true
title: "高频题目"
---
# LeetCode 的高频题目

**DFS**：深度优先搜索算法（Depth First Search），对于二叉树而言，它沿着树的深度遍历树的节点，尽可能深的搜索树的分支，这一过程一直进行到已发现从源节点可达的所有节点为止。

**平衡二叉树：** 左右子树的高度相差不超过 1 的树为平衡二叉树

| 插入方式 | 描述 | 旋转方式 |
|:--------|:------------------------------------------------------- | :------------ |
| LL | 在 A 的左子树根节点的左子树上插入节点而破坏平衡 | 右旋转 |
| RR | 在 A 的右子树根节点的右子树上插入节点而破坏平衡 | 左旋转 |
| LR | 在A的左子树根节点的右子树上插入节点而破坏平衡 | 先左旋后右旋 |
| RL | 在 A 的右子树根节点的左子树上插入节点而破坏平衡 | 先右旋后左旋 |

### XOR 异或运算 ⊕
- 任何数和 0 做异或运算，结果仍然是原来的数，即 a⊕0=a。
- 任何数和其自身做异或运算，结果是 0，即 a⊕a=0。
- 异或运算满足交换律和结合律，即 a⊕b⊕a=b⊕a⊕a=b⊕(a⊕a)=b⊕0=b。

### 汉明距离
两个整数之间的汉明距离是对应位置上数字不同的位数。XOR 的位运算，当且仅当输入位不同时输出为 1。
> 计算 x 和 y 之间的汉明距离，可以先计算 x XOR y，然后统计结果中等于 1 的位数。
>
> > 为了计算等于 1 的位数，可以将每个位移动到最左侧或最右侧，然后检查该位是否为 1。检查最右位是否为 1，可以使用取模运算（i % 2）或者 AND 操作（i & 1），这两个操作都会屏蔽最右位以外的其他位。

### 布赖恩·克尼根算法
该算法使用特定比特位和算术运算移除等于 1 的最右比特位。
> 当我们在 number 和 number-1 上做 AND 位运算时，原数字 number 的最右边等于 1 的比特会被移除。

### 二进制字符串的游标取值操作
当字符串中 a[i] 为 "0" 时，之际操作游标取出的是 uint8=48 类型是 byte，直接 int 转换就会得到一个 int=48；当操作[-''0']之后，相当是 utf8 编码的两个 48 相减，获得的是 uint8=0，int 转换之后就会得到一个 int 类型的 0。如果该位为 "1"，由于正好比"0"多1个编码位，所以得到的就是 uint8=1，int 转换即可得到 1。

### 回溯算法编码公式

```go
result = []
func backtrack(路径，选择列表) {
	if 满足结束条件 {
		result.add(路径)
	}
	return

	for 选择 in 选择列表 {
		做选择
		backtrack(路径，选择列表)
		撤销选择
	}
}
```


#### 位运算 bit operation
- [ ] IP 到 CIDR
- [x] 只出现一次的数字
- [x] 二进制手表

#### 字符串 string
- [x] 罗马数字转整数
- [x] 有效的括号
- [x] 最长公共前缀（实现方法有待优化）
- [x] 二进制求和
- [x] 字符串相加 
- [x] 验证回文串
- [x] 无重复字符的最长子串
- [x] 括号生成
- [x] 最长回文串
- [x] Z 字型变换
- [x] 电话号码的字母组合
- [x] 分割回文串

#### 数学 mathematics
- [x] 整数反转

#### 数组 arrary
- [x] 两数之和
- [x] 合并两个有序数组
- [x] 两数之和 II - 输入有序数组
- [x] 搜索旋转排序数组
- [x] 二维数组中的查找
- [x] 快速排序
- [x] 三数之和
- [x] 岛屿的最大面积
- [x] 最长连续递增序列
- [x] 数组中的第 k 个最大元素
- [x] 全排列
- [x] 乘最多水的容器
- [x] 最大子序和
- [x] 最大乘积子数组

#### 链表 linked list
- [x] 合并两个有序链表
- [x] 反转链表
- [x] 删除链表中的节点

#### 二叉树 binary tree
- [x] 反转二叉树
- [x] 二叉树的最大深度
- [x] 合并二叉树
- [x] 二叉树层次遍历2
- [x] 将有序数组转化为高度平衡二叉搜索树
- [x] 对称二叉树
- [x] 二叉树深度
- [x] 二叉树中序遍历

#### 哈希表 hash map
- [x] 宝石与石头
- [x] 岛屿的周长
- [x] 两个数组的交集

#### 二分查找 binary search
- [x] 二分查找