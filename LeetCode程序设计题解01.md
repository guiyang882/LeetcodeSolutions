# LeetCode算法设计题解(一)

@(程序设计算法)

标签： 算法设计题解


----------

## Majority Element
### 题目
Given an array of size n, find the majority element. The majority element is the element that appears more than [n/2] times.
You may assume that the array is non-empty and the majority element always exist in the array.

### 解答
想法其实很简单，问题中说某个元素出现的次数大于一般或者恰好等于一半。因此，数组中最多的数字可以出现的概率大于50%。
因此，我们可以遍历数组，每两个不相同的数据出现时就同时删除。如此反复，直到数组遍历结束。剩余的数字就是所求。

```
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        int target = 0;
        int n_count = 0;
        for(int i=0;i<nums.size();i++){
            if(n_count ==0){
            target= nums[i];
                n_count++;
            }else{
                if(target == nums[i]){
                    n_count++;
                }else{
                    n_count--;
                }
            }
        }
        return target;
    }
};
```
对于寻找在数组中大于[n/2]次数出现的数据。但是，去寻找数组中[n/k]出现次数的数字又该如何去寻找？

## Maximum Subarray

### 题目
Find the contiguous subarray within an array (containing at least one number) which has the largest sum.
For example, given the array [−2,1,−3,4,−1,2,1,−5,4],the contiguous subarray [4,−1,2,1] has the largest sum = 6.

### 解答
这个是算法中，较为经典的一道题目。我们由浅入深，给出更好的解法。
- 暴力遍历算法

```C++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int n_sum = nums[0];
        for(int i=0;i<nums.size();i++){
            for(int j=i;j<nums.size();j++){
                int tmp_sum = 0;
                for(int k=i;k<=j;k++)
                    tmp_sum += nums[k];
                if(tmp_sum > n_sum){
                    n_sum = tmp_sum;
                }
            }
        }
        return n_sum;
    }
};
```
使用上述算法确实可以得到相应的正确结果，但是当测试数据太大时，一定会超时的。即使做了如下改进后，也会超时的！

```
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int n_sum = nums[0];
        for(int i=0;i<nums.size();i++){
            int tmp_sum = 0;
            for(int j=i;j<nums.size();j++){
                tmp_sum += nums[j];
                if(tmp_sum > n_sum){
                    n_sum = tmp_sum;
                }
            }
        }
        return n_sum;
    }
};
```
- 我们使用分治算法进行求解
将数组分为长度相同的两段，分别求出这两段的最大子数组的和，同时找出最大子数组跨越了中间点的位置的和。
1. nums[1:n]的最大子数组与nums[1:n/2]的和相同
2. nums[1:n]的最大子数组与nums[n/2+1:n]的和相同
3. nums[1:n]的最大子段和为 $sum_{k=i}^{j} nums[k]$, 且 1<=i<=n,n/2+1<=j<=n;

```C++
class Solution
{
public:
    int MaxCrossArray(vector<int>& nums,int left,int center,int right)
    {
        int left_sum = INT_MIN;
        int n_sum = 0;
        for(int i=center; i>=left; i--)
        {
            n_sum += nums[i];
            if(n_sum > left_sum)
            {
                left_sum = n_sum;
            }
        }
        int right_sum = INT_MIN;
        n_sum = 0;
        for(int i=center+1; i<=right; i++)
        {
            n_sum += nums[i];
            if(n_sum > right_sum)
            {
                right_sum = n_sum;
            }
        }
        return left_sum + right_sum;
    }

    int MaxSubArray(vector<int>& nums,int left,int right)
    {
        if(left == right)
            return nums[left];

        int center = (left + right)/2;
        int left_sum = MaxSubArray(nums,left,center);
        int right_sum = MaxSubArray(nums,center+1,right);
        int cross_sum = MaxCrossArray(nums,left,center,right);

        if(left_sum >= right_sum && left_sum >= cross_sum)
            return left_sum;
        else if(right_sum >= left_sum && right_sum >= cross_sum)
            return right_sum;
        else
            return cross_sum;
    }

    int maxSubArray(vector<int>& nums)
    {
        return MaxSubArray(nums,0,nums.size()-1);
    }
};
```

- 还有要给算法，可以在O(n)的时间内，求解该问题，使用动态规划算法进行求解
在设计分治算法时，我们考虑到，若记 $b[j] = max_{1 \leq i \leq j}\{\displaystyle \sum_{k=i}^{j}a[k]\}$,$1 \leq j \leq n$。则所求解的最大字段和为
$$
max_{1 \leq i \leq j \leq n} \sum_{k=i}^{j} a[k] = max_{1 \leq j \leq n} max_{1 \leq i \leq j} \sum_{k=i}^{j} a[k] = max_{1 \leq j \leq n} b[j]
$$
由b[j]的定义可以求得如下的动态递归式
$$
b[j] = max{b[j-1]+nums[j],nums[j]}, 1 \leq j \leq n
$$
基于这个递归式，写出代码如下：

