# LeetCode算法设计题解(二)

@(程序设计算法)

标签： 算法设计题解

----------

## Climbing Stairs
### 问题
You are climbing a stair case. It takes n steps to reach to the top.
Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?
### 解答
```C++
class Solution {
public:
    int climbStairs(int n) {
        if(n <= 0)
            return 0;
        if(n == 1 || n == 2)
            return 1;
        return climbStairs(n-1) + climbStairs(n-2);
    }
};
```
但是上述方案中有很多的重复计算，在运算过程中，往往会超时或者是栈溢出。我们需要将已经计算过的状态保存一份，这样可以减少重复计算。这里自己可以绘制一个树状结构图就可以了！
```C++
class Solution {
public:
    int climbStairs(int n) {
        if(n >= 0 && n <= 2)
            return n;
        int step_one = 2;
        int step_two = 1;
        int total = 0;
        for(int i=3;i<=n;i++){
            total = step_one + step_two;
            step_two = step_one;
            step_one = total;
        }
        return total;
    }
};
```
## Ugly Number
### 问题
Write a program to check whether a given number is an ugly number.

Ugly numbers are positive numbers whose prime factors only include 2, 3, 5. For example, 6, 8 are ugly while 14 is not ugly since it includes another prime factor 7.

Note that 1 is typically treated as an ugly number.
### 解答
```C++
class Solution {
public:
    bool isUgly(int num) {
        if(num == 1)
            return true;
        if(num == 0)
            return false;
        bool flag = false;
        if(num%2 == 0)
            flag = isUgly(num/2);
        else if(num%3 == 0)
            flag = isUgly(num/3);
        else if(num%5 == 0)
            flag = isUgly(num/5);
        return flag;
    }
};
```
## Ugly Number II
### 问题
Write a program to find the n-th ugly number.

Ugly numbers are positive numbers whose prime factors only include 2, 3, 5. For example, 1, 2, 3, 4, 5, 6, 8, 9, 10, 12 is the sequence of the first 10 ugly numbers.

Note that 1 is typically treated as an ugly number.

Hint:

1. The naive approach is to call isUgly for every number until you reach the $n_{th}$ one. Most numbers are not ugly. Try to focus your effort on generating only the ugly ones.
2. An ugly number must be multiplied by either 2, 3, or 5 from a smaller ugly number.
3. The key is how to maintain the order of the ugly numbers. Try a similar approach of merging from three sorted lists: L1, L2, and L3.
4. Assume you have $U_k$, the $k_{th}$ ugly number. Then $U_{k+1}$ must be Min($L_1$ * 2, $L_2$ * 3, $L_3$ * 5).
### 解答
我们的目的是寻找第n个uglyNum，其实我们可以去主动的构造第n个unglyNum，这样的速度是很快的！

```C++
int nthUglyNumber(int n) {
    set<long long> isUgly;
    isUgly.insert(1);
    long long top = 0;
    for(int i=0; i<n; i++){
        top = *isUgly.lower_bound(top+1);
        isUgly.insert(top*2);
        isUgly.insert(top*3);
        isUgly.insert(top*5);
    }
    return top;
}
```

同时可以使用另外的STL进行数据的存储

```C++
class Solution {
private:
    int minThree(int a, int b, int c){
        int value = a;
        if(b < value)
            value = b;
        if(c < value)
            value = c;
        return value;
    }

public:
    int nthUglyNumber(int n) {
        queue<int> L1, L2, L3;
        L1.push(1);
        L2.push(1);
        L3.push(1);

        int value;
        int i;
        for(i=0; i<n; i++){
            value = minThree(L1.front(), L2.front(), L3.front());
            if(value == L1.front()) L1.pop();
            if(value == L2.front()) L2.pop();
            if(value == L3.front()) L3.pop();

            L1.push(value*2);
            L2.push(value*3);
            L3.push(value*5);
        }
        return value;
    }
};
```
## Count Primes
### 问题
Count the number of prime numbers less than a non-negative number, n.
### 解答
```C++
class Solution {
public:
    int countPrimes(int n) {
        if(--n < 2) 
            return 0;
        int m = (n + 1)/2, count = m, k, u = (sqrt(n) - 1)/2;
        bool notPrime[m] = {0};
    
        for(int i = 1; i <= u;i++)
            if(!notPrime[i])
              for(k = (i+ 1)*2*i; k < m;k += i*2 + 1)
                  if (!notPrime[k]){
                      notPrime[k] = true;
                      count--;
                  }
        return count;
    }
};
```
**用筛法求素数的基本思想是**
把从1开始的、某一范围内的正整数从小到大顺序排列， 1不是素数，首先把它筛掉。剩下的数中选择最小的数是素数，然后去掉它的倍数。依次类推，直到筛子为空时结束。如有：
```
1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30
1不是素数，去掉。剩下的数中2最小，是素数，去掉2的倍数，余下的数是：
3 5 7 9 11 13 15 17 19 21 23 25 27 29
剩下的数中3最小，是素数，去掉3的倍数，如此下去直到所有的数都被筛完，求出的素数为：
2 3 5 7 11 13 17 19 23 29
```
## Happy Number
### 问题
Write an algorithm to determine if a number is "happy".

A happy number is a number defined by the following process: Starting with any positive integer, replace the number by the sum of the squares of its digits, and repeat the process until the number equals 1 (where it will stay), or it loops endlessly in a cycle which does not include 1. Those numbers for which this process ends in 1 are happy numbers.

Example: 19 is a happy number

