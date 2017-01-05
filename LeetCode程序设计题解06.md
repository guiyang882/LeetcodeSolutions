##  交换链表当中两个节点
给你一个链表以及两个权值v1和v2，交换链表中权值为v1和v2的这两个节点。保证链表中节点权值各不相同，如果没有找到对应节点，那么什么也不用做。

```c++
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
     * @oaram v1 an integer
     * @param v2 an integer
     * @return a new head of singly-linked list
     */
    ListNode* swapNodes(ListNode* head, int v1, int v2) {
        // Write your code here
        if(head == NULL) return NULL;
        
        ListNode* root = new ListNode(-1);
        root->next = head;
        ListNode* pre1 = root, *cur1 = head;
        while(cur1) {
            if(cur1->val == v1 || cur1->val == v2) {
                break;
            }
            pre1 = cur1;
            cur1 = cur1->next;
        }
        if(cur1 == NULL) {
            delete root;
            return head;
        }
        
        ListNode* pre2 = cur1, *cur2 = cur1->next;
        if(cur1->val == v1) {
            while(cur2) {
                if(cur2->val == v2) {
                    break;
                }
                pre2 = cur2;
                cur2 = cur2->next;
            }
        } else {
            while(cur2) {
                if(cur2->val == v1) {
                    break;
                }
                pre2 = cur2;
                cur2 = cur2->next;
            }
        }
        if(cur2 == NULL) {
            delete root;
            return head;
        }
        
        if(pre2 == cur1) {
            pre1->next = cur2;
            cur1->next = cur2->next;
            cur2->next = cur1;
        } else {
            pre1->next = cur2;
            ListNode* tmp = cur1->next;
            cur1->next = cur2->next;
            pre2->next = cur1;
            cur2->next = tmp;
            pre1->next = cur2;
        }
        
        head = root->next;
        delete root;
        return head;
    }
};
```

## N皇后问题
```C++
class Solution {
public:
    /**
     * Get all distinct N-Queen solutions
     * @param n: The number of queens
     * @return: All distinct solutions
     * For example, A string '...Q' shows a queen on forth position
     */
    void convert2board(vector<int> &board, vector<string> &output) {
        int N = board.size();
        for(int i=0;i<N;i++) {
            string res;
            for(int j=0;j<N;j++) {
                if(board[i] == j) {
                    res += 'Q';
                } else {
                    res += '.';
                }
            }
            output.push_back(res);
        }
    }
    
    bool canPlace(const vector<int> &board, int row, int col) {
        for(int i=0;i<row && i<board.size();i++) {
            if(board[i] == col || abs(board[i] - col) == abs(i - row)) {
                return false;
            }
        }
        return true;
    }
    
    vector<vector<string> > solveNQueens(int n) {
        // write your code here
        vector<vector<string> > results;
        
        vector<int> board(n,-1);
        int row=0,col=0;
        while(row<n) {
            while(col<n) {
                if(canPlace(board, row, col)) {
                    board[row] = col;
                    col = 0;
                    break;
                } else {
                    col++;
                }
            }
            
            if(board[row] == -1) {
                if(row == 0) {
                    break;
                } else {
                    row --;
                    col = board[row]+1;
                    board[row] = -1;
                    continue;
                }
            }
            
            if(row == n-1) {
                vector<string> output;
                convert2board(board, output);
                results.push_back(output);
                
                col = board[row]+1;
                board[row] = -1;
                continue;
            }
            
            row++;
        }
        
        return results;
    }
};
```

## 摆动排序
给你一个没有排序的数组，请将原数组就地重新排列满足如下性质
nums[0] <= nums[1] >= nums[2] <= nums[3]....