```C++
class Solution
{
public:
    int maxSubArray(vector<int>& nums){
        if (nums.empty()) 
            return 0;
        int n_sum = nums[0],local_sum=nums[0];
        for (int i=1;i<nums.size();i++){
            if(local_sum>0){
                local_sum +=nums[i];
            }
            else{
                local_sum = nums[i];
            }
            n_sum = n_sum>local_sum?n_sum:local_sum;
        }
        return n_sum;
    }
};
```

## Search a 2D Matrix II
### 问题
Write an efficient algorithm that searches for a value in an m x n matrix. This matrix has the following properties:

- Integers in each row are sorted in ascending from left to right.
- Integers in each column are sorted in ascending from top to bottom.
For example,
Consider the following matrix:

```
[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]
```
- Given target = 5, return true.
- Given target = 20, return false.

### 解答
先分析数组提供的规律，我们发现，如果从左上角开始进行选择；那么该点的左下和右边的点都比该点要大，因此只有通过搜索才可以知道，如何进行下一步决策，而如果从矩阵的右下角开始扫描，则会发生同样的问题；如果采取从右上角的点开始搜索或者左下角的点开始搜索，就通过该点的值与目标值的大小相比较进而得出下一步的搜索方向。我们就可以得到我们想要的结果。具体的代码如下：

```C++
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int row = matrix.size();
        if(row == 0)
            return false;
        int col = matrix[0].size();
        if(col == 0)
            return false;
        
        int r = 0;
        int c = col-1;
        while(r < row && c >= 0){
            if(matrix[r][c] == target)
                return true;
            if(matrix[r][c] > target){
                c--;
            }else{
                r++;
            }
        }
        return false;
    }
};
```

## Kth Largest Element in an Array
### 问题
Find the $k_{th}$ largest element in an unsorted array. Note that it is the kth largest element in the sorted order, not the kth distinct element.

For example,
Given [3,2,1,5,6,4] and k = 2, return 5.

Note: 
You may assume k is always valid, 1 ≤ k ≤ array's length.

### 解答
对于这个问题，可以采用随机化的快速算法，进行递归查找，每次根据确定的结果，得出下一次查找的范围在左边，还是在右边。

```C++
class Solution {
public:
    int partition(vector<int>& nums,int left,int right){
        int target = nums[right];
        int i=left-1;
        for(int j=left;j<right;j++){
            if(nums[j] < target){
                i++;
                if(i != j){
                    swap(nums[i],nums[j]);
                }
            }
        }
        swap(nums[i+1],nums[right]);
        return i+1;
    }

    int findKthLargest(vector<int>& nums, int k) {
        if(k < 1 || k > nums.size())
            return -1;
        //find the K_th largest means find the (length-k)_th smallest
        k = nums.size() - k;
        int left=0,right=nums.size() - 1;
        while(true){
            int index = partition(nums,left,right);
            if(index == k)
                return nums[index];
            if(index > k){
                right = index - 1;
            }else{
                left = index + 1;
            }
        }
        return -1;
    }
};
```

## Different Ways to Add Parentheses
### 问题
Given a string of numbers and operators, return all possible results from computing all the different possible ways to group numbers and operators. The valid operators are +, - and *.
Example 1
Input: "2-1-1".
```
((2-1)-1) = 0
(2-(1-1)) = 2
Output: [0, 2]
```
Example 2
Input: "2*3-4*5"
```
(2*(3-(4*5))) = -34
((2*3)-(4*5)) = -14
((2*(3-4))*5) = -10
(2*((3-4)*5)) = -10
(((2*3)-4)*5) = 10
Output: [-34, -14, -10, -10, 10]
```
### 解答
本题可以使用分治思路进行求解，因为符号表达式只有三个简单的符号，因此，我们可以使用运算符号作为表达式的分隔符。对表达式进行分割，同时因为表达式是连续的，且添加括号也只能在相邻近的位置进行添加。因此，适合使用递归进行求解。
更加形象的说法是，将遍历运算符，对运算符进行任意的切割和分组，使得利用解空间树，遍历所有分割的情况。
```C++
class Solution {
public:
    vector<int> diffWaysToCompute(string input) {
        vector<int> ans;
        bool pureNum=true;
        for(int i=0; i<input.length(); i++) {
            if (input[i]<'0' || input[i]>'9') {
                char c = input[i];
                pureNum=false;
                vector<int> L=diffWaysToCompute(input.substr(0, i));
                vector<int> R=diffWaysToCompute(input.substr(i+1, input.length()-i-1));
                for (auto l : L)
                    for (auto r : R)
                        if (c=='+') 
                            ans.push_back(l+r);
                        else if (c=='-') 
                            ans.push_back(l-r);
                        else if (c=='*') 
                            ans.push_back(l*r);
            }
        }
        
        if(pureNum)
            ans.push_back(atoi(input.c_str()));
        return ans;
    }
};
```
## Burst Balloons
### 问题
Given n balloons, indexed from 0 to n-1. Each balloon is painted with a number on it represented by array nums. You are asked to burst all the balloons. If the you burst balloon i you will get nums[left] * nums[i] * nums[right] coins. Here left and right are adjacent indices of i. After the burst, the left and right then becomes adjacent.
Find the maximum coins you can collect by bursting the balloons wisely.
Note: 
(1) You may imagine nums[-1] = nums[n] = 1. They are not real therefore you can not burst them.
(2) 0 ≤ n ≤ 500, 0 ≤ nums[i] ≤ 100

