# Leetcode算法设计题解(五)

## 求取一个给定的数字的第几位是什么？
```c++
/*
	@param k: from 1 to N;
	@param val: val>=0
 */
int findKthDigit(int val, int k) {
	string str = to_string(val);
	int len = str.length();
	if(len < k || k <= 0) return -1;
	return str[len-k] + '0';
}

int findKthDigit(int val, int k) {
	if(k <= 0) return -1;
	if(val <= 0) return -1;
	if(val % (int)pow(10, k) < (int)pow(10, k-1)) return -1;
	return val % (int)pow(10, k) / (int)pow(10,k-1);
}
```

## 给定一个数组，求[数组中第三大的元素](https://leetcode.com/problems/third-maximum-number/)
```C++
class Solution {
public:
    int thirdMax(vector<int>& nums) {
        int len = nums.size();
        if(len == 0) return INT_MIN;
        if(len == 1) return nums[0];
        if(len == 2) return max(nums[0], nums[1]);
        //去除重复的元素
        sort(nums.begin(), nums.end());
        vector<int>::iterator it = unique(nums.begin(), nums.end());
        nums.resize(distance(nums.begin(), it));
        if(nums.size() < 3) {
            return *(nums.end()-1);
        }
        return *(nums.end()-3);
    }
};
```

```C++
class Solution {
public:
    int thirdMax(vector<int>& nums) {
        set<int> top3;
        for (int num : nums) {
            top3.insert(num);
            if (top3.size() > 3)
                top3.erase(top3.begin());
        }
        return top3.size() == 3 ? *top3.begin() : *top3.rbegin();
    }
};
```

## 给定一棵二叉树，进行深度克隆
```C++
/**
 * Definition of TreeNode:
 * class TreeNode {
 * public:
 *     int val;
 *     TreeNode *left, *right;
 *     TreeNode(int val) {
 *         this->val = val;
 *         this->left = this->right = NULL;
 *     }
 * }
 */
class Solution {
public:
    /**
     * @param root: The root of binary tree
     * @return root of new tree
     */
    
    void helper(TreeNode* src, TreeNode** dest) {
        if(src == NULL) return ;
        TreeNode* tmp = new TreeNode(-1);
        tmp->val = src->val;
        *dest = tmp;
        if(src->left) {
            helper(src->left, &((*dest)->left));
        }
        if(src->right) {
            helper(src->right, &((*dest)->right));
        }
    }
    
    TreeNode* cloneTree(TreeNode *root) {
        if(root == NULL) return NULL;
        TreeNode* head = new TreeNode(-1);
        head->val = root->val;
        if(root->left) {
            helper(root->left, &(head->left));
        }
        if(root->right) {
            helper(root->right, &(head->right));
        }
        return head;
    }
};
```
##  不同的二叉查找树 II
给出n，生成所有由1...n为节点组成的不同的二叉查找树
给出n = 3，生成所有5种不同形态的二叉查找树：
```
1         3     3       2    1
 \       /     /       / \    \
   3     2     1       1   3    2
    /     /       \                \
    2     1         2                3
```
```C++
/**
 * Definition of TreeNode:
 * class TreeNode {
 * public:
 *     int val;
 *     TreeNode *left, *right;
 *     TreeNode(int val) {
 *         this->val = val;
 *         this->left = this->right = NULL;
 *     }
 * }
 */
class Solution {
public:
    /**
     * @paramn n: An integer
     * @return: A list of root
     */
    vector<TreeNode* > createTrees(int start, int end) {
        vector<TreeNode*> res;
        if(start > end) {
            res.push_back(NULL);
            return res;
        }
        
        if(start == end) {
            res.push_back(new TreeNode(start));
            return res;
        }
        
        for(int i=start;i<=end;i++) {
            vector<TreeNode*> left = createTrees(start, i-1);
            vector<TreeNode*> right = createTrees(i+1, end);
            for(auto l:left) {
                for(auto r:right) {
                    TreeNode* head = new TreeNode(i);
                    head->left = l;
                    head->right = r;
                    res.push_back(head);
                }
            }
        }
        return res;
    }
    
    vector<TreeNode *> generateTrees(int n) {
        // write your code here
        vector<TreeNode*> res = createTrees(1, n);
        return res;
    }
};
```
## 实现字典树并实现查找
```C++
/**
 * Your Trie object will be instantiated and called as such:
 * Trie trie;
 * trie.insert("lintcode");
 * trie.search("lint"); will return false
 * trie.startsWith("lint"); will return true
 */
class TrieNode {
public:
    // Initialize your data structure here.
    TrieNode() {
        isWord = false;
        for(auto &a : alpha) {
            a = NULL;
        }
    }
    
public:
    TrieNode* alpha[26];
    bool isWord;
};

class Trie {
public:
    Trie() {
        root = new TrieNode();
    }

    // Inserts a word into the trie.
    void insert(string word) {
        TrieNode* p = root;
        for(auto c:word) {
            if(p->alpha[c-'a'] == NULL) {
                p->alpha[c-'a'] = new TrieNode();
            }
            p = p->alpha[c-'a'];
        }
        p->isWord = true;
    }

    // Returns if the word is in the trie.
    bool search(string word) {
        TrieNode* p = root;
        for(auto c:word) {
            if(p && p->alpha[c-'a']) {
                p = p->alpha[c-'a'];
            } else {
                return false;
            }
        }
        return p->isWord;
    }

    // Returns if there is any word in the trie
    // that starts with the given prefix.
    bool startsWith(string prefix) {
        TrieNode* p = root;
        for(auto c:prefix) {
            if(p && p->alpha[c-'a']) {
                p = p->alpha[c-'a'];
            } else {
                return false;
            }
        }
        return true;
    }

private:
    TrieNode* root;
};
```

