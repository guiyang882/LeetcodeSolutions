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