Example:
Given [3, 1, 5, 8]
Return 167
```
nums = [3,1,5,8] --> [3,5,8] -->  [3,8] -->  [8] --> []
coins =  3*1*5     +  3*5*8    +  1*3*8   + 1*8*1  = 167
```  
### 解答
先声明一下，这个是瞻仰[shichaotan][1]的代码，但是学习起见，我要好好分析一下这个代码的运行原理。
```C++
class Solution {
public:
    // remember search
    int DP(int i, int j, const vector<int> &nums, vector<vector<int> > &dp) {
        if(dp[i][j] > 0) 
            return dp[i][j];
        for(int x = i; x <= j; ++x) {
            int temp = DP(i, x-1, nums, dp) + nums[i-1]*nums[x]*nums[j+1] + DP(x+1, j, nums, dp);
            if (temp > dp[i][j]) 
                dp[i][j] = temp;
        }
        return dp[i][j];
    }

    int maxCoins(vector<int>& nums) {
        int n = nums.size();
        nums.insert(nums.begin(), 1);
        nums.insert(nums.end(), 1);
        vector<vector<int> > dp(n+2, vector<int>(n+2, 0));
        return DP(1, n, nums, dp);
    }
};
```
刚开始初始化矩阵，结果如下，其中主对角线上的元素为，当仅仅burst一个气球时，所得的分数；通过题目可知，当行row大于列col时，表示的从长度为[row,col]的矩阵中，burst所有气球的得分。自然为零；
```
0    0    0    0    0    0
0    3                
0    0    15            
0    0    0    40        
0    0    0    0    40    
0    0    0    0    0    0
```
经过绘制表格，逐渐发现，上述代码中
```C++
int temp = DP(i, x-1, nums, dp) + nums[i-1]*nums[x]*nums[j+1] + DP(x+1, j, nums, dp);
```
的含义为：
 1. DP[left,right]表示，将气球的标号为left到right的所有气球，全部burst之后，取得的最大的分值为DP[left,right];
 2. nums[i-1]*nums[x]*nums[j+1]表示，当最后一次burst第x个气球时，在当前的状态下的得分；
 3. 并且遍历nums[i,j]这个区间内的气球，每次选取一个气球最后burst，求这其中最大的一个最后一个选中的气球burst之后的得分。
最后的矩阵结果如下：
```
0    0    0    0    0    0
0    3    30    159    167    0
0    0    15    135    159    0
0    0    0    40    48    0
0    0    0    0    40    0
0    0    0    0    0    0
```
这个是一个较为基础的DP算法，开始来比较直观，具有一定的启发作用：
```C++
class Solution {
public:
    int maxCoins(vector<int>& nums) {
        int n = nums.size();
        nums.insert(nums.begin(), 1);
        nums.insert(nums.end(), 1);
        vector<vector<int> > DP(n+2, vector<int>(n+2, 0));

        int length, start, end;
        for(length=1; length<=n; length++){
            for(start=1; start<=n-length+1; start++){
                end = start+length-1;
                for(int i=start; i<=end; i++){
                    int res = DP[start][i-1]+nums[start-1]*nums[i]*nums[end+1]+DP[i+1][end];
                    if(DP[start][end] < res)
                        DP[start][end] = res;
                }
            }
        }
        for(int i=0;i<DP.size();i++){
            for(int j=0;j<DP[i].size();j++)
                cout << DP[i][j] << " ";
            cout << endl;
        }
        return DP[1][n];
    }
};
```

## Count of Smaller Numbers After Self
### 问题
You are given an integer array nums and you have to return a new counts array. The counts array has the property where counts[i] is the number of smaller elements to the right of nums[i].

