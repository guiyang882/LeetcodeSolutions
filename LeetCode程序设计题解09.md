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