```C++
class Solution {
public:
    /**
     * @param nums a list of integer
     * @return void
     */  
    void wiggleSort(vector<int>& nums) {
        // Write your code here
        if (nums.size() <= 1) return;
        for (int i = 1; i < nums.size(); ++i) {
            if ((i % 2 == 1 && nums[i] < nums[i - 1]) || (i % 2 == 0 && nums[i] > nums[i - 1])) {
                swap(nums[i], nums[i - 1]);
            }
        }
    }
};
```

## 线段树的构造
```C++
/**
 * Definition of SegmentTreeNode:
 * class SegmentTreeNode {
 * public:
 *     int start, end;
 *     SegmentTreeNode *left, *right;
 *     SegmentTreeNode(int start, int end) {
 *         this->start = start, this->end = end;
 *         this->left = this->right = NULL;
 *     }
 * }
 */
class Solution {
public:
    /**
     *@param start, end: Denote an segment / interval
     *@return: The root of Segment Tree
     */
    SegmentTreeNode * build(int start, int end) {  
        if(start>end)  
            return nullptr;  
          
        SegmentTreeNode *root= new SegmentTreeNode(start,end);  
        if(start!=end){  
            int mid=start+(end-start)/2;  
            root->left=build(start,mid);  
            root->right=build(mid+1,end);  
        }  
        return root;  
    }  
};
```