Example:
```
Given nums = [5, 2, 6, 1]

To the right of 5 there are 2 smaller elements (2 and 1).
To the right of 2 there is only 1 smaller element (1).
To the right of 6 there is 1 smaller element (1).
To the right of 1 there is 0 smaller element.

Return the array [2, 1, 1, 0].
```
### 解答
先使用暴力算法，复杂度自然很大。结果自然可知，运行超时！
```C++
class Solution {
public:
    vector<int> countSmaller(vector<int>& nums) {
        vector<int> flag(nums.size(),0);
        int length = nums.size();
        for(int i=0;i<length;i++){
            int times = 0;
            for(int j=i+1;j<length;j++){
                if(nums[i] > nums[j])
                    times++;
            }
            flag[i] = times;
        }
        return flag;
    }
};
```
之后，继续寻找其它可行的办法，我们从后向前遍历数组。然后，将遍历过的数据排序按照从小到大的顺序排列。至于排列方式可以采用二叉搜索树进行。
```C++
class Solution {
public:
    // bianry search tree
    struct BinarySearchTreeNode
    {
        int val;     
        int less;      // count of members less than val
        int count;     // count of members equal val
        BinarySearchTreeNode *left, *right;
        BinarySearchTreeNode(int value) : val(value), less(0),count(1),left(NULL), right(NULL) {}
    };

    void insert(BinarySearchTreeNode *root, const int value, int &numLessThan)
    {
        if(value < root->val)  // right
        {
            root->less++;
            if(root->right == NULL)
                root->right = new BinarySearchTreeNode(value);
            else
                insert(root->right, value, numLessThan);
        }
        else if(value > root->val)  // left
        {
            numLessThan += root->less + root->count;
            if(!root->left)
                root->left = new BinarySearchTreeNode(value);
            else
                insert(root->left, value, numLessThan);
        }
        else
        {
            numLessThan += root->less;
            root->count++;
            return;
        }
    }

    vector<int> countSmaller(vector<int>& nums) {
        vector<int> count(nums.size());
        if(nums.size() == 0)
            return count;
        BinarySearchTreeNode root(nums[nums.size()-1]);
        for(int i = nums.size() - 2; i >= 0; i--)
        {
            int numLessThan = 0;
            insert(&root, nums[i], numLessThan);
            count[i] = numLessThan;
        }
        return count;
    }
};
```
## Merge k Sorted Lists
### 问题
Merge k sorted linked lists and return it as one sorted list. Analyze and describe its complexity.
提供的数据结构如下：
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
对于这个题目，目的很明确。使用多路归并算法，将数据组织起来，然后的大最好的结果，其中比较费时的操作是来自于，对于k路链表数据的比较，其中，可以使用小顶堆算法，将数据组织起来，降低比较的次数。其中，小顶堆可以使用STL中的priority_queue，这个数据结构，可以很容易的组织起来小顶堆，要继续加强对于其他STL的用法的熟悉！
```C++
class Solution {
public:
    struct NodeInfo {
        int val;
        int from_list;
        NodeInfo(int val,int from): val(val),from_list(from) {}
    };
    
    struct cmp_small { 
        bool operator()(const NodeInfo &a,const NodeInfo& b){
            return a.val>b.val;
        }
    };

    ListNode* mergeKLists(vector<ListNode*>& lists) {
        if(lists.size() == 0)
            return NULL;
        
        ListNode* root = NULL,*p = NULL;
        priority_queue<int,vector<NodeInfo>,cmp_small> heap_queue;
        //init
        for(int i=0;i<lists.size();i++){
            if(lists[i] != NULL){
                NodeInfo tmp(lists[i]->val,i);
                heap_queue.push(tmp);    
            }
        }
        
        if(heap_queue.empty())
            return root;
            
        
        int cur_from = -1;
        while(!heap_queue.empty()){
            NodeInfo top_node = heap_queue.top();
            heap_queue.pop();
            cur_from = top_node.from_list;

            if(root == NULL){
                root = lists[cur_from];
                lists[cur_from] = lists[cur_from]->next;
                root->next = NULL;
                p = root;
            }else{
                p->next = lists[cur_from];
                lists[cur_from] = lists[cur_from]->next;
                p = p->next;
                p->next = NULL;
            }

            if(lists[cur_from] != NULL){
                NodeInfo tmp(lists[cur_from]->val,cur_from);
                heap_queue.push(tmp);
            }
        }
        return root;
    }
};
```
其实，没有必要自己另外创建要给数据结构去存储相应的数据！具体的run代码如下：
```C++
class Solution {
private:
    class Cmp {
    public:
        int operator()(ListNode* a, ListNode* b){
            return a->val > b->val;
        }
    };

public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        priority_queue<ListNode*, vector<ListNode*>, Cmp> q;
        for (int i = 0; i < lists.size(); ++ i)
            if (lists[i]) q.push(lists[i]);
        ListNode dummy(0), *tail = &dummy;
        while (!q.empty()){
            ListNode *tmp = new ListNode(q.top()->val);
            tail->next = tmp;
            tmp = q.top()->next;
            q.pop();
            if (tmp) q.push(tmp);
            tail = tail->next;
        }
        return dummy.next;
    }
};
```
## Nim Game
### 问题
You are playing the following Nim Game with your friend: There is a heap of stones on the table, each time one of you take turns to remove 1 to 3 stones. The one who removes the last stone will be the winner. You will take the first turn to remove the stones.
Both of you are very clever and have optimal strategies for the game. Write a function to determine whether you can win the game given the number of stones in the heap.
For example, if there are 4 stones in the heap, then you will never win the game: no matter 1, 2, or 3 stones you remove, the last stone will always be removed by your friend.

