---
layout:     post
title:      Series Article of Leetcode Notes -- 02
subtitle:   Study Plan Algo 1 Day 8-14       
date:       2021-09-26
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - leetcode      
    - c++     
    - algorithm            
---     

> leetcode 刷题笔记，Study Plan Algorithm 1, Day 8 -- Day 14  。     

## Day 8 BFS / DFS 广度优先/深度优先搜索             
[GitHub 连接](https://github.com/OUCliuxiang/leetcode/blob/master/StudyPlan/Algo1/day8)           

### 617 Merge Two Binary Trees           
You are given two binary trees root1 and root2.     
Imagine that when you put one of them to cover the other, some nodes of the two trees are overlapped while the others are not. You need to merge the two trees into a new binary tree. The merge rule is that if two nodes overlap, then sum node values up as the new value of the merged node. Otherwise, the NOT null node will be used as the node of the new tree.            
Return the merged tree.             
Note: The merging process must start from the root nodes of both trees.           

Example 1:           
Input: root1 = [1,3,2,5], root2 = [2,1,3,null,4,null,7]        
Output: [3,4,5,5,4,null,7]            

Example 2:           
Input: root1 = [1], root2 = [1,2]             
Output: [2,2]           

Constraints:          
* The number of nodes in both trees is in the range [0, 2000].       
* -104 <= Node.val <= 104

#### My AC Version        
这道题玩儿树，怎么也得是层次遍历或前中后序遍历吧，跟 BFS、DFS 有几毛钱关系？     
先来一个前序遍历版本。          
```c++       
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    TreeNode* mergeTrees(TreeNode* root1, TreeNode* root2) {
        if (!root1 && ! root2)  return nullptr;
        TreeNode* root = new TreeNode();
        Traverse(root, root1, root2);
        return root;
    }
private:
    void Traverse(TreeNode* root, TreeNode* root1, TreeNode* root2){
        if (root1 && root2) {
            root -> val = root1 -> val + root2 -> val;
            if(root1 -> left || root2 -> left){
                root -> left = new TreeNode();
                Traverse(root -> left, root1 -> left, root2 -> left);
            }
            if(root1 -> right || root2 -> right){
                root -> right = new TreeNode();
                Traverse(root -> right, root1 -> right, root2 -> right);
            }
        }else if(root1 && !root2){
            root -> val = root1 -> val;
            if(root1 -> left){
                root -> left = new TreeNode();
                Traverse(root -> left, root1 -> left, nullptr);
            }
            if(root1 -> right){
                root -> right = new TreeNode();
                Traverse(root -> right, root1 -> right, nullptr);
            }
        }
        else if (!root1 && root2){
            root -> val = root2 -> val;
            if(root2 -> left){
                root -> left = new TreeNode();
                Traverse(root -> left, nullptr, root2 -> left);
            }
            if(root2 -> right){
                root -> right = new TreeNode();
                Traverse(root -> right, nullptr, root2 -> right);
            }
        }
        else
            return;
    }
};       
```         
Runtime: 32 ms, faster than 88.12% of C++ online submissions for Merge Two Binary Trees.        
Memory Usage: 33.6 MB, less than 14.71% of C++ online submissions for Merge Two Binary Trees.    
时间表现还过得去，空间表现差了些，一会儿看讨论区。          
虽然用了递归遍历，但我的版本过于复杂，对于遍历中两树节点存在与否的四种可能分别进了讨论。如果硬要按图遍历来讲，这大概算一个深度优先DFS？     

另一方面，由于需要考虑的情况相对较多（两棵树要同步进行，当前节点存在与否，左右子节点存在与否的组合情况），不建议使用层次遍历，或叫广度优先。看看讨论区的奇技淫巧。         

#### Discuss solution        
```c++       
class Solution {
public:
    TreeNode* mergeTrees(TreeNode* t1, TreeNode* t2) {
        if (t1 && t2) { 
            t1->val+=t2->val;
            t1->left = mergeTrees(t1->left,t2->left);  
            t1->right = mergeTrees(t1->right, t2->right);
        } else { 
            return t1 ? t1 : t2;  
        }
        return t1; 
        }
};
```      
Runtime: 24 ms, faster than **99.01%** of C++ online submissions for Merge Two Binary Trees.       
Memory Usage: 32.3 MB, less than 85.30% of C++ online submissions for Merge Two Binary Trees.         

什么神奇解法，惊为天人！       
这就不是人能写出来的代码。简洁，优美，明了，甚至不需要explination 。没有十年 bug 经验，写不出这样优雅的代码。      


### 116 Populating Next Right Pointers in Each Node         

You are given a perfect binary tree where all leaves are on the same level, and every parent has two children. The binary tree has the following definition:      
```c++        
struct Node {
  int val;
  Node *left;
  Node *right;
  Node *next;
}
```        
Populate each next pointer to point to its next right node. If there is no next right node, the next pointer should be set to NULL.       
Initially, all next pointers are set to NULL.        

Example 1:          
Input: root = [1,2,3,4,5,6,7]           
Output: [1,#,2,3,#,4,5,6,7,#]           
Explanation: Given the above perfect binary tree (Figure A), your function should populate each next pointer to point to its next right node, just like in Figure B. The serialized output is in level order as connected by the next pointers, with '#' signifying the end of each level.            

Example 2:       
Input: root = []         
Output: []            

Constraints:           
* The number of nodes in the tree is in the range [0, 212 - 1].           
* -1000 <= Node.val <= 1000

#### My AC Version                      
```c++            
/*
// Definition for a Node.
class Node {
public:
    int val;
    Node* left;
    Node* right;
    Node* next;

    Node() : val(0), left(NULL), right(NULL), next(NULL) {}

    Node(int _val) : val(_val), left(NULL), right(NULL), next(NULL) {}

    Node(int _val, Node* _left, Node* _right, Node* _next)
        : val(_val), left(_left), right(_right), next(_next) {}
};
*/

class Solution {
public:
    Node* connect(Node* root) {
        if (!root) return root;
        list<Node*> nodeList;
        int count = 0, layer = 1;
        nodeList.push_back(root);
        while(!nodeList.empty()){
            Node* thisNode = nodeList.front();
            nodeList.pop_front();
            count ++;
            if ((int)pow(2, layer) == count+1){
                thisNode-> next = NULL;
                layer++;
            }
            else
                thisNode -> next = nodeList.front();
            if (thisNode -> left && thisNode -> right){
                nodeList.push_back(thisNode -> left);
                nodeList.push_back(thisNode -> right);
            }
        }
        return root;
    }
};
```          
Runtime: 24 ms, faster than 35.21% of C++ online submissions for Populating Next Right Pointers in Each Node.           
Memory Usage: 18.3 MB, less than 6.14% of C++ online submissions for Populating Next Right Pointers in Each Node.          
朴素的层次遍历，或者叫广度优先？这题似乎只能有层次来解，本意是使用位运算符判断是否到完美二叉树某一层最有段，但位运算符还是有点门槛的，遂弃之不用，用 pow。     
我们的解时间和空间表现都不算好，看看讨论区有什么奇技淫巧。         

#### Discuss solution             

受讨论区[My simple non-iterative C++ code with O(1) memory](https://leetcode.com/problems/populating-next-right-pointers-in-each-node/discuss/37578/My-simple-non-iterative-C%2B%2B-code-with-O(1)-memory)启发，重写出如下解：            
```c++
class Solution {
public:
    Node* connect(Node* root) {
        if (!root) return root;
        Node* currNode = root;
        while(currNode -> left){
            currNode -> left -> next = currNode -> right;
            Node* p = currNode;
            while(p -> next){
                p -> right -> next = p -> next -> left;
                p = p -> next;
                p -> left -> next = p -> right;
            }
            p -> next = NULL;
            currNode = currNode -> left;
        }
        return root;
    }
};
```        
Runtime: 16 ms, faster than **89.82%** of C++ online submissions for Populating Next Right Pointers in Each Node.          
Memory Usage: 16.7 MB, less than **85.93%** of C++ online submissions for Populating Next Right Pointers in Each Node.           
非常巧妙的解法。本质上仍然是层次遍历，但巧妙的使用了 next 指针，而不是死板的使用 list 容器实现。节省了相当空间，同时时间效率也得到了较大提升。         

## Day 9 BFS / DFS 广度优先/深度优先搜索             
[GitHub 连接](https://github.com/OUCliuxiang/leetcode/blob/master/StudyPlan/Algo1/day9)          

### 542. 01 Matrix         
Given an m x n binary matrix mat, return the distance of the nearest 0 for each cell.          
The distance between two adjacent cells is 1.           

Example 1:          
Input: mat = [          
    [0,0,0],           
    [0,1,0],           
    [0,0,0]]                      
Output: [
    [0,0,0],          
    [0,1,0],            
    [0,0,0]]              

Example 2:            
Input: mat = [           
    [0,0,0],          
    [0,1,0],          
    [1,1,1]]                       
Output: [           
    [0,0,0],          
    [0,1,0],        
    [1,2,1]]            

Constraints:         
* m == mat.length          
* n == mat[i].length           
* 1 <= m, n <= 104             
* 1 <= m * n <= 104           
* mat[i][j] is either 0 or 1.         
* There is at least one 0 in mat.

#### My TLE Version           
```c++       
class Solution {
public:
    vector<vector<int>> updateMatrix(vector<vector<int>>& mat) {
        int Row = mat.size(), Col = mat[0].size();
        for (int i = 0; i < Row; i ++){
            for (int j = 0; j < Col; j++){
                if (mat[i][j] != 0){
                    int minDis = 0x7fff;
                    list<pair<int, int>> itemList;
                    itemList.push_back(make_pair(i,j));
                    while(!itemList.empty()){
                        pair<int, int> thisItem = itemList.front();
                        itemList.pop_front();
                        int row = get<0>(thisItem), col = get<1>(thisItem);
                        if(row < 0 || row == Row || col < 0 || col == Col)
                            continue;
                        if(mat[row][col] == 0 ){
                            minDis = abs(row-i)+abs(col-j);
                            break;
                        }
                        itemList.push_back(make_pair(row-1, col));
                        itemList.push_back(make_pair(row+1, col));
                        itemList.push_back(make_pair(row, col-1));
                        itemList.push_back(make_pair(row, col+1));
                    }
                    mat[i][j] = minDis;
                    itemList.~list();
                } 
            }
        }
        return mat;
    }
}; 
```          
本题起初想用 BFS 广度优先一层一层往外找，通过了48个测试用例，在第四十九个用例：     
[[1,1,1],[1,1,1],[1,1,1],[1,1,1],[1,1,1],[1,1,1],[1,1,1],[1,1,1],[1,1,1],[1,1,1],[1,1,1],[1,1,1],[1,1,1],[1,1,1],[1,1,1],[1,1,1],[1,1,1],[1,1,1],[1,1,1],[0,0,0]]
       
Time Limit Exceeded ，卡住了。看看讨论区。        

#### Discuss solution           

思路完全错了，层次搜索不错，一层一层逼近也不错，但应该是从海面（值 0）向陆地（值 1）逼近。       
```c++             
class Solution {
public:
    int DIRS[5] = {-1,0,1,0,-1}, Row, Col;
    vector<vector<int>> updateMatrix(vector<vector<int>>& mat) {
        Row = mat.size();
        Col = mat[0].size();
        int maxDis = Row+Col;
        queue<pair<int, int>> q; 
        for (int i = 0; i < Row; i++ ){
            for (int j = 0; j < Col; j++){
                mat[i][j] == 0 ? q.push(make_pair(i, j)) : void( mat[i][j] = maxDis); 
            }
        }
        while(!q.empty()){
            auto [x, y] = q.front();    q.pop();
            for (int i = 0; i < 4; i++){
                int x1 = x + DIRS[i], y1 = y + DIRS[i+1];
                if(isValid(x1, y1) && mat[x1][y1] == maxDis){
                    q.push(make_pair(x1, y1));
                    mat[x1][y1] = min(mat[x1][y1], mat[x][y] + 1);
                }                  
            }
        }
        
        return mat;
    }
    
private:
    bool isValid(int x, int y){
        if (x >= 0 && x < Row && y >=0 && y < Col ) return true;
        return false;
    }
};
```           
Runtime: 68 ms, faster than 63.28% of C++ online submissions for 01 Matrix.        
Memory Usage: 29.9 MB, less than 43.83% of C++ online submissions for 01 Matrix.           

讨论区这个思路就比较好，先遍历一遍矩阵，如果元素值是 0，加入到队列 `queue< pair< int, int>>`，否则将值置 maxValue。这里需要注意，三目运算符内部完成赋值操作需要将返回值强制转换为 void，这是由于赋值操作默认的返回类型是 int 整型，不能被判断语句所接收。随后广度优先读队列，如果读出的值 center 的四周某个方向的值是 maxValue，就证明这块地方是陆地，其值变更为 `min(original, center+1)`，比较难理解。首先明确这个解法思路是从 0 开始一层层向外（值 1 方向）逼近，然后多看两眼悟一悟就能理解了。             

### 994. Rotting Oranges         
You are given an m x n grid where each cell can have one of three values:            
0. representing an empty cell,           
1. representing a fresh orange, or         
2. representing a rotten orange.           

Every minute, any fresh orange that is 4-directionally adjacent to a rotten orange becomes rotten.             
Return the minimum number of minutes that must elapse until no cell has a fresh orange. If this is impossible, return -1.           

Example 1:             
Input: grid = [[2,1,1],[1,1,0],[0,1,1]]            
Output: 4              

Example 2:               
Input: grid = [[2,1,1],[0,1,1],[1,0,1]]           
Output: -1              
Explanation: The orange in the bottom left corner (row 2, column 0) is never rotten, because rotting only happens 4-directionally.        

Example 3:            
Input: grid = [[0,2]]             
Output: 0           
Explanation: Since there are already no fresh oranges at minute 0, the answer is just 0.             

Constraints:             
* m == grid.length            
* n == grid[i].length           
* 1 <= m, n <= 10            
* grid[i][j] is 0, 1, or 2.

### My AC Version            
```c++          
class Solution {
public:
    int Row, Col;
    int DIRS[5] = {-1, 0, 1, 0, -1};
    int orangesRotting(vector<vector<int>>& grid) {
        Row = grid.size();
        Col = grid[0].size();
        queue<tuple<int, int, int>> q;
        int step = 0;
        for (int i = 0; i < Row; i ++)
            for (int j = 0; j < Col; j ++)
                if (grid[i][j] == 2)
                    q.push(make_tuple(i, j, step));
        
        while(!q.empty()){
            auto [x, y, s] = q.front(); q.pop();
            for (int i = 0; i < 4; i++){
                int x1 = x + DIRS[i], y1 = y + DIRS[i+1];
                if (isValid(x1, y1, grid)){
                    grid[x1][y1] = 2;
                    q.push(make_tuple(x1, y1, s+1));
                }
            }
            step = s;
        }
        
        for (int i = 0; i < Row; i ++)
            for (int j = 0; j < Col; j ++)
                if (grid[i][j] == 1)
                    return -1;
        
        return step;
    }
private:
    bool isValid(int x, int y, vector<vector<int>>& grid){
        if (x >= 0 && x < Row && y >= 0 && y < Col && grid[x][y] == 1)
            return true;
        return false;
    }
};
```           
Runtime: 8 ms, faster than 62.77% of C++ online submissions for Rotting Oranges.             
Memory Usage: 13.2 MB, less than 51.36% of C++ online submissions for Rotting Oranges.           
这个题看似简单，却着实费了不少功夫去调。依然是广度优先搜索 BFS，分���三个阶段（三个循环）。第一阶段遍历矩阵，将值为 2 的元素加入到广度优先队列；第二阶段读队列，开始广度优先，一层一层向外蔓延，变 1 为 2，直到队列为空；第三阶段在遍历一遍，看看是否仍存在 1，是则返回 -1，否则返回 step 。           
个人认为我的解法巧妙之处在于使用 tuple 而非 pair 作为队列的基本单元，其中 tuple 中除了横纵坐标之外，另加入了每一层的 step 作为 label ，从而避免了 while 读队列时每一个元素 step+1 的窘境。      
比如，测试用例 1 中 [0,0] 元素旁边的两个 orange 是同时被 rot 的，如果按照常规的广度优先读队列，这两个元素的读取由于有先后之分，于是很难做到 step 统一。但我额外加入一个 step label，就很容易做到统一了。           
看看讨论区有什么奇技淫巧。             

#### Discuss solution            
基本看不懂，但我觉得我的解法和讨论区奇技淫巧也差不了多少，时间空间表现也还过得去，就不卷了。            
 

## Day 10 Recursion/Backtracking 递归/回溯            
[GitHub 连接](https://github.com/OUCliuxiang/leetcode/blob/master/StudyPlan/Algo1/day10)         

### 21. Merge Two Sorted Lists          
Merge two sorted linked lists and return it as a sorted list. The list should be made by splicing together the nodes of the first two lists.            

Example 1:         
Input: l1 = [1,2,4], l2 = [1,3,4]           
Output: [1,1,2,3,4,4]            

Example 2:           
Input: l1 = [], l2 = []             
Output: []              

Example 3:          
Input: l1 = [], l2 = [0]            
Output: [0]           

Constraints:          

* The number of nodes in both lists is in the range [0, 50].      
* -100 <= Node.val <= 100      
* Both l1 and l2 are sorted in non-decreasing order.

#### My AC Version         
```c++          
/**
 
 * Definition for singly-linked list.

 * struct ListNode {
 
 *     int val;
 
 *     ListNode *next;
 
 *     ListNode() : val(0), next(nullptr) {}
 
 *     ListNode(int x) : val(x), next(nullptr) {}
 
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 
 * };
 
 */         

class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        if (!l1 && !l2)
            return l1;
        deque<int> tmp;
        while(l1){
            tmp.push_back(l1-> val); 
            //ListNode* node = l1;      

            l1 = l1 -> next;
            //node = nullptr;          

        }
        while(l2){
            tmp.push_back(l2-> val);
            //ListNode* node = l2;
 
            l2 = l2 -> next;
            //node = nullptr;
 
        }
        sort(tmp.begin(), tmp.end());
        ListNode* node = new ListNode(tmp.front());
        tmp.pop_front();
        ListNode* head = node;
        while (!tmp.empty()){
            node -> next = new ListNode();
            node = node -> next;
            node -> val = tmp.front();
            tmp.pop_front();
            }
        return head;
    }
};
```          
Runtime: 8 ms, faster than 74.64% of C++ online submissions for Merge Two Sorted Lists.          
Memory Usage: 15.3 MB, less than 10.18% of C++ online submissions for Merge Two Sorted Lists.          

极其讨巧的一个做法解法，因为使用了额外的 deque 双向队该解法的空间表现非常差。忙猜要用双指针，看看讨论去区的解法。           

#### Discuss solution          

```c++       
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        if (!l1)    return l2;
        if (!l2)    return l1;
        
        if (l1 -> val < l2 -> val){
            l1 -> next = mergeTwoLists(l1 -> next, l2);
            return l1;
        }else{
            l2 -> next = mergeTwoLists(l1, l2 -> next);
            return l2;
        }
    }
};
```           
Runtime: 4 ms, faster than **95.57%** of C++ online submssions for Merge Two Sorted Lists.        
Memory Usage: 14.8 MB, less than 53.24% of C++ online submissions for Merge Two Sorted Lists.          

卧槽，这递归用的妙啊，简直是妙妙进了米奇妙妙屋，妙到家了。简洁优美，极致的简洁，甚至不需要任何解释。哪怕再添加一行注释，都破坏了整段程序的简洁美感。          

### 206. Reverse Linked List             
Given the head of a singly linked list, reverse the list, and return the reversed list.            

Example 1:        
Input: head = [1,2,3,4,5]            
Output: [5,4,3,2,1]           

Example 2:        
Input: head = [1,2]           
Output: [2,1]            

Example 3:           
Input: head = []          
Output: []          

#### Discuss solution          

这道题，不使用另建可翻转容器的方法搞出来。但如果另建容器，过于作弊了，脱离了本题练习递归/回溯的本意。直接跳到讨论区。          
```c++                 
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if( !head || !(head -> next))   return head;
        ListNode* node = reverseList(head -> next);
        head -> next -> next = head;
        head -> next = nullptr;
        return node;
    }
};
```               
Runtime: 4 ms, faster than **95.91%** of C++ online submissions for Reverse Linked List.             
Memory Usage: 8.6 MB, less than 10.55% of C++ online submissions for Reverse Linked List.              
时间表现非常好。           
但，这道题的难度分级是 Easy，这分明一点都不 Easy。递归用的这么熟练，就不是初学者能掌握的等级。        
这个递归吧，五行代码，每一行都是灵魂，都起着至关重要的作用。文字解释不清楚，画个图，可能更清楚一点：             
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/leetcode/leetcode001.png"></div>       


## Day 10 Recursion/Backtracking 递归/回溯            
[GitHub 连接](https://github.com/OUCliuxiang/leetcode/blob/master/StudyPlan/Algo1/day11)         

### 77. Combinations                 
Given two integers n and k, return all possible combinations of k 
numbers out of the range [1, n].         
You may return the answer in any order.             

Example 1:        
Input: n = 4, k = 2        
Output:          
[2,4],        
[3,4],       
[2,3],      
[1,2],         
[1,3],           
[1,4],]          
 
Example 2:           
Input: n = 1, k = 1       
Output: [[1]]       
 
Constraints:      
* 1 <= n <= 20      
* 1 <= k <= n      
 