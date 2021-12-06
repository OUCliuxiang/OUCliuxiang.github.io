---
layout:     post
title:      Series Article of Leetcode Notes -- 11
subtitle:   Study Plan DS II 10-13 链表      
date:       2021-11-25
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - leetcode      
    - c++      
    - data structure      
---     

> leetcode 刷题笔记，Study Plan Data Structure 2, Day 10 -- Day 13 ： 链表 。         

## 第 10 天 链表          

### 2. 两数相加          
给你两个 非空 的链表，表示两个非负的整数。它们每位数字都是按照 逆序 的方式存储的，并且每个节点只能存储 一位 数字。      
请你将两个数相加，并以相同形式返回一个表示和的链表。       
你可以假设除了数字 0 之外，这两个数都不会以 0 开头。       

示例 1：      
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/leetcode/leetcode002.jpg"></div>       

输入：l1 = [2,4,3], l2 = [5,6,4]        
输出：[7,0,8]      
解释：342 + 465 = 807.        

示例 2：       
输入：l1 = [0], l2 = [0]        
输出：[0]          

示例 3：        
输入：l1 = [9,9,9,9,9,9,9], l2 = [9,9,9,9]         
输出：[8,9,9,9,0,0,0,1]           

提示：         
* 每个链表中的节点数在范围 [1, 100] 内         
* 0 <= Node.val <= 9           
* 题目数据保证列表表示的数字不含前导零         

#### Thought     
这题和 字符串相加 非常相似。由于链表中数字逆序，也即头节点是最低位，尾节点是最高位，更方便我们进位操作：要设置一个 int 变量 add 记录进位，初始化为 0。当 l1 存在，l2 存在，或 add 存在，三者满足任意一者，进入 while 循环。        
然后分四种情况讨论：        
1. l1, l2 都存在。计算出当前位加和 val 为 两节点值加 add。新的 add 相应为 val /10 ，val 自然也通过 %10 变为个位数字。l1, l2 各自走到自己的 next。            
2. l1 存在，l2 不存在。同上，但不再操作 l2。      
3. l1 不存在，l2 存在。同上，但不再操作 l1。        
4. else 情形，此时 l1, l2 都不存在，但 add 为 1，也即现存位的加和计算结束后，有溢出的进位。此时 node -> val 直接置为 add 即可。 **注意**，千万不要忘记在 node -> val 赋值后，重新置零 val，否则会陷入死循环。        

最后考虑给 node 是否需要走到下一位，事实上由于 l1, l2, add 都已经完成更新，此时判断 node 是否需要再加一位的条件，和 while 循环可进入的条件一致：当 l1 存在，l2 存在，或 add 存在，三者满足任意一者，node 需要往后走一位。当然要先 new 一个 next 空节点。     

#### AC Version    
```c++    
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode* head = new ListNode(0);
        ListNode* node = head;
        int add = 0;
        while(l1 || l2 || add){
            if (l1 && l2) {
                int val = l1 -> val + l2 -> val + add;
                add = val/10; val %= 10;
                node -> val = val; 
                l1 = l1 -> next; 
                l2 = l2 -> next;
            }
            else if (l1 && !l2){
                int val = l1 -> val + add;
                add = val/10; val %= 10;
                node -> val = val;
                l1 = l1 -> next; 
            }
            else if (!l1 && l2){
                int val = l2 -> val + add;
                add = val/10; val %= 10;
                node -> val = val;
                l2 = l2 -> next; 
            } 
            else {
                node -> val = add;
                add = 0;
            }

            if (l1 || l2 || add){
                node -> next = new ListNode(0);
                node = node -> next;
            }
        }
        return head;
    }
};
```

### 142. 环形链表 II       
给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。     
如果链表中有某个节点，可以通过连续跟踪 next 指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。如果 pos 是 -1，则在该链表中没有环。注意：pos 不作为参数进行传递，仅仅是为了标识链表的实际情况。     
不允许修改 链表。     

示例 1：      
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/leetcode/leetcode002.jpg"></div>        

