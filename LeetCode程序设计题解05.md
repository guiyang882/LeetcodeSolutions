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