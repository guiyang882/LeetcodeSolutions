## Super Washing Machines
```C++
class Solution {
public:
    int findMinMoves(vector<int>& machines) {
        int N = machines.size();
        vector<int> accsum(N+1, 0);
        for(int i=0;i<N;i++) {
            accsum[i+1] = accsum[i] + machines[i];
        }
        if(accsum[N] % N) return -1;
        int avg = accsum[N] / N;
        int res = 0;
        for(int i=0;i<N;i++) {
            // l or r == required dresses - contained dresses
            int l = i * avg - accsum[i];
            int r = (N - i - 1) * avg - (accsum[N] - accsum[i] - machines[i]);
            int tmp = 0;
            if(l>0 && r>0) { // both lack dresses
                tmp = abs(l) + abs(r);
            } else if(l<0 && r<0) { // both have too much dresses
                tmp = max(abs(l), abs(r));
            } else { // one side lack dresses, the other side contain dresses
                tmp = max(abs(l), abs(r));
            }
            if(res < tmp) {
                res = tmp;
            }
        }
        return res;
    }
};
```
## 最长回文字串
```C++
int longestPalindrome(string str) {
    /*
    dp[i][j] = 1, if(i == j)
    dp[i][j] = 1, if(j == i + 1 && s[i] == s[j])
    dp[i][j] = 1, if(j > i + 1 && s[i] == s[j] && dp[i+1][j-1])
     */
    // int N = str.size(), left = 0, right = 0, len = 0;
    // vector<vector<bool>> dp(N, vector<bool>(N, false));
    // for(int i=0;i<N;i++) {
    //     dp[i][i] = true;
    //     for(int j=0;j<i;j++) {
    //         dp[j][i] = (str[j] == str[i] && (i-j<2 || dp[j+1][i-1]));
    //         if(dp[j][i] && len < i-j+1) {
    //             left = j;
    //             right = i;
    //             len = i - j + 1;
    //         }
    //     }
    // }
    // return len;
    
    /*
    find the center of the paliindrome and find the radius
    */
    int N = str.size(), maxLen = 0, tmpLen = 0;
    for(int c=0;c<N;c++) {
        for(int r=0;(c-r >= 0 && c+r<N);r++) {
            if(str[c-r] != str[c+r]) break;
            tmpLen = r * 2 + 1;
        }
        if(tmpLen > maxLen) maxLen = tmpLen;
        for(int r=0;(c-r >= 0 && c+r+1 < N);r++) {
            if(str[c-r] != str[c+r+1]) break;
            tmpLen = r * 2 + 2;
        }
        if(tmpLen > maxLen) maxLen = tmpLen;
    }
    return maxLen;
}
```
## 正则表达式匹配
```
#include <iostream>
#include <vector>
#include <functional>
#include <algorithm>

using namespace std;

class Solution {
public:
    bool match(char* str, char* pattern) {
        if(str == NULL || pattern == NULL) {
            return false;
        }
        return matchCore(str, pattern);
    }
    bool matchCore(char *str, char* pattern) {
        if(*str == '\0' && *pattern == '\0') {
            return true;
        }
        if(*str != '\0' && *pattern == '\0') {
            return false;
        }
        if(*(pattern+1) == '*') {
            if(*pattern == *str || (*pattern == '.' && *str != '\0')) {
                return matchCore(str+1, pattern) || matchCore(str, pattern+2);
            } else {
                return matchCore(str, pattern+2);
            }
        }
        if(*str == *pattern || (*pattern == '.' && *str != '\0')) {
            return matchCore(str+1, pattern+1);
        }
        return false;
    }
};

int main () {
	Solution obj;
    char str[] = "ab";
    char pattern[] = "a*";
	cout << obj.match(str, pattern) << endl;
    return 0;
}
```
## 维护数组X的秩
```C++
template <typename T>
class BTreeNode {
private:
    BTreeNode<T> *_leftChild, *_rightChild;
    T ele;
	int leftsize;
    
public:
    BTreeNode(const T &e) {
        ele = e;
        _leftChild = _rightChild = NULL;
        leftsize = 0;
    }

    void insert(const T &e) {
        if(e <= ele) {
            if(_leftChild) {
                _leftChild->insert(e);
            } else {
                _leftChild = new BTreeNode(e);
            }
            leftsize++;
        } else {
            if(_rightChild) {
                _rightChild->insert(e);
            } else {
                _rightChild = new BTreeNode(e);
            }
        }
    }
    
    int getRank(const T &e) {
        if(e == ele) {
            return leftsize;
        }
        if(e < ele) {
            return _leftChild->getRank(e);
        }
        if(e > ele) {
            return leftsize + 1 +  _rightChild->getRank(e);
        }
        return 0;
    }
};
class Rank {
public:
    BTreeNode<int>* root;
    int helper(int val) {
        if(root == NULL) {
            root = new BTreeNode<int>(val);
        } else {
            root->insert(val);
        }
        return root->getRank(val);
    }
    
    vector<int> getRankOfNumber(vector<int> A, int n) {
        // write code here
        root = NULL;
        vector<int> res(n, 0);
        for(int i=0;i<A.size();i++) {
            res[i] = helper(A[i]);
        }
        return res;
    }
};
```
## 01翻转问题
```
牛牛正在挑战一款名为01翻转的游戏。游戏初始有A个0,B个1，牛牛的目标就是把所有的值都变为1，每次操作牛牛可以任意选择恰好K个数字，并将这K个数字的值进行翻转(0变为1，1变为0)。牛牛如果使用最少的操作次数完成这个游戏就可以获得奖品，牛牛想知道最少的操作次数是多少？
例如:
	A = 4 B = 0 K = 3 
	0000 -> 1110 -> 1001 -> 0100 -> 1111 
	需要的最少操作次数为4

```

