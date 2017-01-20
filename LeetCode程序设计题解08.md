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
