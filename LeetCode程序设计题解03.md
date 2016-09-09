# LeetCode算法设计题解(三)

@(程序设计算法)

标签： 算法设计题解

----------

## Valid Sudoku
### 问题
Determine if a Sudoku is valid, according to: Sudoku Puzzles - The Rules.

The Sudoku board could be partially filled, where empty cells are filled with the character '.'.
![数独图片插图][1]
A partially filled sudoku which is valid.

Note:
A valid Sudoku board (partially filled) is not necessarily solvable. Only the filled cells need to be validated.
### 解答
这个问题仅仅需要检测，数独中的数据填写是否符合规则，并不检测是否满足横竖斜的和的规则！通过已知给定的数组，我们就可以检测这个数独是否合法！
```C++
class Solution {
public:
    bool isValidSudoku(vector<vector<char>>& board) {
        int rowValid[10] = {0};
        int colValid[10] = {0};
        int subBoard[10] = {0};
        for(int i=0;i<9;i++) {
            memset(rowValid,0,sizeof(rowValid));
            memset(colValid,0,sizeof(colValid));
            memset(subBoard,0,sizeof(subBoard));
            for(int j=0;j<9;j++) {
                bool f1 = checkValid(rowValid,board[i][j] - '0');
                bool f2 = checkValid(colValid,board[j][i] - '0');
                bool f3 = checkValid(subBoard,board[3*(i/3) + j/3][3*(i%3) + j%3] - '0');
                if(!(f1 && f2 && f3))
                    return false;
            }
        }
        return true;
    }
    
    bool checkValid(int res[],int val) {
        if(val < 0)
            return true;
        if(res[val])
            return false;
        res[val] = 1;
        return true;
    }
};
```
## Rectangle Area
### 问题
Find the total area covered by two rectilinear rectangles in a 2D plane.

Each rectangle is defined by its bottom left corner and top right corner as shown in the figure.
![相交的矩形示意图][2]
Assume that the total area is never beyond the maximum possible value of int.
### 解答
```C++
class Solution {
public:
    int computeArea(int A, int B, int C, int D, int E, int F, int G, int H) {
        int l1 = C - A;
        int w1 = D - B;

        int l2 = G - E;
        int w2 = H - F;

        int ret = l1 * w1 + l2 * w2;

        if (G <= A || C <= E || H <= B || D <= F) //no overlap
            return ret;

        int l3 = min(C, G) - max(A, E);
        int w3 = min(D, H) - max(B, F);
        int overlap = l3 * w3;

        return ret - overlap;
    }
};
```
## Contains Duplicate II
### 问题
Given an array of integers and an integer k, find out whether there are two distinct indices i and j in the array such that $nums[i] = nums[j]$ and the difference between i and j is at most k.
### 解答
这个是暴力的求解方案，果不出意料，这个超时了！
```C++
class Solution {
public:
    bool containsNearbyDuplicate(vector<int>& nums, int k) {
        if(nums.size() < k)
            return false;
        for(int i=0;i<nums.size()-k;i++) {
            for(int j=i+k;j<nums.size();j++) {
                if(nums[i] == nums[j])
                    return true;
            }
        }
        return false;
    }
};
```
我们可以利用一个哈希表进行存储数据，将数据存储到某个数组中，利用数值信息当成键值，将索引值存储到指定的信息中去，这样就可以在线性时间内进行数据的比较！
```C++
class Solution {
public:
    bool containsNearbyDuplicate(vector<int>& nums, int k) {
        map<int,int>::iterator it;
        for(int i=0;i<nums.size();i++) {
            it = hashtable.find(nums[i]);
            if(it == hashtable.end())
                hashtable[nums[i]] = i;
            else {
                if(i - it->second <= k)
                    return true;
                else
                    hashtable[nums[i]] = i;
            }
        }
        return false;
    }
    map<int,int> hashtable;
};
```
## Contains Duplicate III
### 问题
Given an array of integers, find out whether there are two distinct indices i and j in the array such that the difference between $nums[i]$ and $nums[j]$ is at most t and the difference between i and j is at most k.
### 解答
本题的题意是在下标相距至多为k的窗口内，找到数值相差不超过t的对，如果存在返回true，如果不存在返回false！
```C++
class Solution {
public:
    bool containsNearbyAlmostDuplicate(vector<int>& nums, int k, int t) {
         if(nums.size() < 2) return false;
         vector<pair<long,int>> v;
         for(int i = 0; i < nums.size(); i++)
             v.push_back(pair<long,int>((long)nums[i],i));
         
         sort(v.begin(),v.end(),cmp);
         
         for(int i = 0; i < nums.size(); i++) {
             int j = i + 1;
             while(j < v.size()) {
                 if(v[j].first-v[i].first > t)
                    break;
                 else if(abs(v[i].second-v[j].second) <= k) {
                     return true;
                 } else j++;
             }
         }
         return false;
    }

    static bool cmp(pair<long,int> a, pair<long,int> b){
         return a.first < b.first;
    }
};
```
## Length of Last Word
### 问题
Given a string s consists of upper/lower-case alphabets and empty space characters ' ', return the length of last word in the string.

If the last word does not exist, return 0.

Note: A word is defined as a character sequence consists of non-space characters only.

