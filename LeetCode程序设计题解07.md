##  搜索区间
```C++
class Solution {
    /** 
     *@param A : an integer sorted array
     *@param target :  an integer to be inserted
     *return : a list of length 2, [index1, index2]
     */
public:
    vector<int> searchRange(vector<int> &A, int target) {
        // write your code here
        vector<int> pos = {-1, -1};
        int N = A.size();
        if(N == 0) {
            return pos;
        }
        int l=0, r=N-1, mid;
        while(l<=r) {
            mid = (l + r) / 2;
            if(A[mid] == target) {
                break;
            }
            if(A[mid] < target) {
                l = mid + 1;
            } else {
                r = mid - 1;
            }
        }
        if(A[mid] == target) {
            l = r = mid;
            while(l>=0 && A[l] == target) {
                l--;
            }
            while(r<N && A[r] == target) {
                r++;
            }
            pos[0] = l+1;
            pos[1] = r-1;
        }
        return pos;
    }
};
```
## 转换字符串到整数
实现atoi这个函数，将一个字符串转换为整数。如果没有合法的整数，返回0。如果整数超出了32位整数的范围，返回INT_MAX(2147483647)如果是正整数，或者INT_MIN(-2147483648)如果是负整数。

```C++
class Solution {
public:
    /**
     * @param str: A string
     * @return An integer
     */
    int atoi(string str) {
        // write your code here
        str.erase(0, str.find_first_not_of(" "));
        int N = str.length();
        int isPositive = 0;
        if(N == 0) return 0;
        int res = 0;
        for(int i=0;i<N;i++) {
            if(str[i] == ' ') {
                break;
            }
            if(str[i] == '.') {
                break;
            }
            if(str[i] >= 'a' && str[i] <= 'z') {
                break;
            }
            if(str[i] >= 'A' && str[i] <= 'Z') {
                break;
            }
            if(res == 0) {
                if(str[i] == '+' && isPositive == 0) {
                    isPositive = 1;
                    continue;
                } else if(str[i] == '-' && isPositive == 0) {
                    isPositive = -1;
                    continue;
                } else if(str[i] == '+' || str[i] == '-') {
                    isPositive = -2;
                    break;
                }
            } else {
                if(str[i] == '+' || str[i] == '-') {
                    break;
                }
            }
            
            res = res * 10 + (str[i] - '0');
        }
        
        if(isPositive == -2) {
            return 0;
        }
        
        if(res >= 0) {
            if(isPositive == -1)
                return res * -1;
            return res;
        } else {
            if(isPositive == -1)
                return INT_MIN;
            return INT_MAX;
        }
        
    }
};
```
## 最近公共祖先
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
     * @param root: The root of the binary search tree.
     * @param A and B: two nodes in a Binary.
     * @return: Return the least common ancestor(LCA) of the two nodes.
     */
    void helper(vector<TreeNode*>& path, TreeNode* root, TreeNode* A, TreeNode* B) {
        path.push_back(root);
        
        if(root->val == A->val) {
            APath.assign(path.begin(), path.end());
            //show(APath);
        }
        if(root->val == B->val) {
            BPath.assign(path.begin(), path.end());
            //show(BPath);
        }
        
        if(root->left) {
            helper(path, root->left, A, B);
            path.pop_back();
        }
        if(root->right) {
            helper(path, root->right, A, B);
            path.pop_back();
        }
    }
    
    TreeNode *lowestCommonAncestor(TreeNode *root, TreeNode *A, TreeNode *B) {
        // write your code here
        vector<TreeNode*> path;
        helper(path, root, A, B);
        TreeNode* res = NULL;
        for(int i=0;i<min(APath.size(), BPath.size());i++) {
            if(APath[i] == BPath[i]) {
                res = APath[i];
            } else {
                break;
            }
        }
        return res;
    }
    
    void show(vector<TreeNode*> tmp) {
        cout << endl;
        for(auto a:tmp) {
            cout << a->val << " ";
        }
        cout << endl;
    }