```bash
struct node {
    int num_a, num_b, step;
    node() {
        step = 0;
    }
};

bool status[200005] = {false};

void helper(int a, int b , int k) {
    if(a == 0) {
        cout << 0 << endl;
        continue;
    }
    if(a == k) {
        cout << 1 << endl;
        continue;
    }
    if(a + b < k) {
        cout << -1 << endl;
        continue;
    }

    queue<node> q;
    while(!q.empty()) q.pop();
    node cur, tem;
    cur.num_a = a, cur.num_b = b;
    memset(status, false, sizeof(status));
    q.push(cur);
    bool flag = false;
    while(!q.empty()) {
        cur = q.front();
        q.pop();
        if(cur.num_a == 0) {
            cout << cur.step << endl;
            flag = true;
            break;
        }
        for(int i=k;i>0;i--) {
            node tmp = cur;
            if(tmp.num_a >= i && tmp.num_b >= (k-i)) {
                tmp.num_a += (k - 2 * i);
                tmp.num_b -= (k - 2 * i);
                tmp.step++;
                if(status[tmp.num_a] == false) {
                    status[tmp.num_a] = true;
                    q.push(tmp);
                }
            }
        }
    }
    if(flag == false) {
        cout << -1 << endl;
    }
}
```
## Word Ladder
```bash
For example,

Given:
beginWord = "hit"
endWord = "cog"
wordList = ["hot","dot","dog","lot","log","cog"]
As one shortest transformation is "hit" -> "hot" -> "dot" -> "dog" -> "cog",
return its length 5.
```

```C++
class Solution {
public:
    int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
        unordered_set<string> wordDict;
        for(int i=0;i<wordList.size();i++) {
            wordDict.insert(wordList[i]);
        }
        // wordDict.insert(endWord);
        queue<string> toVisit;
        addNextWords(beginWord, wordDict, toVisit);
        int dist = 2;
        while (!toVisit.empty()) {
            int num = toVisit.size();
            for (int i = 0; i < num; i++) {
                string word = toVisit.front();
                toVisit.pop();
                if (word == endWord) return dist;
                addNextWords(word, wordDict, toVisit);
            }
            dist++;
        }
        return 0;
    }
private:
    void addNextWords(string word, unordered_set<string>& wordDict, queue<string>& toVisit) {
        wordDict.erase(word);
        for (int p = 0; p < (int)word.length(); p++) {
            char letter = word[p];
            for (int k = 0; k < 26; k++) { 
                word[p] = 'a' + k;
                if (wordDict.find(word) != wordDict.end()) {
                    toVisit.push(word);
                    wordDict.erase(word);
                }
            }
            word[p] = letter;
        } 
    } 
};
```
## Sliding Windows Maximum
```c++
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& A, int k) {
        int N = A.size();
        vector<int> res;
        deque<int> dp;
        for(int i=0;i<N;i++) {
            while(!dp.empty() && dp.back() < A[i]) dp.pop_back();
            dp.push_back(A[i]);
            if(i>=k-1) {
                res.push_back(dp.front());
                if(A[i-k+1] == dp.front()) dp.pop_front();
            }
        }
        return res;
    }
};
```
## Repeated Substring Pattern
```
Given a non-empty string check if it can be constructed by taking a substring of it and appending multiple copies of the substring together. You may assume the given string consists of lowercase English letters only and its length will not exceed 10000.

Input: "abab"
Output: True
Explanation: It's the substring "ab" twice.

Input: "aba"
Output: False

Input: "abcabcabcabc"
Output: True
Explanation: It's the substring "abc" four times. (And the substring "abcabc" twice.)
```