$1^2$ + $9^2$ = 82
$8^2$ + $2^2$ = 68
$6^2$ + $8^2$ = 100
$1^2$ + $0^2$ + $0^2$ = 1
### 解答
快乐数（happy number）有以下的特性：在给定的进位制下，该数字所有数位(digits)的平方和，得到的新数再次求所有数位的平方和，如此重复进行，最终结果必为1。
```C++
class Solution {
public:
    bool isHappy(int n) {
        if (n < 1)
            return false;
        if (n == 1)
            return true;
        unordered_set<int> showedNums;
        showedNums.insert(n);

        while(true)
        {
            int s = 0;
            while(n)
            {
                s += (n % 10) * (n % 10);
                n = n / 10;
            }

            if (s == 1)
                return true;
            else if (showedNums.find(s) != showedNums.end())
                return false;
            n = s;
            showedNums.insert(s);
        }
    }
};
```
## Merge Two Sorted Lists
### 问题
Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.
```C++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
```
### 解答
```C++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        ListNode* root = NULL,*p = NULL;
        ListNode* pre = root;
        
        while(l1 != NULL && l2 != NULL){
            if(l1->val < l2->val){
                p = l1;
                l1 = l1->next;
            } else {
                p = l2;
                l2 = l2->next;
            }
            if(root == NULL){
                root = p;
                pre = p;
            } else {
                pre->next = p;
                pre = p;
            }
        }
        if(l1){
            if(root == NULL)
                root = l1;
            else
                pre->next = l1;
        }
        if(l2){
            if(root == NULL)
                root = l2;
            else
                pre->next = l2;
        }
        return root;
    }
};
```
## Merge Sorted Array
### 问题
Given two sorted integer arrays nums1 and nums2, merge nums2 into nums1 as one sorted array.
### 解答
```C++
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        if(n == 0)
            return ;
        if(m == 0)
            for(int i=0;i<n;i++)
                nums1[i] = nums2[i];
        
        int i=m-1,j=n-1;
        while(i >= 0 && j >= 0){
            if(nums1[i] > nums2[j]){
                nums1[i+j+1] = nums1[i];
                i--;
            } else {
                nums1[i+j+1] = nums2[j];
                j--;
            }
        }
        if(j>=0){
            for(int k=0;k<=j;k++)
                nums1[k] = nums2[k];
        }
    }
};
```
## Sort List
### 问题
Sort a linked list in O(n log n) time using constant space complexity.
```C++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
```
### 解答
```C++
class Solution {
public:
    ListNode* sortList(ListNode* head) {
        if (head == NULL || head->next == NULL)
            return head;
        ListNode *fast = head->next->next, *slow = head;
        while(fast && fast->next) {
            fast = fast->next->next;
            slow = slow->next;
        }
        ListNode *r = sortList(slow->next);
        slow->next = NULL;
        ListNode *l = sortList(head);
        return merge(l, r);
    }

    ListNode *merge(ListNode *l, ListNode *r) {
        ListNode *dummy = new ListNode(0), *node = NULL;
        node = dummy;
        while (l && r) {
            if (l->val < r->val) {
                node->next = l;
                l = l->next;
            } else {
                node->next = r;
                r = r->next;
            }
            node = node->next;
        }
        if (l)
            node->next = l;
        else 
            node->next = r;
        node = dummy->next;
        delete dummy;
        return node;
    }
};
```
这里找到一个更加好理解的归并排序
```C++
class Solution {
public:
    ListNode * findListMid(ListNode * head)
    {
        if(head == NULL)return NULL;
        ListNode * fast = head;
        ListNode * slow = head;
        while(fast->next)
        {
            if(fast->next->next)
            {
                fast = fast->next->next;
                slow = slow->next;
            }
            else
                return slow;
        }
        return slow;
    }
    ListNode * merge(ListNode * list1,ListNode * list2)
    {
        if(list1 == NULL)return list2;
        if(list2 == NULL)return list1;
        ListNode * head = NULL;
        if(list1->val >= list2->val)
        {
            head = list2;
            list2 = list2->next;
        }
        else
            if(list1->val < list2->val)
            {
                head = list1;
                list1 = list1->next;
            }
            ListNode * tail = head;
        while(list1 && list2)
        {
            if(list1->val >= list2->val)
            {
                tail->next = list2;
                tail = list2;
                list2 = list2->next;
            }  
            else
                if(list1->val < list2->val)
                {
                    tail->next = list1;
                    tail = list1;
                    list1 = list1->next;
                }  
        }
        if(list1)
            tail->next = list1;
        if(list2)
            tail->next = list2;
        return head;
    }
    ListNode *sortList(ListNode *head) {
        if(head == NULL)return NULL;
        if(head->next == NULL)return head;
        ListNode * mid = findListMid(head);
        ListNode * m = mid->next;
        mid->next = NULL;
        ListNode * list1 = sortList(head);
        ListNode * list2 = sortList(m);
        ListNode * list = merge(list1,list2);
        return list;
    }
};
```
## String to Integer (atoi)
### 问题
Implement atoi to convert a string to an integer.

Hint: Carefully consider all possible input cases. If you want a challenge, please do not see below and ask yourself what are the possible input cases.

Notes: It is intended for this problem to be specified vaguely (ie, no given input specs). You are responsible to gather all the input requirements up front.
>Requirements for atoi:
The function first discards as many whitespace characters as necessary until the first non-whitespace character is found. Then, starting from this character, takes an optional initial plus or minus sign followed by as many numerical digits as possible, and interprets them as a numerical value.
The string can contain additional characters after those that form the integral number, which are ignored and have no effect on the behavior of this function.
If the first sequence of non-whitespace characters in str is not a valid integral number, or if no such sequence exists because either str is empty or it contains only whitespace characters, no conversion is performed.
If no valid conversion could be performed, a zero value is returned. If the correct value is out of the range of representable values, INT_MAX (2147483647) or INT_MIN (-2147483648) is returned.

