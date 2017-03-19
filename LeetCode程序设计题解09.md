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