输入：head = [3,2,0,-4], pos = 1       
输出：返回索引为 1 的链表节点       
解释：链表中有一个环，其尾部连接到第二个节点。         

示例 2：       
输入：head = [1,2], pos = 0         
输出：返回索引为 0 的链表节点       
解释：链表中有一个环，其尾部连接到第一个节点。     

示例 3：       
输入：head = [1], pos = -1       
输出：返回 null       
解释：链表中没有环。         
 
提示：        
* 链表中节点的数目范围在范围 [0, 104] 内            
* -105 <= Node.val <= 105         
* pos 的值为 -1 或者链表中的一个有效索引

#### Thought & AC Version          
只要能想到用 哈希集合 存储节点，一切都迎刃而解：      
建一个哈希表，开始节点遍历。没新访问到一个节点，先判断能否在 哈希集合 中中找到该节点。能，则立刻返回；否则，添加该节点到 哈希集合 ，走到下一个节点。     
如果顺利走完所有节点而没有返回，即意味着没有环。返回 nullptr.          

```c++
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        unordered_set<ListNode*> pool;
        while(head){
            if (pool.find(head) != pool.end())
                return head;
            pool.insert(head); 
            head = head -> next;
        }
        return nullptr;
    }
};
```           

## 第 11 天 链表            

### 160. 相交链表           
给你两个单链表的头节点 headA 和 headB ，请你找出并返回两个单链表相交的起始节点。如果两个链表不存在相交节点，返回 null 。          
图示两个链表在节点 c1 开始相交：
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/leetcode/leetcode160.png"></div>        

注意，函数返回结果后，链表必须 保持其原始结构 。        

#### Thought & AC            
使用 unordered_set 哈希集合存储一个链表的所有节点，遍历另一个节点。如果遍历过程中在集合中找到了当前节点，既是相交，返回节点。若遍历完整结束未返回，既是没有相交节点，返回 Nullptr。       

```c++     
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        unordered_set<ListNode*> nodeSet;
        while (headA) {nodeSet.insert(headA); headA = headA -> next;}
        while(headB){
            if (nodeSet.find(headB) != nodeSet.end()) return headB;
            headB = headB -> next; 
        }return nullptr;
    }
};
```       

### 82. 删除排序链表中的重复元素 II          
存在一个按升序排列的链表，给你这个链表的头节点 head ，请你删除链表中所有存在数字重复情况的节点，只保留原始链表中 没有重复出现 的数字。    
返回同样按升序排列的结果链表。       
示例 1：      
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/leetcode/leetcode082.png"></div>        

输入：head = [1,2,3,3,4,4,5]        
输出：[1,2,5]       

示例 2：     
输入：head = [1,1,1,2,3]      
输出：[2,3]      
 
提示：         
* 链表中节点数目在范围 [0, 300] 内       
* -100 <= Node.val <= 100      
* 题目数据保证链表已经按升序排列       

#### Thought      
这题，做过一次，，再遇到还是不会做。         
用递归去做，递归退出条件是当前节点 node 或下一节点已经不存在，则直接返回节点。取出当前节点的值 nodeValue，并获取下一节点 p。考虑两种情况：        
1. p 存在且值与 nodeValue 相等。则一直往后走，直到 p 为空或节点值不再等于 nodeValue，返回递归调用当前 p （第一个与当前节点值不一致的节点，或空节点）的返回值。      
2. 不相等，则 node -> next = 递归调用 node -> next，然后返回当前节点 node。             

有点绕，看代码就清楚了。        

#### AC Version         
```c++      
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if (!head || !head->next) return head;
        int headVal = head -> val;
        ListNode* p = head -> next;
        if (p && p->val == headVal){
            while (p && p -> val == headVal)
                p = p->next;
            return deleteDuplicates(p);
        }
        else {
            head -> next = deleteDuplicates(head -> next);
            return head;
        }
    }
};
```          

## 第 12 天 链表       
### 24. 两两交换链表中的节点          
给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。          
你不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。        

示例 1：         
输入：head = [1,2,3,4]         
输出：[2,1,4,3]      

示例 2：       
输入：head = []        
输出：[]         

