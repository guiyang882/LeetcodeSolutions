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
