## 重哈希
```C++
/**
 * Definition of ListNode
 * class ListNode {
 * public:
 *     int val;
 *     ListNode *next;
 *     ListNode(int val) {
 *         this->val = val;
 *         this->next = NULL;
 *     }
 * }
 */
class Solution {
public:
    /**
     * @param hashTable: A list of The first node of linked list
     * @return: A list of The first node of linked list which have twice size
     */
    void rebuildHash(vector<ListNode*> &hashTable, int val) {
        int N = hashTable.size();
        int pos = (val % N + N) % N;
        if(hashTable[pos] == NULL) {
            hashTable[pos] = new ListNode(val);
        } else {
            ListNode* tmp = hashTable[pos];
            while(tmp->next) {
                tmp = tmp->next;
            }
            tmp->next = new ListNode(val);
        }
    }
    
    vector<ListNode*> rehashing(vector<ListNode*> hashTable) {
        // write your code here
        int N = hashTable.size();
        vector<ListNode*> ret;
        if(N == 0) return ret;
        ret.resize(N * 2);
        for(int i=0;i<N;i++) {
            if(hashTable[i] == NULL)
                continue;
            ListNode* start = hashTable[i];
            while(start) {
                rebuildHash(ret, start->val);
                start = start->next;
            }
        }
        return ret;
    }
};
```
## 回文链表
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
    /**
     * @param head a ListNode
     * @return a boolean
     */
    int getLen(ListNode* head) {
        ListNode* tmp = head;
        int N = 0;
        while(tmp) {
            N++;
            tmp = tmp->next;
        }
        return N;
    }
    bool isPalindrome(ListNode* head) {
        // Write your code here
        int N = getLen(head);
        if(N <= 1) return true;
        int l = 1;
        stack<int> prepart;
        ListNode* tmp = head;
        while(l <= (N+1) / 2) {
            if(N % 2 == 1 && l == (N+1)/2) {
                tmp = tmp->next;
                break;
            }
            prepart.push(tmp->val);
            tmp = tmp->next;
            l++;
        }
        while(tmp) {
            int topVal= tmp->val;
            int lastVal = prepart.top();
            if(topVal == lastVal) {
                tmp = tmp -> next;
                prepart.pop();
            } else {
                return false;
            }
        }
        return true;
    }
};
```

## 格雷码
```C++
class Solution {
public:
    /**
     * @param n a number
     * @return Gray code
     */
    vector<int> grayCode(int n) {
        // Write your code here
        vector<int> res;
        if(n == 0) {
            res.push_back(0);
            return res;
        }
        if(n == 1) {
            res.push_back(0);
            res.push_back(1);
            return res;
        }
        int nSize = 1 << n;
        for(int i=0;i<nSize;i++) {
            int grayCode = i ^ (i >> 1);
            res.push_back(grayCode);
        }
        return res;
    }
};
```
## 堆化
```C++
class Solution {
public:
    /**
     * @param A: Given an integer array
     * @return: void
     */
    void helper(vector<int>& A, int i, int N) {
        int nChild = i;
        for(;2*i+1 < N;i = nChild) {
            nChild = 2 * i + 1;
            if(nChild < N-1 && A[nChild + 1] > A[nChild]) nChild++;
            if(A[i] < A[nChild]) {
                swap(A[i], A[nChild]);
            } else {
                break;
            }
        }
    }
    
    void heapify(vector<int> &A) {
        // write your code here
        int N = A.size();
        for(int i=N/2-1;i>=0;i--) {
            helper(A, i, N);
        }
        for(int i=N-1;i>0;i--) {
            swap(A[i], A[0]);
            helper(A, 0, i);
        }
    }
};
```

## IPO
```C++
bool cmp1(const pair<int,int> &a, const pair<int,int> &b) {
    return a.second > b.second;
}

class Solution {
public:
    void show(const vector<pair<int,int>>& vals) {
        for(auto a:vals) {
            cout << a.second << " ";
        }
        cout << endl;
    }
    