示例 3：      
输入：head = [1]       
输出：[1]       

提示：       
* 链表中节点的数目在范围 [0, 100] 内        
* 0 <= Node.val <= 100       

#### Thought & AC       
用递归去做，没什么好说的。递归退出条件是当前节点或下一节点不存在，也即走到末端或者最后只剩一个节点茕茕孑立。         
交换当前两节点，然后后面那个（交换前前面那个）的 next 是递归调用其 next 的返回值。      

```c++       
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        if (!head || !head -> next) return head;
        ListNode* tmp = head -> next;
        head -> next = swapPairs(tmp -> next);
        tmp -> next = head;
        return tmp;
    }
};
```      

### 707. 设计链表          
设计链表的实现。您可以选择使用单链表或双链表。单链表中的节点应该具有两个属性：val 和 next。val 是当前节点的值，next 是指向下一个节点的指针/引用。如果要使用双向链表，则还需要一个属性 prev 以指示链表中的上一个节点。假设链表中的所有节点都是 0-index 的。       

在链表类中实现这些功能：       
* get(index)：获取链表中第 index 个节点的值。如果索引无效，则返回-1。     
* addAtHead(val)：在链表的第一个元素之前添加一个值为 val 的节点。插入后，新节点将成为链表的第一个节点。         
* addAtTail(val)：将值为 val 的节点追加到链表的最后一个元素。          
* addAtIndex(index,val)：在链表中的第 index 个节点之前添加值为 val  的节点。如果 index 等于链表的长度，则该节点将附加到链表的末尾。如果 index 大于链表长度，则不会插入节点。如果index小于0，则在头部插入节点。         
* deleteAtIndex(index)：如果索引 index 有效，则删除链表中的第 index 个节点。
 
示例：           
```c++
MyLinkedList linkedList = new MyLinkedList();
linkedList.addAtHead(1);
linkedList.addAtTail(3);
linkedList.addAtIndex(1,2);   //链表变为1-> 2-> 3
linkedList.get(1);            //返回2
linkedList.deleteAtIndex(1);  //现在链表是1-> 3
linkedList.get(1);            //返回3
```

提示：         
* 所有val值都在 [1, 1000] 之内。           
* 操作次数将在  [1, 1000] 之内。      
* 请不要使用内置的 LinkedList 库。         

#### Thought       
这题，是真没意思。构造链表本身没什么难度。主要是各种边界条件，要费尽心力避开。哦对了，双向链表更省事儿。          
#### My AC Version          
```c++       
class MyLinkedList {
public:
    struct Node{
        int val;
        int idx;
        Node* next;
        Node* prev;
        Node(): val(0), idx(0), next(nullptr), prev(nullptr){}
    };
    Node *head, *tail;
    bool empty;

    MyLinkedList() {
        head = new Node();
        tail = head; 
        empty = true;
    }
    
    int get(int index) {
        // printf("get\n");
        if (empty) return -1;
        Node* node = head;
        while(node){
            // printf("idx: %d, val: %d\n", node -> idx, node -> val);
            if (node -> idx == index)
                return node -> val;
            node = node -> next;
        }
        return -1;
    }
    
    void addAtHead(int val) {
        // printf("addAtHead\n");
        if (empty){
            head -> val = val;
            empty = false;
            return;
        }
        Node* node = new Node;
        node -> val = val;
        node -> idx = 0;
        node -> next = head;
        node -> prev = nullptr;
        head -> prev = node;
        Node* tmp = head;
        while(tmp){
            tmp -> idx ++;
            tmp = tmp -> next;
        }
        head = node;
    }
    
    void addAtTail(int val) {
        if (empty){
            tail -> val = val;
            empty = false;
            return;
        }
        Node* node = new Node;
        node -> val = val;
        node -> idx = tail -> idx +1;
        tail -> next = node;
        node -> prev = tail;
        node -> next = nullptr;
        tail = node;
    }
    
    void addAtIndex(int index, int val) {
        // printf("addAtIndex\n");
        if (index <= 0 ) addAtHead(val);
        else if (index == tail -> idx + 1) {
            if (empty) return;
            addAtTail(val);
        }
        else if (index > tail -> idx + 1 ) return;
        else{
            Node* node = new Node;
            node -> val = val;
            node -> idx = index;
            Node* tmp = head -> next;
            while(tmp){
                if (tmp -> idx == index){
                    node -> next = tmp;
                    node -> prev = tmp -> prev;
                    tmp -> prev -> next = node;
                    tmp -> prev = node;
                    while(tmp){
                        tmp -> idx ++;
                        tmp = tmp -> next;
                    }
                    return;
                }
                tmp = tmp -> next;
            }
        }
    }
    
    void deleteAtIndex(int index) {
        if (index < 0 || index > tail -> idx || empty) return;
        if (index == 0){
            head = head -> next;
            if (!head) return;
            head -> prev = nullptr;
            Node* node = head;
            while(node){
                node -> idx --;
                node = node -> next;
            }
            return;
        }

        if (index == tail -> idx){
            tail = tail -> prev;
            if (!tail) return;
            tail -> next = nullptr;
            return;
        }

        Node* node = head;
        while (node){
            if (node -> idx == index){
                Node* tmp = node -> next;
                node -> prev -> next = node -> next;
                node -> next -> prev = node -> prev;
                node -> next = nullptr;
                node -> prev = nullptr;
                while (tmp){
                    tmp -> idx --;
                    tmp = tmp -> next;
                }
                return;
            }
            node = node -> next;
        }
    }
};
```

