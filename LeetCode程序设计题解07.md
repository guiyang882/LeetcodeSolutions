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