### 解答
```C++
class Solution {
public:
    int myAtoi(string str) {
        int flag=1,res=0,curr,start=0,f=0;
        int sflag = 0;
        int len = str.size();
        for(int i=0;i<len;i++) {
            if(str[i] == ' ')
                start++;
            else
                break;
        }
        if(str[start] == '-') {
            flag = -1;
            start +=1;  //去掉-号
            f = 1;
        } else if(str[start] == '+') {
            start +=1;  //去掉-号
            f = 1;
        }
        for(int i = start ; i < len; i++) {
            if(str[i] == ' ' && sflag == 0 && f == 0)
                continue;
            else if(str[i] == ' ' && sflag == 0 && f == 1)
                return 0;
            else if(str[i] == ' ' && sflag == 1)
                return res*flag;
            if(str[i] < '0' || str[i] >'9' && sflag == 1)   //非法时返回0
               return res*flag;
            else if(str[i] < '0' || str[i] >'9' && sflag == 0)
                return 0;
            else {
                sflag = 1;
                f= 0;
                curr = str[i] - '0';
                if(res > (INT_MAX -curr)/10 && flag == 1)
                    return INT_MAX;
                if(res > (INT_MAX-curr)/10 && flag == -1)
                    return INT_MIN;
                else {
                    res = res*10+curr;
                }
            }
        }
        return res*flag;
    }
};
```
上述方案是个十分详细的解答，现在需要使用c++中相应的函数来处理这个字符串，使得代码看起来简介明了！
## Valid Number
### 问题
Validate if a given string is numeric.

Some examples:
```
"0" => true
" 0.1 " => true
"abc" => false
"1 a" => false
"2e10" => true
```
Note: It is intended for the problem statement to be ambiguous. You should gather all requirements up front before implementing one.
### 解答

## Implement Queue using Stacks
### 问题
Implement the following operations of a queue using stacks.
1. push(x) -- Push element x to the back of queue.
2. pop() -- Removes the element from in front of queue.
3. peek() -- Get the front element.
4. empty() -- Return whether the queue is empty.

Notes:
You must use only standard operations of a stack -- which means only push to top, peek/pop from top, size, and is empty operations are valid.
Depending on your language, stack may not be supported natively. You may simulate a stack by using a list or deque (double-ended queue), as long as you use only standard operations of a stack.
You may assume that all operations are valid (for example, no pop or peek operations will be called on an empty queue).
### 解答
```C++
class Queue {
public:
    // Push element x to the back of queue.
    void push(int x) {
        s1.push(x);
    }

    // Removes the element from in front of queue.
    void pop(void) {
        transfer();
        s2.pop();
    }

    // Get the front element.
    int peek(void) {
        transfer();
        return s2.top();
    }

    // Return whether the queue is empty.
    bool empty(void) {
        return s1.empty() && s2.empty();
    }
    
    void transfer(){
        int x = 0;
        if (s2.empty()){
            while (!s1.empty()){
                x= s1.top();
                s1.pop();
                s2.push(x);
            }
        }
    }
    
private:
    stack<int> s1,s2;
};
```

## Balanced Binary Tree
### 问题
Given a binary tree, determine if it is height-balanced.

For this problem, a height-balanced binary tree is defined as a binary tree in which the depth of the two subtrees of every node never differ by more than 1.
```C++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
```
### 解答
```C++
class Solution {
public:
    int getDepth(TreeNode* root){
        if(root == NULL)
            return 0;
        int l = getDepth(root->left);
        int r = getDepth(root->right);
        return (l>r?l:r) + 1;
    }
    
    bool isBalanced(TreeNode* root) {
        if(root == NULL)
            return true;
        int l = getDepth(root->left);
        int r = getDepth(root->right);
        if(abs(l-r) > 1)
            return false;
        return isBalanced(root->left) && isBalanced(root->right);
        
    }
};
```
## Maximum Depth of Binary Tree
### 问题
Given a binary tree, find its maximum depth.

The maximum depth is the number of nodes along the longest path from the root node down to the farthest leaf node.
```C++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
```
### 解答
```C++
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if(root == NULL)
            return 0;
        int l = maxDepth(root->left);
        int r = maxDepth(root->right);
        return (l>r?l:r) + 1;
    }
};
```
## Minimum Depth of Binary Tree
### 问题
Given a binary tree, find its minimum depth.

The minimum depth is the number of nodes along the shortest path from the root node down to the nearest leaf node.
```C++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
```
### 解答
```C++
class Solution {
public:
    int minDepth(TreeNode* root) {
        if(root == NULL)
            return 0;
        if(root->left == NULL && root->right == NULL)
            return 1;
        int l=0,r=0;
        if(root->left != NULL && root->right != NULL) {
            l = minDepth(root->left);
            r = minDepth(root->right);
        } else if(root->left == NULL && root->right != NULL) {
            r = minDepth(root->right);
        } else if(root->right == NULL && root->left != NULL) {
            l = minDepth(root->left);
        }
        if(l == 0 && r != 0)
            return r + 1;
        else if(r ==0 && l != 0)
            return l + 1;
        else 
            return (l>r?r:l) + 1;
    }
};
```
## Binary Tree Level Order Traversal
### 问题
Given a binary tree, return the level order traversal of its nodes' values. (ie, from left to right, level by level).

For example:
```C++
Given binary tree {3,9,20,#,#,15,7},
    3
   / \
  9  20
    /  \
   15   7
return its level order traversal as:
[
  [3],
  [9,20],
  [15,7]
]
```
OJ's Binary Tree Serialization:
The serialization of a binary tree follows a level order traversal, where '#' signifies a path terminator where no node exists below.

Here's an example:
```
   1
  / \
 2   3
    /
   4
    \
     5
```
The above binary tree is serialized as "{1,2,3,#,#,4,#,#,5}".
```C++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
```
### 解答
这题目就是二叉树的层次遍历！很简单的，也是很经典的代码！
```C++
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int> > ret;
        if(root == NULL)
            return ret;
        TreeNode * temp;
        queue<TreeNode *>q;
        q.push(root);
        int curLevelCount = 0;
        while (!q.empty()){
            curLevelCount = q.size();
            vector<int> level;
            while (curLevelCount--){
                temp = q.front();
                level.push_back(temp->val);
                if (temp->left != NULL)
                    q.push(temp->left);
                if (temp->right != NULL)
                    q.push(temp->right);
                q.pop();
            }
            ret.push_back(level);
        }
        return ret;
    }
};
```
## Binary Tree Level Order Traversal II
### 问题
Given a binary tree, return the bottom-up level order traversal of its nodes' values. (ie, from left to right, level by level from leaf to root).

