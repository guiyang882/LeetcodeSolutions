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
