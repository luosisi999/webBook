**1. 判断字符串有无重复值，**
```
def judge(str):
    for i in range(len(str)):
        if str[i+1:].find(str[i])!=-1:
            return True
    return False
str ="aba"
# print(judge(str))
```
**2. 最长回文字段**

```
# 输入一串数字，找出其中最长的对称回文字段，并输出
# 输入：“34312332112332114”
# 输出一：最长回文字段 123321123321
class Solution():
    def longestPalindrome1(self,s):
        #边界条件，主要是要判断字符串不能为空，否则下标会溢出
        if s == s[::-1]: return s #
        max_length = 1 #初始化，要更新的字串长度
        res = s[0] #初始字串
        for i in range(0,len(s)-1):#遍历数组，注意下标
            for j in range(i+1,len(s)): #遍历，注意下标
                #判断是否存在新子串更长，如果存在，则更新最长字串长和最长字串
                #使用了python的切片方式来判断字串是否相同，导致时间复杂度O(n^2)
                #如果不用切片，则是否为回文仍需要一次遍历来判断，复杂度为O(n^3)
                if j-i+1 > max_length and s[i:j+1]==s[i:j+1][::-1]:
                    max_length = j-i+1 #更新最长字串长
                    res = s[i:j+1] #更新最长字串
        return res

    def longestPalindrome(self,s):
        n = len(s)
        line = [False]*n #维护一列数组，不断更新，道理相同。
        ans=""
        for j in range(0,n):
            for i in range(0,j+1):
                if j == i: line[i] = True
                elif j - i == 1:
                    line[i] = s[i]==s[j]
                else:
                    line[i] = line[i+1] and s[i]==s[j]
                if line[i] and j-i+1>len(ans):
                    ans = s[i:j+1]
        return ans
p =Solution()
str ='34312332112332114'
x = p.longestPalindrome(str)
# print(x)
```

**3. 股票买卖问题**
```
# 给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。
price= [7,1,5,3,6,4]
class Solution2:
    def maxProfit(self,prices):
        minPrice = float("inf")
        maxProfit = 0
        if len(prices) == 0:
            return 0
        for num in prices:
            if num < minPrice:
                minPrice = num
            if num - minPrice > maxProfit:
                maxProfit = num - minPrice
        return maxProfit
      
m=Solution2()
max= m.maxProfit(price)
print(max)
```

**5. 判断是否为平衡二叉树**
```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None
 
class Solution:
    def isBalanced(self, root):
        def treeDepth(root):
            if not root:
                return 0
            return max(treeDepth(root.left),treeDepth(root.right))+1
        if not root:
            return True
        leftTreeDepth=treeDepth(root.left)
        rightTreeDepth=treeDepth(root.right)
        if abs(leftTreeDepth-rightTreeDepth)>1:
            return False
        return self.isBalanced(root.left) and self.isBalanced(root.right)
```

**6. 字母排序**
```
row = int(input())
words = []  #创建空列表
for i in range(row):  #使用range进行循环次数
    words.append(input()) #添加输入的字符串
words.sort() #由于python中的sort()函数排序后就是以字典排序的形式进行排序，则使用sort()函数即可
for word in words:
    print(word)
```

**7. 合并两个有序列表**

```
# 方法1： 递归


class Solution:
    def mergeTwoLists(self, l1, l2):
        if not l1:
            return l2

        if not l2:
            return l1

        if l1.val < l2.val:
            l1.next = self.mergeTwoLists(l1.next, l2)
            return l1
        else:
            l2.next = self.mergeTwoLists(l2.next, l1)
            return l2
# class Solution:
#     def mergeTwoLists(self, l1: ListNode, l2: ListNode) -> ListNode:
#         FNode = ListNode(-1)

#         pre = FNode

#         while l1 and l2:
#             if l1.val < l2.val:
#                 pre.next = l1
#                 l1 = l1.next
#             else:
#                 pre.next = l2
#                 l2 = l2.next
#             pre = pre.next

#         pre.next = l1 if l1 else l2

#         return FNode.next
```


**8. 手撕扑克**
```
# 从扑克牌随机抽5张牌，判断是不是一个顺子，即这5张牌是不是连续的。小王、大王可以看成是任意数字
# 思路：可以先将大小王看作是0，先将数组排序，在统计数组中0的个数，最后统计数组排序后相邻数字之间的空缺总数。如果空缺总数小于等于0的个数，那么这个数组是连续的，否则不连续
def IsContinuous(numbers):
    if not numbers:
        return False
    numbers.sort()
    count_0 = numbers.count(0)
    spacing = 0
    small = count_0
    big = small + 1
    while big < len(numbers):
        if numbers[small] == numbers[big]:
            return False
        # 如果前后空缺是1则不需要填补
        spacing += numbers[big] - numbers[small] - 1
        small = big
        big += 1
        return spacing <= count_0
print(IsContinuous([1, 7, 8, 4, 5]))
```

**9.二进制求1的个数**
```
def NumberOf1(n):
        # write code here
        count = 0
        while n & 0xffffffff:
            count += 1
            n = (n - 1) & n
        print()
print(NumberOf1(11))d
```  
**10. 斐波那契 1,1,2,3,5,7**
```

def fun(n):
    if n==1:
        return 1
    elif n==2:
        return 1
    elif n>2:
        a=1
        b=1
        c=0
        for i in range(3,n+1):
           c=a+b
           a=b
           b=c
        print(c)   
fun(4)
```
### **二叉树的最小深度**
最近的叶子节点的深度
```

 /**
* @param {TreeNode} root
* @return {number}
*/
var minDepth=function(root){
    if(!root){return 0;}
    const q = [[root,1]];//创建一个队列
    while(q.length){
        const [n,1] = q.shift();
        if(!n.left&&!n.right) return l;
        if(n.left){
            q.push([n.left,l+1]);
        }
        if(n.right){
            q.push([n.right,l+1]);
        }
    }
}

```