private:
    vector<TreeNode*> APath;
    vector<TreeNode*> BPath;
};
```
## 在线数据流中位数
```C++
class Solution {
public:
    /**
     * @param nums: A list of integers.
     * @return: The median of numbers
     */
    vector<int> medianII(vector<int> &nums) {
        // write your code here
        vector<int> res;
        for(int i=0;i<nums.size();i++) {
            insert(nums[i]);
            int tmp = getMedian();
            res.push_back(tmp);
        }
        return res;
    }
    
    void insert(int val) {
        int N = minRear.size() + maxPrefix.size();
        if(N % 2 == 1) { // 如果现在数据是奇数个数据，添加数据后是偶数个数据，将新数据添加到后半段的数据中
            if(maxPrefix.size() > 0 && val < maxPrefix[0]) {
                maxPrefix.push_back(val);
                push_heap(maxPrefix.begin(), maxPrefix.end(), less<int>());
                val = maxPrefix[0];
                pop_heap(maxPrefix.begin(), maxPrefix.end(), less<int>());
                maxPrefix.pop_back();
            }
            minRear.push_back(val);
            push_heap(minRear.begin(), minRear.end(), greater<int>());
        } else { // 如果出现数据整体是偶数个数据，添加数据后是奇数个数据，将新数据添加到前半段的数据中去
            if(minRear.size() > 0 && val > minRear[0]) {
                minRear.push_back(val);
                push_heap(minRear.begin(), minRear.end(), greater<int>());
                val = minRear[0];
                pop_heap(minRear.begin(), minRear.end(), greater<int>());
                minRear.pop_back();
            }
            maxPrefix.push_back(val);
            push_heap(maxPrefix.begin(), maxPrefix.end(), less<int>());
        }
    }
    
    int getMedian() {
        int N = minRear.size() + maxPrefix.size();
        if(N == 0) return -1;
        return maxPrefix[0];
        if(N % 2 == 1) {
            return maxPrefix[0];
        } else {
            return (maxPrefix[0] + minRear[0]) / 2;
        }
    }

private:
    vector<int> minRear; // 数组后半段的数据建立了一个最小堆
    vector<int> maxPrefix; // 数组前半段的数据建立了一个最大堆
};
```

## 滑动窗口最大值
### Solution 01
```C++
class Solution {
public:
    /**
     * @param nums: A list of integers.
     * @return: The maximum number inside the window at each moving.
     */
    typedef pair<int, int> Pair;
    
    vector<int> maxSlidingWindow(vector<int> &nums, int k) {
        // write your code here
        vector<int> res;
        
        priority_queue<Pair> MaxHeap;
        for(int i=0;i<k-1;i++) {
            MaxHeap.push(Pair(nums[i], i));
        }
        
        for(int i=k-1;i<nums.size();i++) {
            MaxHeap.push(Pair(nums[i], i));
            Pair p = MaxHeap.top();
            while(p.second < i - (k-1)) {
                MaxHeap.pop();
                p = MaxHeap.top();
            }
            res.push_back(p.first);
        }
        return res;
    }
};
```
### Solution 02
```C++
class Solution {
public:
    /**
     * @param nums: A list of integers.
     * @return: The maximum number inside the window at each moving.
     */
    vector<int> maxSlidingWindow(vector<int> &nums, int k) {
        // write your code here
        vector<int> res;
        deque<int> Q;
        if(nums.size() <= 0 || k <= 0) return res;
        for(int i=0;i<k;i++) {
            while(!Q.empty() && nums[i] > nums[Q.back()]) Q.pop_back();
            Q.push_back(i);
        }
        for(int i=k;i<nums.size();i++) {
            res.push_back(nums[Q.front()]);
            while(!Q.empty() && nums[i] >= nums[Q.back()]) Q.pop_back();
            while(!Q.empty() && Q.front() <= i - k) Q.pop_front();
            Q.push_back(i);
        }
        res.push_back(nums[Q.front()]);
        return res;
    }
};
```