For example:
```
Given binary tree {3,9,20,#,#,15,7},
    3
   / \
  9  20
    /  \
   15   7
return its bottom-up level order traversal as:
[
  [15,7],
  [9,20],
  [3]
]
```
### 解答
```C++
class Solution {
public:
    vector<vector<int>> levelOrderBottom(TreeNode* root) {
        vector<vector<int> > ret;
        if(root == NULL)
            return ret;
        TreeNode* tmp;
        queue<TreeNode*> q;
        q.push(root);
        int level_cnt = 0;
        while(!q.empty()){
            level_cnt = q.size();
            vector<int> level;
            while(level_cnt--){
                tmp = q.front();
                q.pop();
                level.push_back(tmp->val);
                if(tmp->left != NULL)
                    q.push(tmp->left);
                if(tmp->right != NULL)
                    q.push(tmp->right);
            }
            ret.insert(ret.begin(),level);
        }
        return ret;
    }
};
```
## Compare Version Numbers
### 问题
Compare two version numbers version1 and version2.
```C++
If version1 > version2:
    return 1
if version1 < version2:
    return -1
otherwise return 0.
```
You may assume that the version strings are non-empty and contain only digits and the **.** character.
The **.** character does not represent a decimal point and is used to separate number sequences.
For instance, **2.5** is not "two and a half" or "half way to version three", it is the fifth second-level revision of the second first-level revision.

Here is an example of version numbers ordering:
```
0.1 < 1.1 < 1.2 < 13.37
```
### 解答
```C++
class Solution {
public:
    int compareVersion(string version1, string version2) {
        if(version1 == version2)
            return 0;

        int v1 = 0, v2 = 0;
        while(v1 < version1.length() || v2 < version2.length()) {
            string temp1 = "",temp2 = "";
            while(version1[v1] != '.' && v1 < version1.length()) {
                temp1 +=version1[v1];
                ++v1;
            }

            while(version2[v2] != '.' && v2 < version2.length()) {
                temp2 +=version2[v2];
                ++v2;
            }

            if(temp1 == "") temp1 = "0";
            if(temp2 == "") temp2 = "0";

            if(stoi(temp1) != stoi(temp2)) {
                return stoi(temp1) > stoi(temp2) ? 1 : -1;
            }
            ++v1;
            ++v2;
        }
        return 0;
    }
};
```
## Symmetric Tree
### 问题
Given a binary tree, check whether it is a mirror of itself (ie, symmetric around its center).

For example, this binary tree is symmetric:
```
    1
   / \
  2   2
 / \ / \
3  4 4  3
```
But the following is not:
```
    1
   / \
  2   2
   \   \
   3    3
```
涉及的数据结构
```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
```
### 解答
```C++
class Solution {
public:
    bool isSymmetric(TreeNode* root) {
        if (root == NULL)
            return true;

        return checkSymmetric(root->left, root->right);
    }
    
    bool checkSymmetric(TreeNode *leftSymmetricNode, TreeNode *rightSymmetricNode)
    {
        if (leftSymmetricNode == NULL && rightSymmetricNode == NULL)
            return true;
        if (leftSymmetricNode == NULL || rightSymmetricNode == NULL)
            return false;
        if (leftSymmetricNode->val == rightSymmetricNode->val)
            return checkSymmetric(leftSymmetricNode->left, rightSymmetricNode->right) && checkSymmetric(leftSymmetricNode->right, rightSymmetricNode->left);
        return false;
    }
};
```
## Remove Element
### 问题
Given an array and a value, remove all instances of that value in place and return the new length.

The order of elements can be changed. It doesn't matter what you leave beyond the new length.
### 解答
```C++
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int len = nums.size();
        int i=0, j=0;

        while(j<len) {
            while(nums[j] == val) {
                j++;
            }
            nums[i] = nums[j];
            if (j<len) {
                i++;
                j++;
            }
        }
        return i;
    }
};
```
## Remove Duplicates from Sorted Array
### 问题
Given a sorted array, remove the duplicates in place such that each element appear only once and return the new length.

Do not allocate extra space for another array, you must do this in place with constant memory.

For example,
```C++
Given input array nums = [1,1,2], Your function should return length = 2, with the first two elements of nums being 1 and 2 respectively. It doesn't matter what you leave beyond the new length.
```
### 解答
```C++
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        int len = nums.size();
        if(!len)
            return len;
        int pre = 0;
        for(int i=1;i<len;) {
            if(nums[pre] == nums[i]) 
                i++;
            else {
                nums[++pre] = nums[i++];
            }
        }
        return pre+1;
    }
};
```
## Plus One
### 问题
Given a non-negative number represented as an array of digits, plus one to the number.

The digits are stored such that the most significant digit is at the head of the list.
### 解答
```C++
class Solution {
public:
    vector<int> plusOne(vector<int>& digits) {
        int len = digits.size();
        int add = 1;
        for(int i=len-1;i>=0;i--) {
            int num = digits[i] + add;
            add = num / 10;
            digits[i] = num % 10;
        }
        if(add)
            digits.insert(digits.begin(),add);
        return digits;
    }
};
```
## Add Binary
### 问题
Given two binary strings, return their sum (also a binary string).

For example,
```
a = "11"
b = "1"
Return "100".
```
### 解答
```C++
class Solution {
public:
    
    string addBinary(string a, string b) {
        int n = a.size() - 1;
        int m = b.size() - 1;
        string res = "";
        int c = 0; // carry
        int s = 0; // sum
    
        while (n >= 0 & m >= 0) {
            s = a[n--] - '0' + b[m--] - '0' + c;
            c = s / 2;
            res = to_string(s % 2) + res;
        }
    
        while (n >= 0) {
            s = a[n--] - '0' + c;
            c = s / 2;  
            res = to_string(s % 2) + res;
        }
    
        while (m >= 0) {
            s = b[m--] - '0' + c;
            c = s / 2;  
            res = to_string(s % 2) + res;
        }
    
        if (c == 1) res = to_string(c) + res;
    
        return res;
    }
};
```
## Odd Even Linked List
### 问题
Given a singly linked list, group all odd nodes together followed by the even nodes. Please note here we are talking about the node number and not the value in the nodes.