## 线段树的构造II
```C++
/**
 * Definition of SegmentTreeNode:
 * class SegmentTreeNode {
 * public:
 *     int start, end, max;
 *     SegmentTreeNode *left, *right;
 *     SegmentTreeNode(int start, int end, int max) {
 *         this->start = start;
 *         this->end = end;
 *         this->max = max;
 *         this->left = this->right = NULL;
 *     }
 * }
 */
class Solution {
public:
    /**
     *@param A: a list of integer
     *@return: The root of Segment Tree
     */
    int findMaxElem(vector<int> &A, int left, int right) {
        int maxV = INT_MIN;
        for(int i=left;i<=right;i++) {
            if(maxV < A[i]) {
                maxV = A[i];
            }
        }
        return maxV;
    }
    
    SegmentTreeNode* helper(vector<int> &A, int left, int right) {
        if(left > right) {
            return NULL;
        }
        if(left == right) {
            SegmentTreeNode* node = new SegmentTreeNode(left, right, A[left]);
            return node;
        }
        SegmentTreeNode* root = new SegmentTreeNode(left, right, findMaxElem(A, left, right));
        int mid = (left + right) / 2;
        SegmentTreeNode* node01 = helper(A, left, mid);
        root->left = node01;
        SegmentTreeNode* node02 = helper(A, mid+1, right);
        root->right = node02;
        return root;
    }
    
    SegmentTreeNode * build(vector<int>& A) {
        // write your code here
        int left = 0, right = A.size()-1;
        if(right < left) return NULL;
        return helper(A, left, right);
    }
};
```
## 线段树的查询
```C++
/**
 * Definition of SegmentTreeNode:
 * class SegmentTreeNode {
 * public:
 *     int start, end, max;
 *     SegmentTreeNode *left, *right;
 *     SegmentTreeNode(int start, int end, int max) {
 *         this->start = start;
 *         this->end = end;
 *         this->max = max;
 *         this->left = this->right = NULL;
 *     }
 * }
 */
class Solution {
public:
    /**
     *@param root, start, end: The root of segment tree and 
     *                         an segment / interval
     *@return: The maximum number in the interval [start, end]
     */
    int query(SegmentTreeNode *root, int start, int end) {
        // write your code here
        if(root == NULL) return INT_MIN;
        
        int left = root->start, right = root->end;
        if(start <= left && end >= right) {
            return root->max;
        }
        int mid = (left + right) / 2;
        if(mid < start) {
            // right
            return query(root->right, start, end);
        } else if(mid + 1 > end) {
            // left
            return query(root->left, start, end);
        } else {
            // mid
            return max(query(root->left, start, mid), query(root->right, mid+1, end));
        }
    }
};
```
## 线段树的修改
```C++
/**
 * Definition of SegmentTreeNode:
 * class SegmentTreeNode {
 * public:
 *     int start, end, max;
 *     SegmentTreeNode *left, *right;
 *     SegmentTreeNode(int start, int end, int max) {
 *         this->start = start;
 *         this->end = end;
 *         this->max = max;
 *         this->left = this->right = NULL;
 *     }
 * }
 */
class Solution {
public:
    /**
     *@param root, index, value: The root of segment tree and 
     *@ change the node's value with [index, index] to the new given value
     *@return: void
     */
    void modify(SegmentTreeNode *root, int index, int value) {
        // write your code here
        if(root == NULL) return ;
        
        int left = root->start, right = root->end;
        if(left <= index && right >= index) {
            if(left == right) {
                root->max = value;
            } else {
                modify(root->left,index,value);  
                modify(root->right,index,value);
                root->max = max(root->left->max, root->right->max);
            }
        }
    }
};
```
## 线段树查询统计
```C++
/**
 * Definition of SegmentTreeNode:
 * class SegmentTreeNode {
 * public:
 *     int start, end, count;
 *     SegmentTreeNode *left, *right;
 *     SegmentTreeNode(int start, int end, int count) {
 *         this->start = start;
 *         this->end = end;
 *         this->count = count;
 *         this->left = this->right = NULL;
 *     }
 * }
 */
class Solution {
public:
    /**
     *@param root, start, end: The root of segment tree and 
     *                         an segment / interval
     *@return: The count number in the interval [start, end] 
     */
    int query(SegmentTreeNode *root, int start, int end) {
        // write your code here
        if(start > end || root == NULL) return 0;
        
        int left = root->start, right = root->end;
        if(start <= left && end >= right) {
            return root->count;
        }
        int mid = (left + right) / 2;
        int lcnt = 0, rcnt = 0;
        if(mid >= start) {
            lcnt = query(root->left, start, min(mid, end));
        }
        if(mid < end) {
            rcnt = query(root->right, mid>start?++mid:start, end);
        }
        return lcnt + rcnt;
    }
};
```
## 带最小值的栈
```C++
class MinStack {
public:
    MinStack() {
        // do initialization if necessary
        A.clear();
    }

    void push(int number) {
        // write your code here
        if(A.empty()) {
            A.push_back(number);
            B.push(0);
        } else {
            A.push_back(number);
            if(number < A[B.top()]) {
                B.push(A.size()-1);
            }
        }
    }

    int pop() {
        // write your code here
        if(B.top() == A.size()-1) {
            B.pop();
        }
        int tmp = A.back();
        A.pop_back();
        return tmp;
    }

    int min() {
        // write your code here
        if(!B.empty()) {
            return A[B.top()];
        }
    }
    
private:
    stack<int> B;
    vector<int> A;
};
```
## 螺旋矩阵
```C++
class Solution {
public:
    /**
     * @param matrix a matrix of m x n elements
     * @return an integer array
     */
    void helper01(int index, int width, int height, vector<vector<int>>& matrix) {
        if(width <= 0 || height <= 0) return ;
        for(int i=0;i<width;i++) {
            res.push_back(matrix[index][index + i]);
        }
        for(int i=1;i<height;i++) {
            res.push_back(matrix[index + i][index + width - 1]);
        }
        for(int i=width-2;i>=0;i--) {
            res.push_back(matrix[index + height - 1][index + i]);
        }
        for(int i=height-2;i>0;i--) {
            res.push_back(matrix[index + i][index]);
        }
        helper01(index + 1, width - 2, height - 2, matrix);
    }
    
    void helper02(int upr, int upc, int downr, int downc, vector<vector<int>>& matrix) {
        while(upr <= downr && upc <= downc) {
            int i=upr, j = upc;
            while(j<=downc) {
                res.push_back(matrix[i][j++]);
            }
            j--,i++;
            while(i<=downr) {
                res.push_back(matrix[i++][j]);
            }
            i--,j--;
            while(j>upc && upr != downr) {
                res.push_back(matrix[i][j--]);
            }
            while(i>upr && upc != downc) {
                res.push_back(matrix[i--][j]);
            }
            upr++,upc++,downr--,downc--;
        }
    }
    
    vector<int> spiralOrder(vector<vector<int>>& matrix) {
        // Write your code here
        int row = matrix.size();
        if(row<=0) return res;
        int col = matrix[0].size();
        
        // helper01(0, row, col, matrix);
        helper02(0, 0, row-1, col-1, matrix);
        
        return res;
    }
    
private:
    vector<int> res;
};
```
## 螺旋矩阵II
```C++
class Solution {
public:
    /**
     * @param n an integer
     * @return a square matrix
     */
    vector<vector<int>> generateMatrix(int n) {
        // Write your code here
        vector<vector<int>> matrix(n, vector<int>(n, 1));
        if(n == 0 || n == 1) return matrix;
        int circle = 0, curnum = 1;
        while(circle <= (n-1)/2) {
            int i=circle, j=circle;
            matrix[i][j] = curnum++;
            while(j < n-i-1) {
                matrix[i][++j] = curnum++;
            }
            
            while(i < j) {
                matrix[++i][j] = curnum++;
            }
            
            while(j > n-i-1) {
                matrix[i][--j] = curnum++;
            }
            
            while(i > j+1) {
                matrix[--i][j] = curnum++;
            }
            circle++;
        }
        return matrix;
    }
};
```

