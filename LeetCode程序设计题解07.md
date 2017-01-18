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
## 迷你推特
实现一个迷你的推特，支持下列几种方法  
1. postTweet(user_id, tweet_text). 发布一条推特.  
2. getTimeline(user_id). 获得给定用户最新发布的十条推特，按照发布时间从最近的到之前排序  
3. getNewsFeed(user_id). 获得给定用户的朋友或者他自己发布的最新十条推特，从发布时间最近到之前排序  
4. follow(from_user_id, to_user_id). from_user_id 关注 to_user_id.  
5. unfollow(from_user_id, to_user_id). from_user_id 取消关注 to_user_id.  

```C++
/**
 * Definition of Tweet:
 * class Tweet {
 * public:
 *     int id;
 *     int user_id;
 *     String text;
 *     static Tweet create(int user_id, string tweet_text) {
 *         // This will create a new tweet object,
 *         // and auto fill id
 *     }
 * }
 */

bool cmp(Tweet a, Tweet b) {
    if(a.id > b.id) return true;
    return false;
}

class MiniTwitter {
private:
    // user_id
    map<int, vector<Tweet>> usermap;
    map<int, set<int>> userConnections;
    
public:
    MiniTwitter() {
        // initialize your data structure here.
        usermap.clear();
        userConnections.clear();
    }

    // @param user_id an integer
    // @param tweet a string
    // return a tweet
    Tweet postTweet(int user_id, string tweet_text) {
        //  Write your code here
        Tweet obj = Tweet::create(user_id, tweet_text);
        if(usermap.count(user_id) == 0) {
            usermap[user_id] = vector<Tweet>{obj};
        } else {
            if(usermap[user_id].size() < 10) {
                usermap[user_id].insert(usermap[user_id].begin(), obj);
            } else {
                usermap[user_id].pop_back();
                usermap[user_id].insert(usermap[user_id].begin(), obj);
            }
        }
        return obj;
    }

    // @param user_id an integer
    // return a list of 10 new feeds recently
    // and sort by timeline
    vector<Tweet> getNewsFeed(int user_id) {
        // Write your code here
        vector<Tweet> res;
        res = getTimeline(user_id);
        for(auto user:userConnections[user_id]) {
            vector<Tweet> tmp = getTimeline(user);
            if(tmp.size() > 0) {
                res.insert(res.begin(), tmp.begin(), tmp.end());
            }
        }
        sort(res.begin(), res.end(), cmp);
        if(res.size() > 10) {
            vector<Tweet> tmp;
            tmp.assign(res.begin(), res.begin() + 10);
            return tmp;
        }
        return res;
    }
        
    // @param user_id an integer
    // return a list of 10 new posts recently
    // and sort by timeline
    vector<Tweet>  getTimeline(int user_id) {
        // Write your code here
        vector<Tweet> res;
        if(usermap.count(user_id) == 0) {
            return res;
        }
        return usermap[user_id];
    }

    // @param from_user_id an integer
    // @param to_user_id an integer
    // from user_id follows to_user_id
    void follow(int from_user_id, int to_user_id) {
        // Write your code here
        if(userConnections.count(from_user_id) == 0) {
            userConnections[from_user_id] = set<int>{to_user_id};
        } else {
            userConnections[from_user_id].insert(to_user_id);
        }
    }

    // @param from_user_id an integer
    // @param to_user_id an integer
    // from user_id unfollows to_user_id
    void unfollow(int from_user_id, int to_user_id) {
        // Write your code here
        if(userConnections.count(from_user_id) != 0) {
            userConnections[from_user_id].erase(to_user_id);
        }
    }
};
```
## 螺栓与螺母匹配的问题
```C++
/**
 * class Comparator {
 *     public:
 *      int cmp(string a, string b);
 * };
 * You can use compare.cmp(a, b) to compare nuts "a" and bolts "b",
 * if "a" is bigger than "b", it will return 1, else if they are equal,
 * it will return 0, else if "a" is smaller than "b", it will return -1.
 * When "a" is not a nut or "b" is not a bolt, it will return 2, which is not valid.
*/
class Solution {
public:
    /**
     * @param nuts: a vector of integers
     * @param bolts: a vector of integers
     * @param compare: a instance of Comparator
     * @return: nothing
     */
    void my_qsort(vector<string> &nuts, vector<string> &bolts, Comparator compare, int l, int r) {
        if(l >= r) {
            return ;
        }
        int ind = my_partition(nuts, bolts[l], compare, l, r);
        my_partition(bolts, nuts[ind], compare, l, r);
        my_qsort(nuts, bolts, compare, l, ind - 1);
        my_qsort(nuts, bolts, compare, ind+1, r);
    }
    
    int my_partition(vector<string> &str, string &pivot, Comparator compare, int l, int u) {
        int m = l;
        for (int i = l + 1; i <= u; ++i) {
            if (compare.cmp(str[i], pivot) == -1 || 
                compare.cmp(pivot, str[i]) == 1) {

                ++m;
                std::swap(str[m], str[i]);
            } else if (compare.cmp(str[i], pivot) == 0 || 
                       compare.cmp(pivot, str[i]) == 0) {
                // swap nuts[l]/bolts[l] with pivot
                std::swap(str[i], str[l]);
                --i;
            }
        }
        // move pivot to proper index
        std::swap(str[m], str[l]);

        return m;
    }
    
    void sortNutsAndBolts(vector<string> &nuts, vector<string> &bolts, Comparator compare) {
        // write your code here
        if(nuts.size() == 0 || bolts.size() == 0 || nuts.size() != bolts.size()) {
            return ;
        }
        my_qsort(nuts, bolts, compare, 0, nuts.size()-1);
    }
};
```
## 图像旋转90度，按照顺时针旋转
```C++
class Solution {
public:
    /**
     * @param matrix: A list of lists of integers
     * @return: Void
     */
    void rotate(vector<vector<int> > &matrix) {
        // write your code here
        int N = matrix.size();
        if(N == 0) return ;
        for(int i=0; i<N/2; i++) {
            int j = N - i - 1;
            swap(matrix[i], matrix[j]);
        }
        for(int i=0;i<N;i++) {
            for(int j=0;j<i;j++) {
                swap(matrix[i][j], matrix[j][i]);
            }
        }
    }
};
```
## 完美平方数
```C++
class Solution {
public:
    /**
     * @param n a positive integer
     * @return an integer
     */
    int numSquares(int n) {
        // Write your code here
        if(n<=0) return 0;
        
        vector<int> dp(n+1, INT_MAX);
        for(int i=0; i*i<=n; i++) {
            dp[i*i] = 1;
        }
        for(int a=0; a<=n; a++) {
            for(int b=0; a+b*b<=n; b++) {
                dp[a + b*b] = min(dp[a]+1, dp[a+b*b]);
            }
        }
        return dp[n];
    }
};
```
## 最小差
```C++
class Solution {
public:
    /**
     * @param A, B: Two integer arrays.
     * @return: Their smallest difference.
     */
    int smallestDifference(vector<int> &A, vector<int> &B) {
        // write your code here
        //if(A.size() == 0 || B.size() == 0) return 0;
        
        int ret = INT_MAX;
        sort(B.begin(), B.end());
        for(int i=0;i<A.size();i++) {
            int ind = lower_bound(B.begin(), B.end(), A[i]) - B.begin();
            ret = min(ret, abs(A[i] - B[ind]));
            if(ind > 0) {
                ret = min(ret, abs(A[i] - B[ind-1]));
            }
            if(ind + 1 < B.size()) {
                ret = min(ret, abs(A[i] - B[ind+1]));
            }
        }
        return ret;
    }
};
```
## x的n次幂
```C++
class Solution {
public:
    /**
     * @param x the base number
     * @param n the power number
     * @return the result
     */
    double myPow(double x, int n) {
        // Write your code here
        if(x == 0) return 0;
        if(n == 0) return 1;
        if(n == 1) return x;
        bool flag = true;
        if(n < 0) flag = false, n = -n;
        
        int k = n / 2;
        int l = n - k * 2;
        double rk = myPow(x, k);
        double rl = myPow(x, l);
        double res = rk * rk * rl;
        if (flag == false) 
            return 1 / res;
        return res;
    }
};
```
## 最接近和零的子数组
```C++
class Solution {
public:
    /**
     * @param nums: A list of integers
     * @return: A list of integers includes the index of the first number 
     *          and the index of the last number
     */
    vector<int> subarraySumClosest(vector<int> nums) {
        // write your code here
        // write your code here  
        vector<pair<int,int> > sum;  
        int temp = 0;  
        sum.push_back(make_pair(temp,-1));  
        for(int i=0;i<nums.size();i++)  
        {  
            temp+=nums[i];  
            sum.push_back(make_pair(temp,i));  
        }  
        sort(sum.begin(),sum.end());  
        int start,end;  
        int diff = INT_MAX;  
        for(int i=1;i<sum.size();i++)  
        {  
            if(abs(sum[i].first-sum[i-1].first)<=diff)  
            {  
                diff = abs(sum[i].first-sum[i-1].first);  
                start = min(sum[i].second,sum[i-1].second)+1;  
                end = max(sum[i].second,sum[i-1].second);  
            }  
        }  
        vector<int> ret;  
        ret.push_back(start);  
        ret.push_back(end);  
        return ret;  
    }
};
```
## 二进制表示
```C++
class Solution {
public:
    /**
     *@param n: Given a decimal number that is passed in as a string
     *@return: A string
     */
    string getInt(int intPart) {
        string res ; 
        if(intPart == 0) {
            res = "0";
        }
        while(intPart) {
            res = string(1, intPart % 2 + '0') + res;
            intPart /= 2;
        }
        return res;
    }
    
    string getDouble(double resPart) {
        string res;
        
        if (resPart == 0) return "0";
        
        while (resPart != 0.0) {
            double ans = resPart * 2;
            int tmp = (int)ans;
            res += (tmp + '0');
            resPart = ans - tmp;
        }

        return res;
    }
    
    string binaryRepresentation(string n) {
        // wirte your code here
        size_t pos = n.find_first_of(".");
        int intPart = 0;
        double resPart = 0.0;
        
        if(pos == string::npos) {
            intPart = atoi(n.substr(0, pos).c_str());
        } else {
            intPart = atoi(n.substr(0, pos).c_str());
            resPart = atof(n.substr(pos).c_str());
        }
        string res;
        if(resPart == 0.0) {
            res = getInt(intPart);
        } else {
            res = getInt(intPart);
            res += ".";
            string resStr = getDouble(resPart);
            if(resStr.size() > 32) return "ERROR";
            res += resStr;
        }
        
        return res;
    }
};
```
## 二叉树中搜索区间
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
     * @param k1 and k2: range k1 to k2.
     * @return: Return all keys that k1<=key<=k2 in ascending order.
     */
    
    void inorder(TreeNode* root, int k1, int k2, vector<int>& res) {
        if(root == NULL) return ;
        if(root->left) {
            inorder(root->left, k1, k2, res);
        }
        if(root->val >= k1 && root->val <= k2) {
            res.push_back(root->val);
        }
        if(root->right) {
            inorder(root->right, k1, k2, res);
        }
    }
    
    vector<int> searchRange(TreeNode* root, int k1, int k2) {
        // write your code here
        vector<int> res;
        inorder(root, k1, k2, res);
        return res;
    }
};
```
## 不同的二叉查找树
```C++
class Solution {
public:
    /**
     * @paramn n: An integer
     * @return: An integer
     */
    int numTrees(int n) {
        // write your code here
        int dp[n+1];  
        fill(dp,dp+n+1,0);  
        dp[0]=dp[1]=1;    
         
        for(int i=2;i<=n;i++)    
            for(int j=1;j<=i;j++)    
                dp[i]+= dp[j-1]*dp[i-j];    
          
        return dp[n];
    }
};
```
## 接雨水
```C++
class Solution {
public:
    /**
     * @param heights: a vector of integers
     * @return: a integer
     */
    int trapRainWater(vector<int> &heights) {
        // write your code here
        int res = 0;
        if(heights.size() == 0) return res;
        int l=0, r=heights.size()-1;
        int lmax=0, rmax=0;
        while(l < r) {
            lmax = max(lmax, heights[l]);
            rmax = max(lmax, heights[r]);
            if(lmax < rmax) {
                res += (lmax - heights[l++]);
            } else {
                res += (rmax - heights[r--]);
            }
        }
        return res;
    }
};
```