You should try to do it in place. The program should run in O(1) space complexity and O(nodes) time complexity.

Example:
```
Given 1->2->3->4->5->NULL,
return 1->3->5->2->4->NULL.
```
Note:
The relative order inside both the even and odd groups should remain as it was in the input. 
The first node is considered odd, the second node even and so on.
```
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
```
### 解答
本题目使用双指针，通过双指针，在通过运算过程中，一个记录奇数指针，一个记录偶数指针，而后将奇数指针的末尾指向偶数指针的开头就可以了！
```C++
class Solution {
public:
    ListNode* oddEvenList(ListNode* head) {
        if(head == NULL)
            return head;
        ListNode *head_even = head->next;
        ListNode *a = head, *b = head_even;
    
        while(b != NULL && b->next != NULL){
            a->next = b->next;
            a = a->next;
            b->next = a->next;
    
            b = b->next;
        }
        a->next = head_even;
        return head;
    }
};
```
## Bulb Switcher
### 问题
There are n bulbs that are initially off. You first turn on all the bulbs. Then, you turn off every second bulb. On the third round, you toggle every third bulb (turning on if it's off or turning off if it's on). For the nth round, you only toggle the last bulb. Find how many bulbs are on after n rounds.
Example:
```
Given n = 3. 

At first, the three bulbs are [off, off, off].
After first round, the three bulbs are [on, on, on].
After second round, the three bulbs are [on, off, on].
After third round, the three bulbs are [on, off, off]. 

So you should return 1, because there is only one bulb is on.
```
### 解答
对于第i栈灯泡，当i的因子个数为奇数时，最终会保持点亮状态，例如9的因子为1，3，9. 而当i的因子个数为偶数时，最终会保持熄灭状态，例如8的因子为：1，2，4，8.当且仅当i为完全平方数时，其因子个数为奇数.
我们知道，每当灯泡会改变状态，也就是 toggle 时，是因为它出现在了某个数的整数倍上。
```
对于第1个灯泡：1*1，会改变1次状态，即 off->on
对于第2个灯泡：1*2，2*1，会改变2次状态，即 off->on->off
对于第3个灯泡：1*3，3*1，会改变2次状态，即 off->on->off
对于第4个灯泡：1*4，2*2，4*1，会改变3次状态，即 off->on->off->on

会发现，每当我找到一个数的整数倍，总会找到对称的一个整数倍，例如 1*2，就肯定会有一个 2*1。唯一的例外出现在平方数上，例如 4 = 2*2，只有一次整数倍。

每次作为偶数次整数倍，最终的灯泡都会还原为 off；只有作为奇数次整数倍，最终的灯泡都会 on。
也就是说，最终亮的灯泡数目由小于其的最大平方数确定。
```
```C++
class Solution {
public:
    int bulbSwitch(int n) {
        return (int)sqrt(n);
    }
};
```
## Maximum Product of Word Lengths
### 问题
Given a string array words, find the maximum value of length(word[i]) * length(word[j]) where the two words do not share common letters. You may assume that each word will contain only lower case letters. If no such two words exist, return 0.
```
Example 1:
Given ["abcw", "baz", "foo", "bar", "xtfn", "abcdef"]
Return 16
The two words can be "abcw", "xtfn".

Example 2:
Given ["a", "ab", "abc", "d", "cd", "bcd", "abcd"]
Return 4
The two words can be "ab", "cd".

Example 3:
Given ["a", "aa", "aaa", "aaaa"]
Return 0
No such pair of words.
```
### 解答
```C++
class Solution {
public:

    int maxProduct(vector<string>& words) {
        vector<pair<unsigned int,int> > res;
        for(int i=0;i<words.size();i++) {
            unsigned int key = 0;
            for(int j=0;j<words[i].length();j++) {
                key |=  (1 << (words[i][j] - 'a'));
            }
            res.push_back(make_pair(key,words[i].length()));
            //cout << "key " << std::bitset<32>(key) << " len " << words[i].length() << endl;
        }
        int global_max = 0;
        for(int i=0;i<res.size();i++) {
            int local_max = 0;
            for(int j=i+1;j<res.size();j++) {
                if(!(res[i].first & res[j].first)) {
                    //cout << (res[i].first & res[j].first) << endl;
                    local_max = max(local_max,res[i].second*res[j].second);
                }
            }
            global_max = max(global_max,local_max);
        }
        return global_max;
    }
};
```
## Binary Tree Preorder Traversal
### 问题
Given a binary tree, return the preorder traversal of its nodes' values.
```C++
For example:
Given binary tree {1,#,2,3},
   1
    \
     2
    /
   3
return [1,2,3].
```
Note: Recursive solution is trivial, could you do it iteratively?
```C++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
```
### 解答
```C++
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        if(!root)
            return vector<int> ();
        else {
            vector<int> ret;
            stack<TreeNode*> s;
            s.emplace(root);
            while(!s.empty()) {
                TreeNode* node = s.top();
                s.pop();
                ret.push_back(node->val);
                if(node->right)
                    s.push(node->right);
                if(node->left)
                    s.push(node->left);
            }
            return ret;
        }
    }
};
```
## Binary Tree Inorder Traversal
### 问题
Given a binary tree, return the inorder traversal of its nodes' values.
```
For example:
Given binary tree {1,#,2,3},
   1
    \
     2
    /
   3
return [1,3,2].
```
Note: Recursive solution is trivial, could you do it iteratively?
```C++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
```
### 解答
```C++
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        if(!root)
            return vector<int> ();
            
        vector<int> ret;
        stack<TreeNode*> s;
        while(!s.empty() || root != NULL) {
            if(root != NULL) {
                s.push(root);
                root = root->left;
            } else {
                TreeNode* tmp = s.top();
                s.pop();
                ret.push_back(tmp->val);
                root = tmp->right;
            }
        }
        return ret;
        
    }
};
```
## Search Insert Position
### 问题
Given a sorted array and a target value, return the index if the target is found. If not, return the index where it would be if it were inserted in order.

