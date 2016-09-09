# Leetcode算法设计题解(四)

@(程序设计算法)


[TOC]

## 实现组合问题
```c++
void combine(vector<int>& nums, int index, int k, vector<int> temp) {
    if(temp.size() == k) {
        cout << temp;
    } else {
        for(int i=index;i<nums.size();i++) {
            temp.push_back(nums[i]);
            combine(nums, index+1, k, temp);
            temp.pop_back();
        }
    }
}
```

## 实现排列问题
```c++
void perm(vector<int>& nums, int start, int end) {
    if(start == end) {
        for(int i=0;i<nums.size();i++) {
            cout << nums[i] << " ";
        }
    } else {
        for(int i=start;i<=end;i++) {
            swap(nums[start], nums[i]);
            perm(nums, start+1, end);
            swap(nums[i], nums[start]);
        }
    }
}
```

## 计算当前的一个状态时，下一个状态(排列)
- c++中的STL中已经实现了next_permutation() 和 pre_permutation()
- 我们自己实现这个算法过程
```c++
//具体的实现算法可以通过 (3,6,4,2) -> (4,6,3,2) -> (4,2,3,6)
void nextPermutation(vector<int> &num) {
    int length = num.size();
    if(length <= 1) return ;

    int begin = 0;
    for(int i=length-1; i>0; i--) {
        if(num[i] > num[i-1]) {
            begin = i;
            break;
        }
    }

    if(begin > 0) {
        int j = length-1;
        for(; j >= begin; j--)
            if(num[j] > num[begin-1]) break;
        swap(num[begin-1], num[j]);
    }
    int end = length-1;
    while(begin < end) {
        swap(num[begin], num[end]);
        begin++, end--;
    }
}
```

## 给定一个集合，给出一个序列，判断这个序列是第多少个
```c++
/*
和前一个问题的推导过程相反。例如3267514：

后6位的全排列为6!，3为{1, 2, 3 ,4 , 5, 6, 7}中第2个元素（从0开始计数），故2*720=1440；

后5位的全排列为5!，2为{1, 2, 4, 5, 6, 7}中第1个元素，故1*5!=120；

后4位的全排列为4!，6为{1, 4, 5, 6, 7}中第3个元素，故3*4!=72；

后3位的全排列为3!，7为{1, 4, 5, 7}中第3个元素，故3*3!=18；

后2位的全排列为2!，5为{1, 4, 5}中第2个元素，故2*2!=4；

最后2位为增序，因此计数0，求和得：1440+120+72+18+4=1654
*/
int calcSequenceNumber(string str) {
    //main to calculate the target's position in the given dict set
    vector<char> nums;
    for(auto c:str) {
        nums.push_back(c);
    }
    vector<int> fact(str.size()+1,0);
    fact[1] = 1;
    for(int i=2;i<fact.size();++i) {
        fact[i] = fact[i-1] * i;
    }
    sort(nums.begin(), nums.end());
    int total=0;
    for(auto c:str) {
        int index=0;
        for(;index<nums.size();++index) {
            if(c == nums[index]) {
                break;
            }
        }
        nums.erase(nums.begin()+index);
        total += (index * fact[nums.size()]);
    }
    return total;
}
```

## 给定一个集合，给出按照字典序列排列的第K个组合
```c++
/*
举例说明：如7个数的集合为{1, 2, 3, 4, 5, 6, 7}，要求出第n=1654个排列。

(1654 / 6!)取整得2，确定第1位为3，剩下的6个数{1, 2, 4, 5, 6, 7}，求第1654 % 6!=214个序列；

(214 / 5!)取整得1，确定第2位为2，剩下5个数{1, 4, 5, 6, 7}，求第214 % 5!=94个序列；

(94 / 4!)取整得3，确定第3位为6，剩下4个数{1, 4, 5, 7}，求第94 % 4!=22个序列；

(22 / 3!)取整得3，确定第4位为7，剩下3个数{1, 4, 5}，求第22 % 3!=4个序列；

(4 / 2!)得2，确定第5为5，剩下2个数{1, 4}；由于4 % 2!=0，故第6位和第7位为增序<1 4>；
*/
class Solution {
public:
    string getPermutation(int n, int k) {
        if(n <= 0) return "";
        vector<char> nums(n, '1');
        for(int i=1;i<nums.size();++i)
            nums[i] += i;

        vector<int> fact(n+1,0);
        fact[1] = 1;
        for(int i=2;i<=n;++i) {
            fact[i] = fact[i-1] * i;
        }
        
        if(k > fact[n]) return "";
        
        string str;
        k--;
        int index = n-1;
        while(index) {
            int tmp = k / fact[index];
            str += nums[tmp];
            nums.erase(nums.begin() + tmp);
            k %= fact[index];
            index--;
        }
        str += nums[0];
        return str;
    }
};

```