## 正则表达式和前缀树搜索
```C++
class TrieNode {
public:
    // Initialize your data structure here.
    TrieNode() {
        isWord = false;
        for(auto &a : alpha) {
            a = NULL;
        }
    }
    
public:
    TrieNode* alpha[26];
    bool isWord;
};

class WordDictionary {
public:
    WordDictionary() {
        root = new TrieNode();
    }
    // Adds a word into the data structure.
    void addWord(string word) {
        // Write your code here
        TrieNode* p = root;
        for(auto c:word) {
            if(p->alpha[c-'a'] == NULL) {
                p->alpha[c-'a'] = new TrieNode();
            }
            p = p->alpha[c-'a'];
        }
        p->isWord = true;
    }

    // Returns if the word is in the data structure. A word could
    // contain the dot character '.' to represent any one letter.
    bool search(string word) {
        // Write your code here
        return search(word, root, 0);
    }
    
    bool search(string &word, TrieNode* node, int pos) {
        if(pos == word.size()) return node->isWord;
        if(word[pos] == '.') {
            for(auto r:node->alpha) {
                if(r && search(word, r, pos+1)) return true;
            }
            return false;
        } else {
            return node->alpha[word[pos]-'a'] && search(word, node->alpha[word[pos]-'a'], pos+1);
        }
        return false;
    }
    
private:
    TrieNode* root;
};

// Your WordDictionary object will be instantiated and called as such:
// WordDictionary wordDictionary;
// wordDictionary.addWord("word");
// wordDictionary.search("pattern");
```

## 使用背包问题求解
在n个物品中挑选若干物品装入背包，最多能装多满？假设背包的大小为m，每个物品的大小为A[i]
```C++
class Solution {
public:
    /**
     * @param m: An integer m denotes the size of a backpack
     * @param A: Given n items with size A[i]
     * @return: The maximum size
     */
    
    void dfs(int m, int residue, const vector<int>& A, vector<bool>& selected, 
    int &maxSum, int start) {
        if(residue >= 0) {
            maxSum = max(maxSum, m - residue);
        }
        for(int i=start;i<A.size();i++) {
            if(selected[i] == false && A[i] <= residue) {
                selected[i] = true;
                dfs(m, residue - A[i], A, selected, maxSum, i+1);
                selected[i] = false;
            }
        }
    }
    
    int backPack(int m, vector<int> A) {
        // write your code here
        vector<bool> selected(A.size(), false);
        int maxSum = 0;
        dfs(m, m, A, selected, maxSum, 0);
        return maxSum;
    }
};
```
- 这段代码是使用的思路就是暴力求解，进行深度优先遍历，遍历所有解空间，那必定会超时