## Assign Cookies
```
Input: [1,2,3], [1,1]
Output: 1
Explanation: You have 3 children and 2 cookies. The greed factors of 3 children are 1, 2, 3. 
And even though you have 2 cookies, since their size is both 1, you could only make the child whose greed factor is 1 content.
You need to output 1.

Input: [1,2], [1,2,3]
Output: 2
Explanation: You have 2 children and 3 cookies. The greed factors of 2 children are 1, 2. 
You have 3 cookies and their sizes are big enough to gratify all of the children, 
You need to output 2.
```

```C++
class Solution {
public:
    int findContentChildren(vector<int>& g, vector<int>& s) {
        sort(g.begin(), g.end());
        sort(s.begin(), s.end());
        int sindex = 0, cnt = 0;
        for(int i=0;i<g.size();i++) {
            while(sindex<s.size()) {
                if(s[sindex++] >= g[i]) {
                    cnt++;
                    break;
                }
            }
        }
        return cnt;
    }
};
```

## 链表整数求和
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
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        vector<int> nums1, nums2;
        while(l1) {
            nums1.push_back(l1->val);
            l1 = l1->next;
        }
        while(l2) {
            nums2.push_back(l2->val);
            l2 = l2->next;
        }
        int n1 = nums1.size(), n2 = nums2.size();
        ListNode* head = NULL, *p = NULL;
        int sum = 0, carray = 0;
        for(int i=n1-1, j=n2-1; i>=0 || j>=0 || carray>0; i--,j--) {
            sum = carray;
            if(i>=0) {
                sum += nums1[i];
            }
            if(j>=0) {
                sum += nums2[j];
            }
            carray = sum / 10;
            p = new ListNode(sum%10);
            p->next = head;
            head = p;
        }
        return head;
    }
};
```
## Complex Number Multiplication
```c++
class LComplex {
public:
    explicit LComplex(const string & a) {
        m_complex = a;
        
        realpart = 0;
        imagepart = 0;
        
        int ind = a.find("+");
        string r1 = a.substr(0, ind);
        string r2 = a.substr(ind+1);
        realpart = stoi(r1);
        imagepart = stoi(r2.substr(0, r2.size()-1));
    }
    
    friend string operator*(const LComplex& a, const LComplex& b) {
        int n_real = b.realpart * a.realpart - b.imagepart * a.imagepart;
        int n_image = b.realpart * a.imagepart + b.imagepart * a.realpart;
        string res = to_string(n_real) + "+" + to_string(n_image) + "i";
        return res;
    }
    
public:
    string m_complex;
    int realpart;
    int imagepart;
};

class Solution {
public:
    string complexNumberMultiply(string a, string b) {
        LComplex ca(a);
        LComplex cb(b);
        string res = ca * cb;
        return res;
    }
};
```
## Number Complement
```c++
class Solution {
public:
    int findComplement(int num) {
        unsigned int mask = ~0;
        while((mask & num)) mask <<= 1;
        return ~mask & ~num;
    }
};
```

## Construct String from Binary
```bash
Input: Binary tree: [1,2,3,4]
       1
     /   \
    2     3
   /    
  4     

Output: "1(2(4))(3)"

Explanation: Originallay it needs to be "1(2(4)())(3()())", 
but you need to omit all the unnecessary empty parenthesis pairs. 
And it will be "1(2(4))(3)".

Input: Binary tree: [1,2,3,null,4]
       1
     /   \
    2     3
     \  
      4 

Output: "1(2()(4))(3)"

Explanation: Almost the same as the first example, 
except we can't omit the first parenthesis pair to break the one-to-one mapping relationship between the input and the output.
```

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    string tree2str(TreeNode* t) {
        if(t == NULL) return "";
        
        string s = to_string(t->val);
        if(t->left) {
            s += ("(" + tree2str(t->left) + ")");
        } else if(t->right) {
            s += "()";
        }
        if(t->right) {
            s += ("(" + tree2str(t->right) + ")");
        }
        return s;
    }
};
```

## System Longest File Path
Suppose we abstract our file system by a string in the following manner:

The string "dir\n\tsubdir1\n\tsubdir2\n\t\tfile.ext" represents:

```
dir
    subdir1
    subdir2
        file.ext
The directory dir contains an empty sub-directory subdir1 and a sub-directory subdir2 containing a file file.ext.
```

The string

```
"dir\n\tsubdir1\n\t\tfile1.ext\n\t\tsubsubdir1\n\tsubdir2\n\t\tsubsubdir2\n\t\t\tfile2.ext"
represents:

dir
    subdir1
        file1.ext
        subsubdir1
    subdir2
        subsubdir2
            file2.ext
```