For example, 
```
Given s = "Hello World",
return 5.
```
### 解答
```C++
class Solution {
public:
    int lengthOfLastWord(string s) {
        int len = s.length();
        int n = 0,index=len-1;
        while(s[index] == ' ') index--;
        for(int i=index;i>=0;i--) {
            if(s[i] == ' ')
                break;
            n++;
        }
        return n;
    }
};
```
## Remove Nth Node From End of List
### 问题
Given a linked list, remove the $n^{th}$ node from the end of list and return its head.

For example,
```
Given linked list: 1->2->3->4->5, and n = 2.
After removing the second node from the end, the linked list becomes 1->2->3->5.
```
Note:
Given n will always be valid.
Try to do this in one pass.
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
    int getLength(ListNode* head) {
        int n=0;
        while(head) {
            n++;
            head = head->next;
        }
        return n;
    }
    
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        if(head == NULL) return head;
        if(n<=0) return head;
        int len = getLength(head);
        if(len == n) return head->next;
        
        ListNode* p1 = head;
        do {
            n--;
            if(p1 == NULL)  return head;
            p1 = p1->next;
        }while(n != 0);
        ListNode* p2 = head,*pre = head;
        while(p1) {
            p1 = p1->next;
            if(p2 != pre) {
                pre = p2;
            }
            p2 = p2->next;
        }
        if(p2)
            pre->next = p2->next;
        else
            pre->next = NULL;
        return head;
    }
};
```
## Valid Parentheses
### 问题
Given a string containing just the characters '(', ')', '{', '}', '[' and ']', determine if the input string is valid.

The brackets must close in the correct order, "()" and "()[]{}" are all valid but "(]" and "([)]" are not.
### 解答
```C++
class Solution {
public:
    bool isValid(string s) {
        stack<char> res;
        for(int i=0;i<s.length();i++) {
            if(res.empty()){
                res.push(s[i]);
            } else {
                if(isPair(res.top(),s[i])) res.pop();
                else res.push(s[i]);
            }
        }
        return res.empty();
    }
    
    bool isPair(char s,char d) {
        if((s == '(' && d == ')') || (s == ')' && d == '(')) return true;
        if((s == '[' && d == ']') || (s == ']' && d == '[')) return true;
        if((s == '{' && d == '}') || (s == '}' && d == '{')) return true;
        return false;
    }
};
```
## Isomorphic Strings
### 问题
Given two strings s and t, determine if they are isomorphic.

Two strings are isomorphic if the characters in s can be replaced to get t.

All occurrences of a character must be replaced with another character while preserving the order of characters. No two characters may map to the same character but a character may map to itself.

For example,
```
Given "egg", "add", return true.
Given "foo", "bar", return false.
Given "paper", "title", return true.
```
Note:
You may assume both s and t have the same length.
### 解答
```C++
class Solution {
public:
    bool isIsomorphic(string s, string t) {
        // if s[i]->t[i], then map s[i] and t[i] to the same integer;
        // if s[i] is not mapped, but t[i]!=0, then it's not a one-to-one mapping, return false;

        int map_s[256] = {0};
        int map_t[256] = {0};

        for(int i=0; i<s.size(); i++) {
            if(map_s[ s[i] ] && map_s[ s[i] ] == map_t[ t[i] ])
                continue;

            if(!map_s[ s[i] ] && !map_t[ t[i] ]) {
                map_s[ s[i] ] = i+1;
                map_t[ t[i] ] = i+1;
                continue;
            }

            return false;
        }

        return true;
    }
};
```
## Remove Linked List Elements
### 问题
Remove all elements from a linked list of integers that have value val.

Example
```
Given: 1 --> 2 --> 6 --> 3 --> 4 --> 5 --> 6, val = 6
Return: 1 --> 2 --> 3 --> 4 --> 5
```
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
```C++
class Solution {
public:
    ListNode* removeElements(ListNode* head, int val) {
        ListNode* pre = head,*cur = head;
        while(cur) {
            if(cur->val == val) {
                if(pre == cur && pre == head) 
                    pre = pre->next,cur = cur->next,head = head->next;
                else 
                    pre->next = cur->next,cur = cur->next;
            } else {
                pre = cur;
                cur = cur->next;
            }
        }
        return head;
    }
};
```
## Binary Tree Paths
### 解答
Given a binary tree, return all root-to-leaf paths.

For example, given the following binary tree:
```
   1
 /   \
2     3
 \
  5
```
All root-to-leaf paths are:
```
["1->2->5", "1->3"]
```
```
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
### 问题
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
class Solution {
public:
    void binaryTreePathsHelper(TreeNode* root, string& s, vector<string>& result) {
        if(root==NULL)
            return;
        int length = s.length();
        string temp1, temp2;
        temp1 = s;
        if(length > 0)
            s += "->";
        s += to_string(root->val);

        if(root->left == NULL && root->right==NULL)
            result.push_back(s);

        temp2 = s;
        binaryTreePathsHelper(root->left, s, result);
        s = temp2;
        binaryTreePathsHelper(root->right, s, result);
        s = temp1;
    }
    
    vector<string> binaryTreePaths(TreeNode* root) {
        string s;
        vector<string> result;
        binaryTreePathsHelper(root, s, result);
        return result;
    }
};
```



  [1]: http://upload.wikimedia.org/wikipedia/commons/thumb/f/ff/Sudoku-by-L2G-20050714.svg/250px-Sudoku-by-L2G-20050714.svg.png "数独图片"
  [2]: https://leetcode.com/static/images/problemset/rectangle_area.png "相交的矩形"