## 寻找丑数II
```C++
class Solution {
public:
    /*
     * @param n an integer
     * @return the nth prime number as description.
     */
    int nthUglyNumber(int n) {
        // write your code here
        vector<int> res(n,1);
        if(n <= 6) return n;
        int ind2 = 0, ind3 = 0, ind5 = 0;
        int cnt = 1;
        while(cnt < n) {
            int a2 = res[ind2] * 2;
            int a3 = res[ind3] * 3;
            int a5 = res[ind5] * 5;
            
            int minV = min(a2, min(a3, a5));
            if(minV == a2) {
                ind2++;
            }
            
            if(minV == a3) {
                ind3++;
            }
            
            if(minV == a5) {
                ind5++;
            }
            res[cnt++] = minV;
        }
        return res.back();
    }
};
```
## 统计数字
```python
class Solution:
    # @param k & n  two integer
    # @return ans a integer
    def digitCounts(self, k, n):
        cnt = 0
        for i in xrange(n+1):
            cnt += list(str(i)).count(str(k))
        return cnt
```

```C++
class Solution {
public:
    /*
     * param k : As description.
     * param n : As description.
     * return: How many k's between 0 and n.
     */
    int digitCounts(int k, int n) {
        // write your code here
        int cnt = 0;
        for(int i=0;i<=n;i++) {
            int target = i;
            if(target == 0 && k == 0) {
                cnt ++;
            } else {
                while(target) {
                    if(target % 10 == k) {
                        cnt++;
                    }
                    target /= 10;
                }
            }
        }
        return cnt;
    }
};
```

```C++
class Solution {
public:
    /*
     * param k : As description.
     * param n : As description.
     * return: How many k's between 0 and n.
     */
    int digitCounts(int k, int n) {
        // write your code here
        int res = 0, base = 1;
        if(n==0 && k==0)
            return 1;
            
        while(n/base>0) {
            int curBit = (n/base)%10;
            int low = n - (n/base)*base;
            int high = n/(base*10);
            // cout << "curBit: " << curBit << ", low: " << low << ", high: " << high << endl;
            if (curBit < k) {
                res += high * base;                         
            } else if (curBit == k) {  
                res += high*base+low+1;  
            } else {  
                if(k==0 && high==0){  
                      ;
                }else{  
                    res += (high+1)*base;  
                }  
            }  
            base *=10;
        }  
        return res;  
    }
};
```