Hint:
If there are 5 stones in the heap, could you figure out a way to remove the stones such that you will always be the winner?
### 解答
Nim游戏，是一个组合优化问题，对这类问题，有其数学上的证明！具体的问题和解答证明过程可以详见[Nim百科][2]。对于计算机而言，我们需要将问题归结到某个固定的已知范围内，然后，通过问题的简化可以得到局部的状态！
```C++
class Solution {
public:
    bool canWinNim(int n) {
        if((n%4) != 0)
            return true;
        else
            return false;
    }
};
```
## Best Time to Buy and Sell Stock
### 问题
Say you have an array for which the $i_{th}$ element is the price of a given stock on day i.
If you were only permitted to complete at most one transaction (ie, buy one and sell one share of the stock), design an algorithm to find the maximum profit.
### 解答
```C++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if (prices.empty()) 
            return 0;
        int ans = 0, min_val = prices[0];
        //找到已经遍历过的最小值
        //找到尚未遍历过的最大值
        for (int i = 1; i < prices.size(); i++) {
            min_val = min(min_val, prices[i-1]);
            ans = max(ans, prices[i] - min_val);
        }
        return ans;
    }
};
```
## Best Time to Buy and Sell Stock II
### 问题
Say you have an array for which the $i_{th}$ element is the price of a given stock on day i.
Design an algorithm to find the maximum profit. You may complete as many transactions as you like (ie, buy one and sell one share of the stock multiple times). However, you may not engage in multiple transactions at the same time (ie, you must sell the stock before you buy again).
### 解答
这个问题是上个问题的加强版本，其中涉及到多次的股票交易，这其中不允许在同一个时间多次操作股票数据，且每次在买入股票数据时，必须已经将手中股票卖掉了。其实问题就变得很简单了！将股票数据中，涨的交易全部用来操作！
```C++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int profit=0;
        for(int i=1;i<prices.size();i++){
            if(prices[i]>prices[i-1]){
                profit+=prices[i]-prices[i-1];
            }
        }
        return profit;
    }
};
```
## Best Time to Buy and Sell Stock with Cooldown
### 问题
Say you have an array for which the ith element is the price of a given stock on day i.
Design an algorithm to find the maximum profit. You may complete as many transactions as you like (ie, buy one and sell one share of the stock multiple times) with the following restrictions:
You may not engage in multiple transactions at the same time (ie, you must sell the stock before you buy again).
After you sell your stock, you cannot buy stock on next day. (ie, cooldown 1 day)
Example:
```C++
prices = [1, 2, 3, 0, 2]
maxProfit = 3
transactions = [buy, sell, cooldown, buy, sell]
```
### 解答
这个很明显是个DP问题，其中涉及到要给交易日的CoolDown，并且允许我们进行多次的交易。这样，问题的关键就是如何去定义这个CoolDown。
$$
assitpf[i] = 
\begin{cases} 
max(profit[i],0) & \mbox{if } i \equiv 0 \\ 
profit[i]+max(assitpf[i-1],max(assitpf[0] \quad to \quad assitpf[i-3])) & \mbox{if } i \geq 1
\end{cases}
$$
经过分析简化，我们可以进一步简化这个表达式。
$$maxp = max(maxp,assitpf)$$同时，我们可以得出
$$prev2Max = max(prev2Max,assiftpf[i-3])$$这样问题就可以这样表达式就可以这样简化了！
$$
assitpf[i] = 
\begin{cases} 
max(profit[i],0) & \mbox{if } i \equiv 0 \\ 
profit[i]+max(assitpf[i-1],max(0，prev2Max)) & \mbox{if } i \geq 1
\end{cases}
$$
具体的代码如下：
```C++
class Solution{
public:
    int maxProfit(vector<int>& prices){
        int len=prices.size();
        if(len<2)
            return 0;
        if(len==2)
            return max(0,prices[1]-prices[0]);
        len--;
        vector<int> profit(len,0);
        vector<int> assitpf(len,0);
        for(int i=0; i<len; i++)
            profit[i]=prices[i+1]-prices[i];
        int maxp=0,prev2max=0;
        for(int i=0; i<len; i++){
            if(i>=3)
                prev2max=max(prev2max,assitpf[i-3]);
            if(i==0){
                assitpf[i]=max(profit[i],0);
                maxp=assitpf[i];
            }else{
                assitpf[i]=profit[i]+max(assitpf[i-1],max(prev2max,0));
                maxp=max(maxp,assitpf[i]);
            }
        }
        for(int i=0;i<assitpf.size();i++)
            cout << assitpf[i] << " " ;
        return maxp;
    }
};
```
## Best Time to Buy and Sell Stock IV
### 问题
Say you have an array for which the $i_{th}$ element is the price of a given stock on day i.
Design an algorithm to find the maximum profit. You may complete at most k transactions.
Note:
You may not engage in multiple transactions at the same time (ie, you must sell the stock before you buy again).
### 解答
```C++
class Solution {
public:
    int maxProfit(int k, vector<int>& prices) {
        int n = prices.size();
        if(n<=1 || k<1)
            return 0;

        int temp = 0;
        int count = 0;
        int i;
        for(i=1; i<n; i++){
            if(prices[i] - prices[i-1] > 0){
                temp = temp + prices[i] - prices[i-1];
                count = count + 1;
            }
        }

        if(count < k)
            return temp; // When k is big, dynamic programming might become time consuming

        // Dynamic programming
        vector<int> curProfit(n, 0);
        vector<int> preProfit(n, 0);

        int lowCost,j;
        for(j=0; j<k; j++){
            lowCost = prices[0];
            for(i=1; i<n; i++){
                if(curProfit[i-1] < prices[i] - lowCost)
                    curProfit[i] = prices[i] - lowCost;
                else
                    curProfit[i] = curProfit[i-1];

                if(prices[i]-preProfit[i-1] < lowCost)
                    lowCost = prices[i]-preProfit[i-1];
            }
            preProfit = curProfit;
        }
        return curProfit[n-1];
    }
};
```
## Invert Binary Tree
### 问题
Invert a binary tree.
```shell
     4
   /   \
  2     7
 / \   / \
1   3 6   9
```
to
```shell
     4
   /   \
  7     2
 / \   / \
9   6 3   1
```
### 解答
其中数据结构定义如下：
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
具体实现如下：
```C++
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        if(root == NULL)
            return NULL;
        swap(root->left,root->right);
        invertTree(root->left);
        invertTree(root->right);
        return root;
    }
};
```
## Single Number
### 问题
Given an array of integers, every element appears twice except for one. Find that single one.
Note:
Your algorithm should have a linear runtime complexity. Could you implement it without using extra memory?
### 解答
```C++
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        // XOR (^) is both commutative and associative 
        // The numbers which appear twice will be cancelled
        // Only the number that appear twice survive 
        int value = 0;
        int i, n;
        n = nums.size();
        for(i=0; i<n; i++)
            value = value ^ nums[i];
        return value;
    }
};
```

