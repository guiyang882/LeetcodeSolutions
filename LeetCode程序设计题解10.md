## 最长上升子序列
```C++
class Solution {
public:
    /**
     * @param nums: The integer array
     * @return: The length of LIS (longest increasing subsequence)
     */
    int longestIncreasingSubsequence(vector<int> nums) {
        // write your code here
        int len = nums.size();
        if(len == 0) return 0;
        
        vector<int> dp(len, 0);
        for(int i=0;i<len;i++) {
            dp[i] = 1;
        }
        for(int i=1;i<len;i++) {
            for(int j=0;j<i;j++) {
                if(nums[j] < nums[i]) {
                    dp[i] = max(dp[i], dp[j] + 1);
                }
            }
        }
        return dp[len - 1];
    }
};

```

## 拓扑排序
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
     * @return: Any topological order for the given graph.
     */
    vector<DirectedGraphNode*> topSort(vector<DirectedGraphNode*> graph) {
        // write your code here
        vector<DirectedGraphNode*> res;
        if(graph.empty()) {
            return res;
        }
        map<DirectedGraphNode*, int> hash;
        for(auto item : graph) {
            if(hash.find(item) == hash.end()) {
                hash[item] = 0;
            }
            for(auto cell : item->neighbors) {
                hash[cell]++;
            }
        }
        stack<DirectedGraphNode*> s;
        for(auto item : hash) {
            if(item.second == 0) {
                s.push(item.first);
                //cout << "stack push !" << endl;
            }
        }
        
        while(!s.empty()) {
            DirectedGraphNode* tmp = s.top();
            s.pop();
            res.push_back(tmp);
            for(auto cell : tmp->neighbors) {
                hash[cell]--;
                if(hash[cell] == 0) {
                    s.push(cell);
                }
            }
        }
        return res;
    }
};
```

## 最长回文子串
```C++
class Solution {
public:
    /*
     * @param s: input string
     * @return: the longest palindromic substring
     */
    string longestPalindrome(string s) {
        // write your code here
        int len = s.size();
        if(len <= 1) return s;
        vector<vector<bool>> dp(1000, vector<bool>(1000, false));
        for(int i=0;i<1000;i++) {
            dp[i][i] = true;
        }
        int startIdx = 0, maxlen = 0;
        for(int i=0;i<len-1;i++) {
            if(s[i] == s[i+1]) {
                dp[i][i+1] = true;
                startIdx = i;
                maxlen = 2;
            }
        }
        for(int k=3; k<=len; k++) {
            for(int i=0;i<len-k+1;i++) {
                int j = i+k-1;
                if(s[i] == s[j] && dp[i+1][j-1]) {
                    dp[i][j] = true;
                    maxlen = k;
                    startIdx = i;
                }
            }
        }
        if(maxlen >= 2) {
            return s.substr(startIdx, maxlen);
        }
        return "";
    }
};
```