You may assume no duplicates in the array.
```
Here are few examples.
[1,3,5,6], 5 → 2
[1,3,5,6], 2 → 1
[1,3,5,6], 7 → 4
[1,3,5,6], 0 → 0
```
### 解答
```C++
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        for(int i=0;i<nums.size();i++) {
            if(target == nums[i])
                return i;
            if(target < nums[i]) {
                return i;
            }
        }
        return nums.size();
    }
};
```
## Linked List Cycle
### 问题
Given a linked list, determine if it has a cycle in it.

Follow up:
Can you solve it without using extra space?
```C++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
```
### 解答
```C++
class Solution {
public:
    bool hasCycle(ListNode *head) {
        ListNode* fast = head,*low = head;
        while(fast != NULL && fast->next != NULL) {
            low = low -> next;
            fast = fast->next->next;
            if(low == fast)
                return true;
        }
        return false;
    }
};
```
## Find the Duplicate Number
### 问题
Given an array nums containing n + 1 integers where each integer is between 1 and n (inclusive), prove that at least one duplicate number must exist. Assume that there is only one duplicate number, find the duplicate one.

Note:
1. You must not modify the array (assume the array is read only).
2. You must use only constant, O(1) extra space.
3. Your runtime complexity should be less than $O(n^2)$.
4. There is only one duplicate number in the array, but it could be repeated more than once.
### 解答
这道题（据说）花费了计算机科学界的传奇人物Don Knuth 24小时才解出来。并且我只见过一个人（Keith Amling）用更短时间解出此题。

问题的第一部分 - 证明至少存在一个重复元素 - 是鸽笼原理的直接应用。如果元素的范围是[1, n]，那么只存在n种不同的值。如果有n+1个元素，其中一个必然重复。

问题的第二部分 - 在给定约束条件下寻找重复元素 - 可就难多了。 要解决这个问题，我们需要敏锐的洞察力，使问题通过一列的转化，变为一个完全不同的问题。

解决本题需要的主要技巧就是要注意到：由于数组的n + 1个元素范围从1到n，我们可以将数组考虑成一个从集合{1, 2, ..., n}到其本身的函数f。这个函数的定义为f(i) = A[i]。基于这个设定，重复元素对应于一对下标i != j满足 f(i) = f(j)。我们的任务就变成了寻找一对(i, j)。一旦我们找到这个值对，只需通过f(i) = A[i]即可获得重复元素。

但是我们怎样寻找这个重复值呢？这变成了计算机科学界一个广为人知的“环检测”问题。问题的一般形式如下：给定一个函数f，序列x_i的定义为
```
    x_0     = k       (for some k)
    x_1     = f(x_0)
    x_2     = f(f(x_0))
    ...
    x_{n+1} = f(x_n)
```
假设函数f从定义域映射到它本身，此时会有3种情况。首先，如果定义域是无穷的，则序列是无限长并且没有循环的。例如，函数 f(n) = n + 1，在整数范围内满足这个性质 - 没有数字是重复的。 第二， 序列可能是一个闭合循环，这意味着存在一个i使得x_0 = x_i。在这个例子中，序列在一组值内无限循环。第三，序列有可能是的“ρ型的”，此时序列看起来像下面这样：
```
      x_0 -> x_1 -> ... x_k -> x_{k+1} ... -> x_{k+j}
                         ^                       |
                         |                       |
                         +-----------------------+
```
也就是说，序列从一列链条型的元素开始进入一个环，然后无限循环。我们将环的起点称为环的“入口”。

对于从数组中寻找重复元素这个问题，考虑序列从位置n开始重复调用函数f。亦即从数组的最后一个元素开始，然后移动到其元素值对应的下标处，并且重复此过程。可以得到：此序列是ρ型的。要明白这一点，需要注意到其中一定有环，因为数组是有限的并且当访问n个元素时，一定会对某个元素访问两次。无论从数组的哪一个位置开始，这都是成立的。

另外，注意由于数组元素范围1到n，因此不存在值为0的元素。进而，从数组的第一个元素开始调用一次函数f之后，再也不会回到这里。这意味着第一个元素不会是环的一部分，但如果我们继续重复调用函数f，最终总会访问某个节点两次。从0节点开始的链条与环形相接，使得其形状一定是ρ型。

此外，考虑一下环的入口。由于节点位于环的入口，一定存在两个输入，其对应的函数f的输出值都等于入口元素下标。要使其成立，一定存在两个下标i != j，满足f(i) = f(j)，亦即A[i] = A[j]。因而环的入口一定是重复值。

