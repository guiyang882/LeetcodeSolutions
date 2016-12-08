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