## 第 13 天 链表       

### 25. K 个一组翻转链表        
给你一个链表，每 k 个节点一组进行翻转，请你返回翻转后的链表。          
k 是一个正整数，它的值小于或等于链表的长度。            
如果节点总数不是 k 的整数倍，那么请将最后剩余的节点保持原有顺序。            
 
示例 1：           
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/leetcode/leetcode025.jpg"></div>        


输入：head = [1,2,3,4,5], k = 2        
输出：[2,1,4,3,5]           

示例 2：           
输入：head = [1,2,3,4,5], k = 3          
输出：[3,2,1,4,5]         

示例 3：           
输入：head = [1,2,3,4,5], k = 1           
输出：[1,2,3,4,5]           

示例 4：         
输入：head = [1], k = 1          
输出：[1]           

提示：        
* 列表中节点的数量在范围 sz 内          
* 1 <= sz <= 5000           
* 0 <= Node.val <= 1000          
* 1 <= k <= sz          

#### Thought         
这个题是翻转链表的变体。         
对于反转链表而言，极为关键的一点是虚拟头结点的使用：维护一个虚拟节点 pre，它的位置在 head 之前，初始化为 nullptr，于是对于原有的 head 不需要另外考虑，只需要也使他正常的指向其前驱节点 pre，最后的结果就变成了一个指向 nullptr 的尾节点。        
而翻转链表的过程，就是 `pre`, `head`, `nxt = head -> next` 这三个节点的不断变换：     
* nxt = head -> next;         
* head -> next = pre;         
* pre = head;        
* head = nxt;        

直到 head == nullptr，链表完全翻转，此时 pre 处于原始链表的尾节点，也即完成翻转后的链表的头结点。          