这是由Robert Floyd提出的一个著名算法，给定一个ρ型序列，在线性时间，只使用常数空间寻找环的起点。这个算法经常被称为“龟兔”算法，至于原因下面就明白了。
算法背后的思想是定义两个变量。首先，令c为进入环的链的长度，然后令l为环的长度。接下来，令l'为大于c的l的倍数的最小值。可以得出结论：对于上文定义的任意ρ型序列的l'，都有
```
     x_{l'} = x_{2l'}
```
证明实际上非常直观并且具有自明性 - 这是计算机科学中我最喜欢的证明之一。思路就是由于l'至少为c，它一定包含在环内。同时，由于l'是环长度的倍数，我们可以将其写作ml，其中m为常数。如果我们从位置x_{l'}开始（其在环内），然后再走l'步到达x_{2l'}，则我们恰好绕环m次，正好回到起点。

Floyd算法的一个关键点就是即使我们不明确知道c的值，依然可以在O(l')时间内找到值l'。思路如下。我们追踪两个值"slow"和"fast"，均从x_0开始。然后迭代计算
```
 slow = f(slow)
 fast = f(f(fast))
```
我们重复此步骤直到slow与fast彼此相等。此时，我们可知存在j满足slow = x_j，并且fast = x_{2j}。 由于x_j = x_{2j}，可知j一定至少为c，因为此时已经在环中。另外，可知j一定是l的倍数，因为x_j = x_{2j}意味着在环内再走j步会得到同样的结果。最后，j一定是大于c的l的最小倍数，因为如果存在一个更小的大于c的l的倍数，我们一定会在到达j之前到达那里。所以，我们一定有j = l'，意味着我们可以在不知道环的长度或者形状的情况下找到l'。

要完成整个过程，我们需要明白如何使用l'来找到环的入口（记为x_c）。要做到这一步，我们再用最后一个变量，记为"finder"，从x_0出发。然后迭代重复执行过程：
```
finder = f(finder)
slow   = f(slow)
``` 
直到finder = slow为止。我们可知：(1) 两者一定会相遇 (2) 它们会在环的入口相遇。 要理解这两点，我们注意由于slow位于x_{l'}，如果我们向前走c步，那么slow会到达位置x_{l' + c}。由于l'是环长度的倍数，相当于向前走了c步，然后绕环几圈回到原位。换言之，x_{l' + c} = x_c。另外，考虑finder变量在行进c步之后的位置。 它由x_0出发，因此c步之后会到达x_c。这证明了(1)和(2)，由此我们已经证明两者最终会相遇，并且相遇点就是环的入口。

算法的美妙之处在于它只用O(1)的额外存储空间来记录两个不同的指针 - slow指针和fast指针（第一部分），以及finder指针（第二部分）。但是在此之上，运行时间是O(n)的。要明白这一点，注意slow指针追上fast指针的时间是O(l')。由于l'是大于c的l的最小倍数，有两种情况需要考虑。首先，如果l > c，那么就是l。 否则，如果l < c，那么我们可知一定存在l的倍数介于c与2c之间。要证明这一点，注意在范围c到2c内，有c个不同的值，由于l < c，其中一定有值对l取模运算等于0。最后，寻找环起点的时间为O(c)。这给出了总的运行时间至多为O(c + max{l, 2c})。所有这些值至多为n，因此算法的运行时间复杂度为O(n)。
```C++
class Solution {
public:
    int findDuplicate(vector<int>& nums) {
        int n=nums.size()-1;
        int low=1;
        int high=n;
        int mid;
        while(low<high){
            mid = (low+high) >> 1;
            int count=0;
            for(auto num:nums) {
                if(num<=mid)
                    count++;
            }
            if(count > mid) 
                high=mid;
            else 
                low=mid+1; 
        }
        return low;
    }
};
```
## Linked List Cycle II
### 问题
Given a linked list, return the node where the cycle begins. If there is no cycle, return null.

Note: Do not modify the linked list.

Follow up:
Can you solve it without using extra space?
```C++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
```
### 解答
```C++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        ListNode* fast=  head,*low = head;
        bool isExists = false;
        while(fast != NULL && fast->next != NULL) {
            low = low->next;
            fast = fast->next->next;
            if(fast == low) {
                isExists = true;
                break;
            }
        }
        if(isExists == false)
            return NULL;
        low = head;
        while(fast != low) {
            fast = fast->next;
            low = low->next;
        }
        return fast;
    }
};
```
## House Robber
### 问题
You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed, the only constraint stopping you from robbing each of them is that adjacent houses have security system connected and it will automatically contact the police if two adjacent houses were broken into on the same night.

Given a list of non-negative integers representing the amount of money of each house, determine the maximum amount of money you can rob tonight without alerting the police.
### 解答
```C++
class Solution {
public:
    int rob(vector<int>& nums) {
        if(nums.size() == 0)
            return 0;
        if(nums.size() == 1)
            return nums[0];
        if(nums.size() == 2)
            return max(nums[0],nums[1]);
        vector<int> dp(nums.size(),0);
        dp[0] = nums[0];
        dp[1] = max(nums[0],nums[1]);
        for(int i=2;i<nums.size();i++) {
            dp[i] = max(dp[i-1], dp[i-2] + nums[i]);
        }
        return dp[nums.size()-1];
    }
};
```
## House Robber II
### 问题
After robbing those houses on that street, the thief has found himself a new place for his thievery so that he will not get too much attention. This time, all houses at this place are arranged in a circle. That means the first house is the neighbor of the last one. Meanwhile, the security system for these houses remain the same as for those in the previous street.

Given a list of non-negative integers representing the amount of money of each house, determine the maximum amount of money you can rob tonight without alerting the police.
### 解答
```C++
class Solution {
public:
    int rob(vector<int>& nums) {
        int n = nums.size();
        if(n==0)
            return 0;
        else if(n==1)
            return nums[0];
        else if(n==2)
            return max(nums[0], nums[1]);
        else if(n==3) 
            return max(nums[2], max(nums[1], nums[0]));
        else {
            int result = INT_MIN;
            vector<vector<int>> DP(2,vector<int>(n-1,0));

            for(int i=0; i<2; i++) {
                DP[i][0] = nums[i];
                DP[i][1] = max(nums[i], nums[(i+1)%n]);
                result = max(result, DP[i][0]);
                result = max(result, DP[i][1]);
                for(int j=2; j<n-1; j++) {
                    DP[i][j] = INT_MIN;
                    for(int k=0; k<j-1; k++)
                        DP[i][j] = max(DP[i][j], DP[i][k] + nums[(i+j)%n]);
                    result = max(result, DP[i][j]);
                }
            }

            return result;
        }
    }
};
```
## Kth Smallest Element in a BST
### 问题
Given a binary search tree, write a function kthSmallest to find the kth smallest element in it.

Note: 
You may assume k is always valid, 1 ≤ k ≤ BST's total elements.

Follow up:
What if the BST is modified (insert/delete operations) often and you need to find the kth smallest frequently? How would you optimize the kthSmallest routine?

Hint:

Try to utilize the property of a BST.
What if you could modify the BST node's structure?
The optimal runtime complexity is O(height of BST).
```C++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
```
### 解答
```C++
class Solution {
public:
    int kthSmallest(TreeNode* root, int k) {
        if(k <= 0)
            return -1;
        TreeNode* cur = root;
        stack<TreeNode*> res;
        int count = 1;
        while(true) {
            while(cur) {
                res.push(cur);
                cur = cur->left;
            }
            if(count == k) {
                cur = res.top();
                return cur->val;
            } else {
                cur = res.top();
                res.pop();
                cur = cur->right;
                count++;
            }
        }
    }
};
```
## Populating Next Right Pointers in Each Node
### 问题
Given a binary tree
```C++
struct TreeLinkNode {
  TreeLinkNode *left;
  TreeLinkNode *right;
  TreeLinkNode *next;
}
```
Populate each next pointer to point to its next right node. If there is no next right node, the next pointer should be set to NULL.

Initially, all next pointers are set to NULL.

Note:

You may only use constant extra space.
You may assume that it is a perfect binary tree (ie, all leaves are at the same level, and every parent has two children).
For example,
Given the following perfect binary tree,
```
         1
       /  \
      2    3
     / \  / \
    4  5  6  7
```    
After calling your function, the tree should look like:
```
         1 -> NULL
       /  \
      2 -> 3 -> NULL
     / \  / \
    4->5->6->7 -> NULL
```
```C++
/**
 * Definition for binary tree with next pointer.
 * struct TreeLinkNode {
 *  int val;
 *  TreeLinkNode *left, *right, *next;
 *  TreeLinkNode(int x) : val(x), left(NULL), right(NULL), next(NULL) {}
 * };
 */
```
### 解答
其实就是按照层次遍历，将每一层的节点按照从左到右的顺序插入到队列中去。然后，将每一层的节点排布到相邻的节点去，这其中将最后一个节点的next指针置NULL就好了！
```C++
class Solution {
public:
    void connect(TreeLinkNode *root) {
        while(root) {
            TreeLinkNode* cur = root;
            if(cur->left == NULL) break;
            while(cur) {
                cur->left->next = cur->right;
                if(cur->next) {
                    cur->right->next = cur->next->left;
                }
                cur = cur->next;
            }
            root = root->left;
        }
    }
};
```
## Convert Sorted Array to Binary Search Tree
### 问题
Given an array where elements are sorted in ascending order, convert it to a height balanced BST.
```C++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
```
### 解答
```C++

```
## Pascal's Triangle
### 问题
Given numRows, generate the first numRows of Pascal's triangle.

For example, given numRows = 5,
Return
```C++
[
     [1],
    [1,1],
   [1,2,1],
  [1,3,3,1],
 [1,4,6,4,1]
]
```
### 解答
```C++
class Solution {
public:
    vector<vector<int>> generate(int numRows) {
        vector<vector<int> > data;
        data.clear();
        if(numRows == 0) {
            return data;
        }
        data.resize(numRows);
        for(int i=0;i<numRows;i++) {
            vector<int> tmp(i+1,1);
            data[i] = tmp;
            for(int j=1;j<i && i >= 2;j++) {
                data[i][j] = data[i-1][j-1] + data[i-1][j];
            }
        }
        return data;
    }
};
```
## Factorial Trailing Zeroes
### 问题
Given an integer n, return the number of trailing zeroes in n!.

Note: Your solution should be in logarithmic time complexity.
### 解答
对n!做质因数分解$n!=2^x*3^y*5^z*...$

显然0的个数等于min(x,z)，并且min(x,z)==z

证明：

对于阶乘而言，也就是1*2*3*...*n
[n/k]代表1~n中能被k整除的个数
那么很显然
$[n/2] > [n/5]$ (左边是逢2增1，右边是逢5增1)
$[n/2^2] > [n/5^2]$(左边是逢4增1，右边是逢25增1)
……
$[n/2^p] > [n/5^p]$(左边是逢$2^p$增1，右边是逢$5^p$增1)
随着幂次p的上升，出现$2^p$的概率会远大于出现$5^p$的概率。
因此左边的加和一定大于右边的加和，也就是n!质因数分解中，2的次幂一定大于5的次幂
```C++
class Solution {
public:
    int trailingZeroes(int n) {
        int num;
        for (num = 0; n != 0; n /= 5, num += n);
        return num;
    }
};
```
## Pascal's Triangle II
### 问题
Given an index k, return the kth row of the Pascal's triangle.

For example, given k = 3,
Return [1,3,3,1].
Note:
Could you optimize your algorithm to use only O(k) extra space?
### 解答
对于Pascal中的每一行，是一个多项式的展开形式，但是记住，每一行对应的numRows-1阶的多项式展开！问题就化简为求解$C_n^k,0 \leq k \leq n$,现在只要求解这个表达式的系数就可以了！并且这个是个对称的数列！主要求解一般就可以了！
```C++
class Solution {
public:
    vector<int> getRow(int rowIndex) {
        vector<int> vi; vi.push_back(1);
        if(rowIndex==0) return vi;
        for(int i = 1; i<=rowIndex; i++){
            for(int j = 1, a = vi[0], b; j<vi.size(); j++, a = b){
                b = vi[j];
                vi[j] = a + vi[j];
            }
            vi.push_back(1);
        }
        return vi;
    }
};
```
## Intersection of Two Linked Lists
### 问题
Write a program to find the node at which the intersection of two singly linked lists begins.


For example, the following two linked lists:
```
A:          a1 → a2
                   ↘
                     c1 → c2 → c3
                   ↗            
B:     b1 → b2 → b3
begin to intersect at node c1.
```

Notes:

1. If the two linked lists have no intersection at all, return null.
2. The linked lists must retain their original structure after the function returns.
3. You may assume there are no cycles anywhere in the entire linked structure.
4. Your code should preferably run in O(n) time and use only O(1) memory.
```C++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
```
### 解答
```C++
class Solution {
public:
    int getLength(ListNode* root) {
        int n = 0;
        while(root) {
            n++;
            root = root->next;
        }
        return n;
    }
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        if(headA == NULL || headB == NULL) return NULL;
        int lenA = getLength(headA);
        int lenB = getLength(headB);
        if(lenA < lenB)
            return getIntersectionNode(headB,headA);
        ListNode* tmpA = headA;
        while(lenA > lenB) {
            tmpA = tmpA->next;
            lenA--;
        }
        ListNode* tmpB = headB;
        while(tmpA != tmpB) {
            tmpA = tmpA->next;
            tmpB = tmpB->next;
        }
        return tmpA;
    }
};
```


