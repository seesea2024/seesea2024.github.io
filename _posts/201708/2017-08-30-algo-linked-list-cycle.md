---
published: true
---
## 链表是否有环以及环的第一个入口点

### 链表是否有环

* 快慢指针，最终会相遇到环上的某个节点上。否则快指针直接到空节点。

### 有环链表第一个入口点

* 分别从链表头和上面环上相遇的节点开始步长为1的遍历，下一次相遇的节点为环的第一个入口点。[理论分析](http://www.cnblogs.com/BeyondAnyTime/archive/2012/07/06/2580026.html)
![图](https://github.com/shidongwa/seesea2024.github.io/blob/master/images/linked-list-cycle.png?raw=true)

```html
当fast与slow相遇时，slow肯定没有走遍历完链表，而fast已经在环内循环了n圈(1 <= n)。假设slow走了s步，则fast走了2s步（fast步数还等于s 加上在环上多转的n圈），设环长为r，则：
2s = s + nr
s= nr
设整个链表长L，入口环与相遇点距离为x，起点到环入口点的距离为a。
a + x = nr
a + x = (n – 1)r +r = (n-1)r + L - a

(L – a – x)为相遇点到环入口点的距离，由此可知，从链表头到环入口点a等于(n-1)循环内环+相遇点到环入口点
a = (n-1)r + (L – a – x) 

```

## 两个链表第一个相交节点

首先判断两个链表是否有环，根据上面的算法。

### case1: 两个链表都无环

* 方法1: 链表1的节点地址hash，遍历链表2是否有相同地址的节点
* 方法2: 
** 先判断是否相交（最后一个节点是否相同）
** 求两个链表的长度
** 长的链表先走diff步，两个链表再同时前进（步长为1），第一个相同的节点是交点
* 方法3: 两个链表连成一个环，问题转化为求环的第一个入口点
维护两个指针pA和pB，初始分别指向A和B。然后让它们分别遍历整个链表，每步一个节点。当pA到达链表末尾时，让它指向B的头节点；类似的当pB到达链表末尾时，重新指向A的头节点。如果pA在某一点与pB相遇，则pA或pB就是交点
```python
    # Definition for singly-linked list.
    #class ListNode:
    #     def __init__(self, x):
    #         self.val = x
    #         self.next = None
class Solution:
    # @param two ListNodes
    # @return the intersected ListNode
    def getIntersectionNode(self, headA, headB):
        pA = headA
        pB = headB
        if headA == None or headB == None:
            return
        while pA and pB:
            if pA.val != pB.val:
                if pA.next and pB.next:
                    pA = pA.next
                    pB = pB.next
                elif pA.next == None and pB.next != None:
                    pA = headB
                    pB = pB.next
                elif pB.next == None and pA.next != None:
                    pA = pA.next
                    pB = headA
                else:
                    return
            else:
                return pA
```

### case2: 一个链表有环，另外一个没有
不可能相交

### case3: 两个链表共享一个环
* 先根据是否有环算法找到两个链表的环上位置pos1，pos2
* 分别求两个链表到pos1，pos2的长度
* diff长度，长的链表先走diff长度，再同时前进找到第一个相同节点
