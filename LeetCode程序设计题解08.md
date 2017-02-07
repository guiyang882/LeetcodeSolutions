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