    int findMaximizedCapital(int k, int W, vector<int>& Profits, vector<int>& Capital) {
        vector<pair<int,int>> hash;
        for(int i=0;i<Capital.size();i++) {
            // hash.push_back(pair<int, int>(Capital[i], Capital[i]+Profits[i]));
            hash.push_back(pair<int, int>(Capital[i], Profits[i]));
        }
        sort(hash.begin(), hash.end(), cmp1);
        vector<bool> status(Capital.size(),true);
        while(k) {
            bool selected = false;
            for(int i=0;i<Capital.size();i++) {
                if(status[i]) {
                    if(W>=hash[i].first) {
                        W += hash[i].second;
                        status[i] = false;
                        k--;
                        selected = true;
                        break;
                    }
                }
            }
            if(selected == false) break;
        }
        
        return W;
    }
};
```
## 图中亮点之间的线路
```C++
/**
 * Definition for Directed graph.
 * struct DirectedGraphNode {
 *     int label;
 *     vector<DirectedGraphNode *> neighbors;
 *     DirectedGraphNode(int x) : label(x) {};
 * };
 */
class Solution {
public:
    /**
     * @param graph: A list of Directed graph node
     * @param s: the starting Directed graph node
     * @param t: the terminal Directed graph node
     * @return: a boolean value
     */
    bool hasRoute(vector<DirectedGraphNode*> graph,
                  DirectedGraphNode* s, DirectedGraphNode* t) {
        
        queue<DirectedGraphNode*> que;
        map<DirectedGraphNode*, bool> visited;
        
        que.push(s);
        visited[s] = true;
        while(!que.empty()) {
            DirectedGraphNode* tmp = que.front();
            que.pop();
            if(tmp == t) {
                return true;
            }
            for(auto line:tmp->neighbors) {
                if(!visited[line]) {
                    que.push(line);
                    visited[line] = true;
                }
            }
        }
        return false;
    }
};
```
## 房屋染色
```C++
class Solution {
public:
    /**
     * @param costs n x 3 cost matrix
     * @return an integer, the minimum cost to paint all houses
     */
    int minCost(vector<vector<int>>& costs) {
        // Write your code here
        if(costs.size() == 0) return 0;
        vector<int> record = costs[0];
        for(int i=1;i<costs.size();i++) {
            vector<int> tmp;
            for(int k=0;k<3;k++) {
                tmp.push_back(costs[i][k] + min(record[(k+1)%3], record[(k+2)%3]));
            }
            record = tmp;
        }
        return min(min(record[0], record[1]), record[2]);
    }
};
```
## 二叉查找树迭代器
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
 * Example of iterate a tree:
 * BSTIterator iterator = BSTIterator(root);
 * while (iterator.hasNext()) {
 *    TreeNode * node = iterator.next();
 *    do something for node
 */
class BSTIterator {
public:
    void helper(TreeNode* root) {
        if(root == NULL) return ;
        helper(root->left);
        que.push(root);
        helper(root->right);
    }
    
    //@param root: The root of binary tree.
    BSTIterator(TreeNode *root) {
        // write your code here
        helper(root);
    }

    //@return: True if there has next node, or false
    bool hasNext() {
        // write your code here
        return !que.empty();
    }
    
    //@return: return next node
    TreeNode* next() {
        // write your code here
        TreeNode* tmp = que.front();
        que.pop();
        return tmp;
    }
    
private:
    queue<TreeNode*> que;
};
```
## 生成括号
```C++
class Solution {
public:
    /**
     * @param n n pairs
     * @return All combinations of well-formed parentheses
     */
    void helper(int n, int left, int right, string tmp) {
        if(left>n || right>n || left < right) {
            return ;
        }
        if(left == n) {
            while(right != n) {
                tmp += ')';
                right++;
            }
            res.push_back(tmp);
            return ;
        }
        helper(n, left+1, right, tmp+'(');
        helper(n, left, right+1, tmp+')');
    }
    
    vector<string> generateParenthesis(int n) {
        // Write your code here
        string tmp;
        helper(n, 0, 0, tmp);
        return res;
    }
    
private:
    vector<string> res;
};
```
## 下一个排列
```C++
左边是原始排列，右边是对应的下一个排列。

1,2,3 → 1,3,2

3,2,1 → 1,2,3

1,1,5 → 1,5,1
```