## Single Number II
### 问题
Given an array of integers, every element appears three times except for one. Find that single one.
Note:
Your algorithm should have a linear runtime complexity. Could you implement it without using extra memory?
### 解答
```C++
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int x=0,res=0;
        
        for(int i = 0; i < sizeof(int)*8; i++) {
            x = 1 << i;
            int sum = 0;
            for(int j = 0; j < nums.size(); j++) {
                if(x & nums[j]) 
                    sum++;
            }

            if(sum % 3) 
                res = res | x;
        }

        return res;
    }
};
```

## Single Number III
### 问题
Given an array of numbers nums, in which exactly two elements appear only once and all the other elements appear exactly twice. Find the two elements that appear only once.
For example:
Given nums = [1, 2, 1, 3, 2, 5], return [3, 5].

Note:
The order of the result is not important. So in the above example, [5, 3] is also correct.
Your algorithm should run in linear runtime complexity. Could you implement it using only constant space complexity?
### 解答
```C++
class Solution {
public:
    vector<int> singleNumber(vector<int>& nums) {
        int acc = accumulate(nums.begin(), nums.end(), 0, bit_xor<int>());
        int mask = acc & ~(acc-1);
        vector<int> output{
            accumulate(nums.begin(), nums.end(), 0, [mask](int acc, int elem) {return elem & mask ? acc ^ elem : acc;}),
            accumulate(nums.begin(), nums.end(), 0,[mask](int acc, int elem) {return elem & mask ? acc : acc ^ elem;})
        };
        return output;
    }
};
```
## Valid Anagram
### 问题
Given two strings s and t, write a function to determine if t is an anagram of s.

For example:
```Shell
s = "anagram", t = "nagaram", return true.
s = "rat", t = "car", return false.
```
Note:
You may assume the string contains only lowercase alphabets.

Follow up:
What if the inputs contain unicode characters? How would you adapt your solution to such case?
### 解答
这个很明显，使用的是统计特性。经某个字符按照字符进行统计，然后通过另一个字符串进行统计，如果一样两者的统计结果一样，就可以结束了！
```C++
class Solution {
public:
    bool isAnagram(string s, string t) {
        if(s.size() != t.size())
            return false;
        vector<int> s_cnt(26,0);
        for(int i=0;i<s.size();i++){
            s_cnt[s[i]-'a']++;
        }
        for(int i=0;i<t.size();i++){
            s_cnt[t[i]-'a']--;
            if(s_cnt[t[i]-'a'] < 0)
                return false;
        }
        return true;
    }
};
```
## Excel Sheet Column Number
### 问题
Given a column title as appear in an Excel sheet, return its corresponding column number.