```C++
class Solution {
public:
    /**
     * @param m: An integer m denotes the size of a backpack
     * @param A: Given n items with size A[i]
     * @return: The maximum size
     */
    
    void dfs(int m, int residue, const vector<int>& A, vector<bool>& selected, 
    int &maxSum, int start) {
        if(residue >= 0) {
            maxSum = max(maxSum, m - residue);
        }
        for(int i=start;i<A.size();i++) {
            if(selected[i] == false && A[i] <= residue) {
                selected[i] = true;
                dfs(m, residue - A[i], A, selected, maxSum, i+1);
                selected[i] = false;
            }
        }
    }
    
    int backPack(int m, vector<int> A) {
        // write your code here
        //vector<bool> selected(A.size(), false);
        int maxSum = 0;
        //dfs(m, m, A, selected, maxSum, 0);
        /*
        dp[i,j] : 前i个物品放入容量为j的背包中的最大重量是
        dp[i,j] = max(dp[i-1,j-A[i]] + A[i], dp[i-1, j])
        */
        int N = A.size();
        vector<vector<int>> dp(N+1, vector<int>(m+1,0));
        for(int i=1;i<=N;i++) {
            for(int j=1;j<=m;j++) {
                if(j - A[i-1] >= 0)
                    dp[i][j] = max(dp[i-1][j-A[i-1]] + A[i-1], dp[i-1][j]);
                else
                    dp[i][j] = max(dp[i][j], dp[i-1][j]);
            }
        }
        maxSum = dp[N][m];
        return maxSum;
    }
};
```

- 将上述问题转化为二维动态规划问题，但是在最后却只通过了80%的测试用例，还要继续优化！

```C++
class Solution {
public:
    /**
     * @param m: An integer m denotes the size of a backpack
     * @param A: Given n items with size A[i]
     * @return: The maximum size
     */
    
    void dfs(int m, int residue, const vector<int>& A, vector<bool>& selected, 
    int &maxSum, int start) {
        if(residue >= 0) {
            maxSum = max(maxSum, m - residue);
        }
        for(int i=start;i<A.size();i++) {
            if(selected[i] == false && A[i] <= residue) {
                selected[i] = true;
                dfs(m, residue - A[i], A, selected, maxSum, i+1);
                selected[i] = false;
            }
        }
    }
    
    int backPack(int m, vector<int> A) {
        // write your code here
        //vector<bool> selected(A.size(), false);
        int maxSum = 0;
        //dfs(m, m, A, selected, maxSum, 0);
        /*
        dp[i,j] : 前i个物品放入容量为j的背包中的最大重量是
        dp[i,j] = max(dp[i-1,j-A[i]] + A[i], dp[i-1, j])
        */
        /*
        对上述的动态规划方程进行优化
        dp[i]仅仅和dp[i-1]的状态相关
        且，每次在迭代时，需要从m开始遍历，这会将更加便于操作
        */
        int N = A.size();
        vector<int> dp(m+1, 0);
        for(int i=0;i<N;i++) {
            for(int j=m;j>=0;j--) {
                if(j - A[i] >= 0)
                    dp[j] = max(dp[j-A[i]] + A[i], dp[j]);
            }
        }
        maxSum = dp[m];
        return maxSum;
    }
};
```

## 背包问题II
给出n个物品的体积A[i]和其价值V[i]，将他们装入一个大小为m的背包，最多能装入的总价值有多大？

```C++
class Solution {
public:
    /**
     * @param m: An integer m denotes the size of a backpack
     * @param A & V: Given n items with size A[i] and value V[i]
     * @return: The maximum value
     */
    /*
    dp[i,j] : 存储当前i个物体存储到容量问j的包中，所最大的价值
    dp[i,j] = {dp[i-1, j-A[i]] + val[i], dp[i-1,j]}
    */
    int backPackIII(int m, vector<int> A, vector<int> V) {
        int N = A.size();
        vector<int> dp(m+1, 0);
        for(int i=0;i<N;i++) {
            for(int j=m;j>=0;j--) {
                if(j - A[i] >= 0) {
                    dp[j] = max(dp[j], dp[j-A[i]] + V[i]);
                }
            }
        }
        return dp[m];
    }
    
    int backPackII(int m, vector<int> A, vector<int> V) {
        // write your code here
        return backPackIII(m, A, V);
        
        int N = A.size();
        vector<vector<int>> dp(N+1, vector<int>(m+1, 0));
        for(int i=1;i<=N;i++) {
            for(int j=m;j>=0;j--) {
                if(j - A[i-1] >= 0) {
                    dp[i][j] = max(dp[i-1][j], dp[i-1][j-A[i-1]] + V[i-1]);
                }
                dp[i][j] = max(dp[i][j], dp[i-1][j]);
            }
        }
        return dp[N][m];
    }
};
```