The directory dir contains two sub-directories subdir1 and subdir2. subdir1 contains a file file1.ext and an empty second-level sub-directory subsubdir1. subdir2 contains a second-level sub-directory subsubdir2 containing a file file2.ext.

We are interested in finding the longest (number of characters) absolute path to a file within our file system. For example, in the second example above, the longest absolute path is "dir/subdir2/subsubdir2/file2.ext", and its length is 32 (not including the double quotes).

Given a string representing the file system in the above format, return the length of the longest absolute path to file in the abstracted file system. If there is no file in the system, return 0.
```
class Solution:
    # @param {string} input an abstract file system
    # @return {int} return the length of the longest absolute path to file
    def lengthLongestPath(self, input):
        # Write your code here
        if len(input) == 0:
            return 0
        inputs = input.strip().split("\n")
        if len(inputs) == 1:
            return 0
        info = list()
        file_list = list()
        for i in range(len(inputs)):
            if "\t" not in inputs[i]:
                info.append([inputs[i], 0, -1])
                if "." in inputs[i]:
                    file_list.append(len(info) - 1)
                continue
            cells = inputs[i].split("\t")
            info.append([cells[-1], len(cells) - 1])
            if "." in cells[-1]:
                file_list.append(len(info) - 1)
        # print(info, len(info))
        for i in range(len(info)-1, 0, -1):
            target = info[i][-1] - 1
            for j in range(i-1, -1, -1):
                # print(info[j][1], target)
                if info[j][1] == target:
                    info[i].append(j)
                    break
        # print(info)
        # print(file_list)
        file_len = list()
        for idx in file_list:
            t_len, pos = info[idx][1], idx
            while pos >= 0:
                t_len += len(info[pos][0])
                pos = info[pos][-1]
                # print(pos)
            file_len.append(t_len)
        # print(t_len)
        return max(file_len)
```

## K组链表反转

```
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
     * @param k an integer
     * @return a ListNode
     */
    int getlen(ListNode* head) {
        int n = 0;
        while(head) {
            head = head->next;
            n++;
        }
        return n;
    }
    ListNode *reverseKGroup(ListNode *head, int k) {
        // Write your code here
        ListNode* cur = head;
        ListNode *next = nullptr, *pre = nullptr;
        int cnt = 0;
        if(getlen(head) < k)
            return cur;
        while(cur && cnt < k) {
            next = cur->next;
            cur->next = pre;
            pre = cur;
            cur = next;
            cnt++;
        }
        if(next) {
            head->next = reverseKGroup(next, k);
        }
        return pre;
    }
};
```

## Frog Jump
```C++
class Solution {
public:
    /**
     * @param stones a list of stones' positions in sorted ascending order
     * @return true if the frog is able to cross the river or false
     */
    bool canCross(vector<int>& stones) {
        // Write your code here
        int k = 0;
        bool flag = helper(stones, 0, k);
        return flag;
    }
    
    bool helper(vector<int> &stones, int idx, int k) {
        int len = stones.size();
        if(idx == len - 1) return true;
        for(int i=max(0,k-1);i<=k+1;i++) {
            int nextJump = stones[idx] + i;
            int nextPos = -1;
            for(int j=idx+1;j<len;j++) {
                if(stones[j] == nextJump) {
                    nextPos = j;
                    break;
                }
            }
            if(nextPos > 0) {
                bool f = helper(stones, nextPos, i);
                if(f) return true;
            }
        }
        return false;
    }
};
```

```C++
class Solution {
public:
    /**
     * @param stones a list of stones' positions in sorted ascending order
     * @return true if the frog is able to cross the river or false
     */
    bool canCross(vector<int>& stones) {
        // Write your code here
        int k = 0;
        map<pair<int, int>, bool> hash;
        bool flag = helper(stones, 0, k, hash);
        return flag;
    }
    
    bool helper(vector<int> &stones, int idx, int k, map<pair<int, int>, bool> &hash) {
        int len = stones.size();
        if(idx == len - 1) return true;
        for(int i=max(0,k-1);i<=k+1;i++) {
            int nextJump = stones[idx] + i;
            int nextPos = -1;
            for(int j=idx+1;j<len;j++) {
                if(stones[j] == nextJump) {
                    nextPos = j;
                    break;
                }
            }
            if(nextPos > 0) {
                if(hash.find(make_pair(nextPos, i)) == hash.end()) {
                    bool f = helper(stones, nextPos, i, hash);
                    hash[make_pair(nextPos, i)] = f;
                }
                
                if(hash[make_pair(nextPos, i)]) return true;
            }
        }
        return false;
    }
};
```