For example:
```SQL
    A -> 1
    B -> 2
    C -> 3
    ...
    Z -> 26
    AA -> 27
    AB -> 28 
```
### 解答
```C++
class Solution {
public:
    int titleToNumber(string s) {
        if(s.size() == 0)
            return 0;
        int res = 0;
        for(int i=s.size()-1;i>=0;i--){
            res += ((s[i]-'A' + 1) * pow(26,s.size()-i-1));
        }
        return res;
    }
};
```

## Excel Sheet Column Title
### 问题
Given a positive integer, return its corresponding column title as appear in an Excel sheet.

For example:
```SQL
    1 -> A
    2 -> B
    3 -> C
    ...
    26 -> Z
    27 -> AA
    28 -> AB 
```
### 解答
```C++
class Solution {
public:
    string convertToTitle(int n) {
        if(n == 0)
            return "";
        char str[15] = {'\0'};
        int index = 0;
        while(n){
            if(n%26 == 0){
                str[index++] = 'Z';
                n -= 26;
            }
            else
                str[index++] = n%26 + 'A' -1;
            n = n / 26;
        }
        string res;
        res.resize(index);
        for(int i=0;i<index;i++)
            res[i] = str[index-i-1];
        return res;
    }
};
```
同时这里有个更加精炼的代码：
```C++
class Solution {
public:
    string convertToTitle(int n) {
        int num;
        int number = n;
        string s, ss;
        while(number>0)
        {
            number = number-1;
            num = number%26;
            ss = 'A' + num;
            s = ss + s;
            number = number/26;
        }
        return s;
    }
};
```
## Lowest Common Ancestor of a Binary Search Tree
### 问题
Given a binary search tree (BST), find the lowest common ancestor (LCA) of two given nodes in the BST.

According to the definition of LCA on Wikipedia: “The lowest common ancestor is defined between two nodes v and w as the lowest node in T that has both v and w as descendants (where we allow a node to be a descendant of itself).”
```Shell
        _______6______
       /              \
    ___2__          ___8__
   /      \        /      \
   0      _4       7       9
         /  \
         3   5
```
For example, the lowest common ancestor (LCA) of nodes 2 and 8 is 6. Another example is LCA of nodes 2 and 4 is 2, since a node can be a descendant of itself according to the LCA definition.
### 解答
这其中使用的数据结构是
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
具体的实现如下
```C++
//并且这个是二叉树是有顺序的，因此，可以通过数据的大小，来指导寻找共同祖先的方向。
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(root == NULL || p == root || q == root)
            return root;
        if(p == NULL || q == NULL)
            return NULL;
        if((root->val > p->val && root->val < q->val) || (root->val > q->val && root->val < p->val))
            return root;
        if(root->val > p->val && root->val > q->val)
            return lowestCommonAncestor(root->left,p,q);
        if(root->val < p->val && root->val < p->val)
            return lowestCommonAncestor(root->right,p,q);
    }
};
```
## Lowest Common Ancestor of a Binary Tree
### 问题
Given a binary tree, find the lowest common ancestor (LCA) of two given nodes in the tree.