```C++
class Solution {
public:
    /**
     * @param nums: a vector of integers
     * @return: return nothing (void), do not return anything, modify nums in-place instead
     */
    void nextPermutation(vector<int> &nums) {
        // write your code here
        //先从数组的末尾开始寻找，找到第一个降低的位置的数字
        //然后在从后面找到的第一个比上一个折点大的位置，进行交换
        //然后将从后向前的子序列进行逆转
        
        // 先找到从后到前的数组中降低的位置
        int N = nums.size();
        bool flag = false;
        int index = 0;
        for(int i=N-1;i>0;i--) {
            if(nums[i]>nums[i-1]) {
                index = i-1;
                flag = true;
                break;
            }
        }
        if(flag == false) {
            reverse(nums.begin(), nums.end());
        }
        if(flag == true) {
            for(int i=N-1;i>index;i--) {
                if(nums[i] > nums[index]) {
                    swap(nums[i], nums[index]);
                    break;
                }
            }
            reverse(nums.begin()+index+1, nums.end());
        }
    }
};
```

## Reverse Pairs
```C++
class Solution {
public:
    long long mergeArray(vector<int>& A, int left, int mid, int right) {
        long long cnt = 0;
        int l = left, r = mid+1;
        while(l<=mid && r <= right) {
            if((long long)A[l] > 2*(long long)A[r]) {
                cnt += (mid - l + 1);
                r++;
            } else {
                l++;
            }
        }
        sort(A.begin()+left, A.begin()+right+1);
        return cnt;
    }
    
    long long helper(vector<int>& A, int l, int r) {
        long long ans = 0;
        if (l < r) {
            int m = (l + r) >> 1;
            ans += helper(A, l, m);
            ans += helper(A, m + 1, r);
            ans += mergeArray(A, l, m, r);
        }
        return ans;
    }

    int reversePairs(vector<int>& nums) {
        
        int N = nums.size();
        if(N <= 1) return 0;
        
        tmp.resize(N, 0);
        long long cnt = 0;
        cnt = helper(nums, 0, N-1);
        
        return cnt;
    }
    
private:
    vector<int> tmp;
};
```
## 给定一组数据，找到差值最大和最小的对数？
```C++
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int main() {
    int N = 0;
    while(cin >> N) {
        vector<int> nums(N, 0);
        for(int i=0;i<N;i++) {
            cin >> nums[i];
        }
        sort(nums.begin(), nums.end());
        if(nums[0] == nums[N-1]) {
            int n = N*(N-1)/2;
            cout << n << " " << n << endl;
            continue;
        }
        int nMax = count(nums.begin(), nums.end(), nums[N-1]);
        int nMin = count(nums.begin(), nums.end(), nums[0]);
        int nMaxDiff = nMax * nMin;
        int minDiff = nums[1] - nums[0], cnt = 0;
        for(int i=1;i<N-1;i++) {
            int tmp = nums[i+1] - nums[i];
            if(tmp < minDiff) {
                minDiff = tmp;   
            }
        }
        for(int i=1;i<N;i++) {
            for(int j=i-1;j>=0;j--) {
                if(nums[i] - nums[j] == minDiff) {
                    cnt++;
                } else {
                    break;
                }
            }
        }
        cout << cnt << " " << nMaxDiff << endl;
    }
    return 0;
}
```
## 数组单调和
```C++
class MonoSum {
public:
    int helper(vector<int>& A, int left, int mid, int right) {
        int i=left, j=mid+1;
        int sum = 0;
        while(i<=mid && j <=right) {
            if(A[i] <= A[j]) {
                sum += (A[i] * (right-j+1));
                i++;
            } else {
                j++;
            }
        }
        sort(A.begin()+left, A.begin()+right+1);
        return sum;
    }
    
    int mergeSort(vector<int> &A, int left, int right) {
        if(left < right) {
            int mid = (left + right) / 2;
            int lSum = mergeSort(A, left, mid);
            int rSum = mergeSort(A, mid+1, right);
            return lSum + rSum + helper(A, left, mid, right);
        } else 
	        return 0;
    }
    
    int calcMonoSum(vector<int> A, int n) {
        return mergeSort(A, 0, n-1);
    }
};
```
## 数组中为出现的最下正整数
```C++
请设计一个高效算法，查找数组中未出现的最小正整数。
给定一个整数数组A和数组的大小n，请返回数组中未出现的最小正整数。保证数组大小小于等于500。
测试样例：
[-1,2,3,4],4
返回：1
```

```C++
class ArrayMex {
public:
    int findArrayMex(vector<int> A, int n) {
        // write code here
        map<int, bool> hash;
        for(auto a:A) {
            if(a >= 1 && a <= 500) {
                hash[a] = true;
            }
        }
        int lowBound = 1;
        for(auto item:hash) {
            if(item.first > lowBound) {
                return lowBound;
            }
            if(item.first == lowBound) {
                lowBound++;
            }
        }
        return lowBound;
    }
};
```
## 相邻最大差值
```C++
请设计一个复杂度为O(n)的算法，计算一个未排序数组中排序后相邻元素的最大差值。
给定一个整数数组A和数组的大小n，请返回最大差值。保证数组元素个数大于等于2小于等于500。
```