对于该题来说，只是整体翻转变成了区域翻转，其本质是没有变换的。只是又多了几条注意点：        
1. 需要维护一个反转函数，参数是当前需要翻转区域的 head 和 tail ，返回值仍需要是顺序的：tail, head。这样方便找到新的 tail (原始 head) 的下一节点作为下一区域的 head。         
2. 由于是区域翻转，决不能时 head 翻转后直接指向 nullptr，这样完成当前区域翻转后，链表就断了。希望的结果是原始的 head 继承原始 tail 的后继：初始化pre = tail -> next。       
3. 翻转过程的 while 循环，**绝对不要** 以 cur（初始为 head）== tail -> next 作为判断标志！这是由于在 cur = tail 这一次循环过程中，cur 会走到原始的 tail -> next 节点（nxt = cur -> next），但！！ cur -> next 他会变为 pre，也就是 cur 的前驱。如此，while 循环永无终止。但，由于 tail 对我们是已知的，完全可以使用，也最好使用 pre = tail 作为 while 循环的判断标准。          
4. 外层循环里，维护一个 pre 作为整个链表的虚拟头结点，一旦满足条件，无脑返回 pre -> next 准没错，而不用考虑 pre -> next 到底是何方神圣。         
5. 始终维护一个 cur 作为当前需要翻转的区域的前驱节点，区域翻转的 head 参数既是 cur -> next。于是区域翻转完成后，只需要让 cur -> next 指向新的 head（原来的 tail ）；并，cur 赋值为新的 tail（原来的 head），即可以保证整个链表一个接一个区域依次翻转。cur 初始化显然为外层 pre。        
6. tail 的获取，和剩余节点数量的判断显然应当同步进行。外层循环每次维护一个 tail 节点，从 cur 开始向后走 k 步。这样一来，如果正常走完，也就走到了目标 tail。如果走不完，则中间过程必有 tail == nullptr，于是可直接返回 pre -> next。             

#### AC Version           
```c++        
class Solution {
public:
    ListNode* reverseKGroup(ListNode* head, int k) {
        ListNode* pre = new ListNode(0);
        pre -> next = head;
        ListNode* cur = pre;
        while(cur){
            ListNode* tail = cur;
            for (int i = 0; i < k; ++i){
                tail = tail -> next;
                if (!tail) return pre -> next;
            }
            pair<ListNode*, ListNode*> nodes = reverseKGroup(cur -> next, tail);
            cur -> next = nodes.first;
            cur = nodes.second;
        }
        return pre -> next;
    }
    pair<ListNode*, ListNode*> reverseKGroup(ListNode* head, ListNode* tail){
        ListNode* pre = tail -> next;
        ListNode* cur = head;
        while(pre != tail){
            ListNode* node = cur -> next;
            cur -> next = pre;
            pre = cur;
            cur = node;
        }
        return make_pair(tail, head);
    }
};
```       

### 143. 重排链表           
给定一个单链表 L 的头节点 head ，单链表 L 表示为：         
```
L0 → L1 → … → Ln - 1 → Ln        
```          
请将其重新排列后变为：         
```
L0 → Ln → L1 → Ln - 1 → L2 → Ln - 2 → …
```          
不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。

示例 1：
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/leetcode/leetcode143.png"></div>       

输入：head = [1,2,3,4]           
输出：[1,4,2,3]           

示例 2：         
输入：head = [1,2,3,4,5]          
输出：[1,5,2,4,3]        
 
提示：         
* 链表的长度范围为 [1, 5 * 104]           
* 1 <= node.val <= 1000            

#### Thought        
这道题可以分解为三道完全独立的简单题：         
1. 链表切割。用快慢指针解。             
2. 链表翻转。就是上一题的原型。           
3. 链表融合。迭代求解。        

#### AC Version          
```c++       
class Solution {
public:
    void reorderList(ListNode* head) {
        if (!head || !head->next) return;
        ListNode* mid = findMiddle(head);
        ListNode* reverseMid = reverse(mid -> next);
        mid -> next = nullptr;
        ListNode* node = head;
        merge(node, reverseMid);
    }
private:
    ListNode* findMiddle(ListNode* head){
        ListNode* slow = head;
        ListNode* fast = head;
        while(fast -> next && fast -> next -> next){
            slow = slow -> next;
            fast = fast -> next -> next;
        }
        return slow;
    }
    ListNode* reverse(ListNode* mid){
        ListNode* pre = nullptr;
        ListNode* cur = mid;
        while(cur){
            ListNode* nxt = cur -> next;
            cur -> next = pre;
            pre = cur; 
            cur = nxt;
        }
        return pre;
    }
    void merge(ListNode* node1, ListNode* node2){
        while(node1 && node2){
            ListNode* tmp1 = node1 -> next;
            ListNode* tmp2 = node2 -> next;

            node1 -> next = node2;
            node2 -> next = tmp1;

            node1 = tmp1;
            node2 = tmp2;
        }
    }
};
```         