According to the definition of LCA on Wikipedia: “The lowest common ancestor is defined between two nodes v and w as the lowest node in T that has both v and w as descendants (where we allow a node to be a descendant of itself).”
```Shell
        _______3______
       /              \
    ___5__          ___1__
   /      \        /      \
   6      _2       0       8
         /  \
         7   4
```
For example, the lowest common ancestor (LCA) of nodes 5 and 1 is 3. Another example is LCA of nodes 5 and 4 is 5, since a node can be a descendant of itself according to the LCA definition.
### 解答
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
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(root == NULL || root == p || root == q)
            return root;
        if(q == NULL || p == NULL)
            return NULL;
        TreeNode* L = lowestCommonAncestor(root->left,p,q);
        TreeNode* R = lowestCommonAncestor(root->right,p,q);
        if(L && R)
            return root;
        return L?L:R;
    }
};
```
## Number of 1 Bits
### 问题
Write a function that takes an unsigned integer and returns the number of ’1' bits it has (also known as the Hamming weight).

For example, the 32-bit integer ’11' has binary representation **00000000000000000000000000001011**, so the function should return 3.
### 解答
```C++
class Solution {
public:
    int hammingWeight(uint32_t n) {
        int n_cnt = 0;
        while(n){
            if(n & 1)
                n_cnt++;
            n = n >> 1;
        }
        return n_cnt;
    }
};
```
## Reverse Bits
### 问题
Reverse bits of a given 32 bits unsigned integer.

For example, given input 
**43261596** (represented in binary as **00000010100101000001111010011100**), 
return **964176192** (represented in binary as **00111001011110000010100101000000**).

Follow up:
If this function is called many times, how would you optimize it?
### 解答
```C++
class Solution {
public:
    uint32_t reverseBits(uint32_t n) {
        uint32_t result = 0;
        for(int i=0; i<32; i++)
        {
            if(((1<<i)&n) > 0)
                result = result | (1<<(31-i));
        }
        return result;
    }
};
```
还用一个分治算法的逻辑进行编写。
```C++
class Solution {
public:
    uint32_t reverseBits(uint32_t n) {
        n=(n&0x55555555)<<1|(n>>1)&0x55555555;
        n=(n&0x33333333)<<2|(n>>2)&0x33333333;
        n=(n&0x0f0f0f0f)<<4|(n>>4)&0x0f0f0f0f;
        n=(n&0x00ff00ff)<<8|(n>>8)&0x00ff00ff;
        n=n<<16|n>>16;
        return n;
    }
};
```
## Roman to Integer
### 问题
Given a roman numeral, convert it to an integer.
Input is guaranteed to be within the range from 1 to 3999.

### 解答
罗马数字是阿拉伯数字传入之前使用的一种数码。罗马数字采用七个罗马字母作数字、即Ⅰ（1）、X（10）、C（100）、M（1000）、V（5）、L（50）、D（500）。记数的方法：
 - 相同的数字连写，所表示的数等于这些数字相加得到的数，如 Ⅲ=3；
 - 小的数字在大的数字的右边，所表示的数等于这些数字相加得到的数，如 Ⅷ=8、Ⅻ=12；
 - 小的数字（限于 Ⅰ、X 和 C）在大的数字的左边，所表示的数等于大数减小数得到的数，如 Ⅳ=4、Ⅸ=9；
 - 在一个数的上面画一条横线，表示这个数增值 1,000 倍，如=5000。

```C++
class Solution {
public:
    int romanToInt(string s) {
        unordered_map<char, int> myMap = {{'I',1}, {'V',5}, {'X',10}, {'L',50}, {'C',100}, {'D',500}, {'M',1000}};
        int res = myMap[s.back()];
        for (unsigned int i = 1; i < s.size(); i++) {
            if (myMap[s[i-1]] < myMap[s[i]])
                res -= myMap[s[i-1]];
            else
                res += myMap[s[i-1]];
        }
        return res;
    }
};
```
## Integer to Roman
### 问题
Given an integer, convert it to a roman numeral.
Input is guaranteed to be within the range from 1 to 3999.

### 解答
```C++
class Solution {
public:
    string intToRoman(int num) {
        string roman[4][10] = {
            {"", "I", "II", "III", "IV", "V", "VI", "VII", "VIII", "IX"},
            {"", "X", "XX", "XXX", "XL", "L", "LX", "LXX", "LXXX", "XC"},
            {"", "C", "CC", "CCC", "CD", "D", "DC", "DCC", "DCCC", "CM"},
            {"", "M", "MM", "MMM"}
        };
        string result;
        int cur, R = 0;
        while(num){
            cur = num % 10;
            string tmp(roman[R++][cur]);
            result = tmp.append(result);
            num /= 10;
        }
        return result;
    }
};
```
## Reverse Linked List
### 问题
Reverse a singly linked list.
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
    ListNode* reverseList(ListNode* head) {
        if(head == NULL || head->next == NULL)
            return head;
        ListNode* root = head,*p=head;
        head = head->next;
        root->next = NULL;
        while(head->next){
            p = head;
            head = head->next;
            p->next = root;
            root = p;
        }
        head->next = root;
        return head;
    }
};
```

## Power of Two
### 问题
Given an integer, write a function to determine if it is a power of two.
### 解答
```C++
class Solution {
public:
    bool isPowerOfTwo(int n) {
        if(n<1) return false;
        while(!(n&0x1)){
            n=n>>1;
        }
        if((n|0x0) == 0x1)
            return true;
        return false;
    }
};
```
## Power of Three
### 问题
Given an integer, write a function to determine if it is a power of three.
### 解答
```C++
class Solution {
public:
    bool isPowerOfThree(int n) {
        if(n == 1)
            return true;
        if(n == 0 || n % 3 > 0)
            return false;
        return isPowerOfThree(n / 3);
    }
};
```
## Remove Duplicates from Sorted List
### 问题
Given a sorted linked list, delete all duplicates such that each element appear only once.
For example,
```C++
Given 1->1->2, return 1->2.
Given 1->1->2->3->3, return 1->2->3.
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
    ListNode* deleteDuplicates(ListNode* head) {
        if(head == NULL || head->next == NULL)
            return head;
        int cur_val = head->val;
        ListNode* pre = head,*root = head;
        head = head->next;
        while(head){
            if(cur_val == head->val){
                pre->next = head->next;
                head = pre->next;
            }else{
                cur_val = head->val;
                pre = pre->next;
                head = head->next;
            }
        }
        return root;
    }
};
```


[1]: https://leetcode.com/discuss/user/shichaotan "这个是leetcoder shichaotan的答案解析"
[2]: http://baike.baidu.com/link?url=BeJEp_CYt6lJPAV18XBEi-6GOQHhnk8rqt1XSsDND2-M76W-rOnlne61bK6Nu1kQIbzv3dTQdigqS5jMOboPZa "Nim游戏百科"