##  最长公共子序列
给出两个字符串，找到最长公共子序列(LCS)，返回LCS的长度。
```C++
class Solution {
public:
    /**
     * @param A, B: Two strings.
     * @return: The length of longest common subsequence of A and B.
     */
     
    /*
    动态规划
    dp[i,j] : A[i], B[j]的最长公共子序列的长度
    dp[i,j] = {
        dp[i-1,j-1] + 1, if(A[i] == B[j])
        max(dp[i-1,j], dp[i,j-1]), if(A[i] != B[j])
    }
    */
    int longestCommonSubsequence(string A, string B) {
        // write your code here
        int res = 0;
        int lA = A.size(), lB = B.size();
        vector<vector<int> > dp(lA+1, vector<int>(lB+1, 0));
        for(int i=1;i<=lA;i++) {
            for(int j=1;j<=lB;j++) {
                if(A[i-1] == B[j-1]) {
                    dp[i][j] = dp[i-1][j-1] + 1;
                } else {
                    dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
                }
            }
        }
        res = dp[lA][lB];
        return res;
    }
};
```

## 背包问题VI
Given an integer array nums with all positive numbers and no duplicates, find the number of possible combinations that add up to a positive integer target.

```C++
class Solution {
public:
    /**
     * @param nums an integer array and all positive numbers, no duplicates
     * @param target an integer
     * @return an integer
     */
    void helper(vector<int> &nums, int residue, int &cnt, vector<int> &cur) {
        if(residue == 0) {
            if(table.count(cur) == 0) {
                cnt++;
                table.insert(cur);
            }
        } else {
            for(int i=0;i<nums.size();i++) {
                if(residue - nums[i] >= 0) {
                    cur.push_back(nums[i]);
                    helper(nums, residue - nums[i], cnt, cur);
                    cur.pop_back();
                }
            }
        }
    }
    
    /*
    dp[i] : 含义是当容量为i时，有多少种装包的方法
    dp[i] = {
        if(cur_obj <= i) {
            dp[i] += dp[i-cur_obj];
        }
    }
    */
    int helper02(vector<int>& nums, int target) {
        vector<int> dp(target+1, 0);
        dp[0] = 1;
        for(int i=1;i<=target;i++) {
            for(auto a:nums) {
                if(i>=a) {
                    dp[i] += dp[i-a];
                }
            }
        }
        return dp[target];
    }
    
    int backPackVI(vector<int>& nums, int target) {
        // Write your code here
        int N = nums.size();
        if(N == 0) return 0;
        if(N == 1 && nums[0] != target) return 0;
        int cnt = 0;
        //vector<int> cur;
        //helper(nums, target, cnt, cur);
        cnt = helper02(nums, target);
        return cnt;
    }
    
private:
    set<vector<int>> table;
};
```

## 交错正负数
给出一个含有正整数和负整数的数组，重新排列成一个正负数交错的数组。
```C++
class Solution {
public:
    /**
     * @param A: An integer array.
     * @return: void
     */
    void rerange(vector<int> &A) {
        // write your code here
        int N = A.size();
        int r = 1, l = N-1;
        
        while(r<N) {
            while(r<N && A[r] * A[r-1] < 0) {
                r++;
            }
            int l = r;
            if(A[r] > 0) {
                while(l<N && A[l]>0) l++;
            } else {
                while(l<N && A[l]<0) l++;
            }
            if(l<N) {
                swap(A[r], A[l]);
            }
            if(A[r] * A[r-1] > 0) {
                A.insert(A.begin(), A[N-1]);
                A.pop_back();
                break;
            }
        }
    }
};
```