```C++
class MaxDivision {
public:
    int findMaxDivision(vector<int> A, int n) {
    	// write code here
        int minn = A[0];
        int maxx = A[0];
        for (int i = 1; i < n; i++)
        {
            if (A[i] > maxx)
                maxx = A[i];
            if (A[i] < minn)
                minn = A[i];
        }
        // 分成maxx-minn+1个区间(桶)，每个区间只记录最大最小值
        vector<int> vmin(n + 1, maxx);
        vector<int> vmax(n + 1, minn);

        // 最大数 单独一个桶
        vmin[n] = maxx;
        vmax[n] = maxx;

        // 剩下的n-1个数用n个桶,桶区间大小
        double bucket = 1.0 * (maxx - minn) / n;

        // 处理每一个A[i]
        for (int i = 0; i < n; i++)
        {
            if (A[i] == maxx)
                continue;
            int pos = (int)(1.0*(A[i] - minn) / bucket); // 得到元素的下标位置
            if (A[i] < vmin[pos])
            {
                vmin[pos] = A[i];
            }
            if (A[i] > vmax[pos]){
                vmax[pos] = A[i];
            }
        }

        int rs = 0;
        // 对于每个非空桶 其最小值减去前面桶的最大值 就是一个结果
        int pre = 0; // 最小的那个值在第0号桶中
        for (int i = 1; i <= n;i++)
        {
            if (vmin[i] == maxx && vmax[i] == minn) // 表示这是个空桶
                continue;

            if ((vmin[i] - vmax[pre]) > rs) // 对比找到结果
                rs = (vmin[i] - vmax[pre]);
            pre = i;
        }
        // 返回结果
        return rs;
    }
};
```
## 合唱队问题
```C++
左右递归上升子序列
```

```C++
#include <iostream>
#include <vector>
#include <math.h>

using namespace std;

int main () {
    int N = 0;
    while(cin >> N) {
        vector<int> nums(N, 0);
        for(int i=0;i<N;i++) {
            cin >> nums[i];
        }
        
        vector<int> left(N,1);
        for(int i=0;i<N;i++) {
            left[i] = 1;
            for(int j=0;j<i;j++) {
                if(nums[j] < nums[i] && left[i] < left[j] + 1) {
                    left[i] = left[j] + 1;
                }
            }
        }
        
        vector<int> right(N,1);
        for(int i=N-1;i>=0;i--) {
            right[i] = 1;
            for(int j=N-1;j>i;j--) {
                if(nums[j] < nums[i] && right[i] < right[j] + 1) {
                    right[i] = right[j] + 1;
                }
            }
        }
        int t = 0, ind = 0;
        for(int i=0;i<N;i++) {
            if(t < left[i] + right[i]) {
                t = left[i] + right[i];
                ind = i;
            }
        }
        cout << N - left[ind] - right[ind] + 1 << endl;
    }
    return 0;
}
```
## Beautiful Arrangement
```C++
class Solution {
public:
    void helper(vector<int> &flag, unordered_map<int, vector<int>> &hash, int level) {
        if(level >= flag.size()) {
            return ;
        }
        if(level == flag.size()-1) {
            if(accumulate(flag.begin(), flag.end(), 0) == flag.size()) {
                cnt++;
                return ;
            }
        }
        level++;
        vector<int> tmp = hash[level];
        for(auto item:tmp) {
            if(flag[item] == false) {
                flag[item] = true;
                helper(flag, hash, level);
                flag[item] = false;
            }
        }
        level--;
    }

    int countArrangement(int N) {
        cnt = 0;
        unordered_map<int, vector<int>> hash;
        for(int i=1;i<=N;i++) {
            vector<int> tmp;
            for(int j=1;j<=N;j++) {
                if(i % j == 0 || j % i == 0) {
                    tmp.push_back(j);
                }
            }
            hash[i] = tmp;
        }
        vector<int> flag(N+1, 0);
        flag[0] = 1;
        helper(flag, hash, 0);
        return cnt;
    }
    
private:
    int cnt;
};
```