## 字符串分割
```c++
void splitStr(string str, vector<string>& res, char ch) {
    //trim the free space from the head and tail
    str.erase(0,str.find_first_not_of(" "));
    str.erase(str.find_last_not_of(" ") + 1);
    if(str.size() == 0) return ;
    int start=0, end=0;
    while(start <= end && end < str.size()) {
        while(end<str.size() && str[end]!=ch) end++;
        string sub = str.substr(start, end-start);
        res.push_back(sub);
        while(end<str.size() && str[end]==ch) end++;
        start = end;
    }
    for(auto i=0;i<res.size();i++) {
        cout << res[i] << endl;
    }
}
```

## 大数乘法
说明：
- 没有实现浮点数的乘法
```c++
class Solution {
public:
    string multiply(string num1, string num2) {
        if(num1.size() == 0 || num2.size() == 0) return "";
        if(num1.size() == 1 && num1[0] == '0') return "0";
        if(num2.size() == 1 && num2[0] == '0') return "0";
        
        int sign = 1;
        if(num1[0] == '-')  {
            sign *= -1;
            num1.erase(num1.begin());
        }
        
        if(num2[0] == '-') {
            sign *= -1;
            num2.erase(num2.begin());
        }
        
        int len1 = num1.size(), len2 = num2.size(), len = 0;
        len = len1 + len2;
        string res(len, '0');
        
        int rest = 0, front = 0;
        for(int i = len1-1; i >= 0; i--) {
            int tmp = num1[i] - '0';
            rest = 0, front = 0;
            for(int j = len2-1; j >= 0; j--) {
                int a = num2[j] - '0';
                int tmp_res = tmp * a + front;
                
                rest = tmp_res % 10;
                front = tmp_res / 10;
                rest = res[len - (len1 - i) - (len2 - j) + 1] - '0' + rest;
                front += (rest / 10);
                res[len - (len1 - i) - (len2 - j) + 1] = (rest % 10) + '0';
            }
            cout << front << endl;
            if(front > 0) {
                res[len - (len1 - i) - len2] = res[len - (len1 - i) - len2] - '0' + front + '0';
                front = 0;
            }
        }
        
        if(front >= 1) {
            res[len - len1] = res[len - len1] - '0' + front + '0';
        }
        while(res[0] == '0') {
            res.erase(res.begin());
        }
        if(sign == -1) {
            res.insert(res.begin(), '-');
        }
        return res;
    }
};
```

## 转置句子是保持单词的顺序不变
- 去掉句子的首尾的空格
```c++
class Solution {
public:
    void reverseWords(string &s) {
        if(s.size() == 0) return ;
        
        while(s.size() > 0 && s[0] == ' ') s.erase(s.begin());
        s.assign(s.rbegin(), s.rend());
        while(s.size() > 0 && s[0] == ' ') s.erase(s.begin());
        if(s.size() == 0 || s.size() == 1) return ;

        int start = 0, end = 0;
        while(end < s.size()) {
            while(start < s.size() && s[start] == ' ') start++;
            end = start;
            while(end < s.size() && s[end] != ' ') end++;
            string sub = s.substr(start, end - start);
            sub.assign(sub.rbegin(), sub.rend());
            s.replace(start, end-start, sub);
            start = end;
        }
    }
};
```
- 去掉单吃之间的多于的空格
```c++
class Solution {
public:
    void reverseWords(string &s) {
        if(s.size() == 0) return ;
        
        while(s.size() > 0 && s[0] == ' ') s.erase(s.begin());
        s.assign(s.rbegin(), s.rend());
        while(s.size() > 0 && s[0] == ' ') s.erase(s.begin());
        if(s.size() == 0 || s.size() == 1) return ;
        
        int start = 0, end = 0;
        while(end < s.size()) {
            while(start < s.size() && s[start] == ' ') s.erase(s.begin() + start);
            end = start;
            
            while(end < s.size() && s[end] != ' ') end++;
            string sub = s.substr(start, end - start);
            sub.assign(sub.rbegin(), sub.rend());
            s.replace(start, end-start, sub);
            end++;
            start = end;
        }
    }
};
```

