##1.[按奇偶性交换后的最大数字](https://leetcode-cn.com/problems/largest-number-after-digit-swaps-by-parity/)
**解题思路**:贪心，当碰到奇数时，往后找找有没有比它还大的奇数，碰到偶数是同理，往后找找有没有比他大的偶数。

Code:
```cpp

class Solution {
public:
    bool isodd(int k) {
        return k & 1;
    }
    int largestInteger(int num) {
        vector<int> data;
        while(num) {
            int tmp = num % 10;
            data.push_back(tmp);
            num /= 10;
        }
        for(int i = data.size() - 1; i >= 0; --i) {
            bool odd_flag = isodd(data[i]);
            for(int j = i - 1; j >= 0; --j) {
                if(odd_flag) {
                    if(isodd(data[j]) && data[j] > data[i]) {
                        swap(data[j], data[i]);
                    }
                } else {
                    if(!isodd(data[j]) && data[j] > data[i]) {
                        swap(data[j], data[i]);
                    }
                }
            }
        }
        int ans = 0;
        for(int i = data.size() - 1; i >= 0; --i) {
            ans = ans * 10 + data[i];
        }
        return ans;
    }
};
```

##2.[向表达式添加括号后的最小结果](https://leetcode-cn.com/problems/minimize-result-by-adding-parentheses-to-expression/)
**解题思路**：题目已经提到，我们的字符串的序列中只有一个+号，那么我们可以现进行一次遍历来找到+号的位置，然后枚举左括号和右括号的位置。这道题用c++有点难写，在比赛中我写了大概15min才通过，推荐用python来写，不过我这里还是放出我写的c++代码。

Code:
```cpp
class Solution {
private:
    int pos = 0;
public:
    //计算字符串在i处加(, j处加)的数值结果
    int cal(string& e, int i, int j) {
        int len = e.size();
        string left = e.substr(i, pos - i);
        string right = e.substr(pos + 1, j - pos);
        string left_mul = (i == 0 ? "1" : e.substr(0, i));
        string right_mul = (j == len - 1 ? "1" : e.substr(j + 1, len - j));
        return (stoi(left) + stoi(right)) * stoi(left_mul) * stoi(right_mul);
    }
    string minimizeResult(string e) {
        for(int i = 0; i < e.size(); ++i) {
            if(e[i] == '+') {
                pos = i;
                break;
            }
        }
        int left, right;
        int ans = INT_MAX;
        for(int i = 0; i < pos; ++i)
            for(int j = pos + 1; j < e.size(); ++j) {
                int res = cal(e, i, j);
                if(ans > res) {
                    left = i;
                    right = j;
                    ans = res;
                }
            }
        string a = e.substr(left, pos - left);
        string b = e.substr(pos + 1, right - pos);
        string left_sup = (left == 0 ? "" : e.substr(0, left));
        string right_sup = (right == e.size() - 1 ? "" : e.substr(right + 1, e.size() - right));
        //丑陋的字符串合并
        return left_sup + "(" + a + "+" + b + ")" + right_sup;
    }
};

```

##3.[K 次增加后的最大乘积](https://leetcode-cn.com/problems/maximum-product-after-k-increments/)
**解题思路**:数据范围10^5，直接用优先队列维护最小值即可。(个人感觉比第二题好写多了)。

Code:
```cpp
class Solution {
private:
    int mod = 1e9 + 7;
public:
    int maximumProduct(vector<int>& nums, int k) {
        priority_queue<long long, vector<long long>, greater<long long>> pq(nums.begin(), nums.end());
        while(k--) {
            int min_num = pq.top();
            pq.pop();
            min_num++;
            pq.push(min_num);
        }
        long long ans = 1;
        while(!pq.empty()) {
            cout << pq.top() << " ";
            ans = (ans * pq.top()) % mod;
            ans %= mod;
            pq.pop();
        }
        return ans % mod;
    }
};
```

##4.[花园的最大总美丽值](https://leetcode-cn.com/problems/maximum-total-beauty-of-the-gardens/)
**解题思路**:通过枚举完善花园的数量，并且二分不完善花园中可能最小值的最大值即可,虽然说起来很简单，但是比赛的时候我并没有写出来(欸嘿). 代码是来自[TsReaper](https://leetcode-cn.com/u/tsreaper/)，该去给大佬点赞了。
Code:
```cpp
class Solution {
public:
    long long maximumBeauty(vector<int>& flowers, long long newFlowers, int target, int full, int partial) {
        int n = flowers.size();
        vector<int> A(n + 1);
        sort(flowers.begin(), flowers.end());
        for (int i = 0; i < n; i++) A[i + 1] = flowers[i];
        vector<long long> f(n + 1);
        for (int i = 1; i <= n; i++) f[i] = f[i - 1] + A[i];

        // 统计一开始就有几座完善的花园
        int start;
        for (start = 0; start < n; start++) if (A[n - start] < target) break;
        // sm 表示为了获得规定数量的完善花园，需要多种几朵花
        long long ans = 0, sm = 0;
        for (int i = start; i <= n && sm <= newFlowers; i++) {
            // 二分，用剩下的花，可以把不完善的花园里最少的花变得和哪个花园一样多
            int head = 0, tail = n - i;
            while (head < tail) {
                int mid = (head + tail + 1) >> 1;
                long long t = 1LL * mid * A[mid] - f[mid];
                if (sm + t <= newFlowers) head = mid;
                else tail = mid - 1;
            }
            // 所有花园里花的数目至少和第 head 个花园一样多，可能还剩下一些花，还能再提高花的数目
            // 不完善花园里的花不能超过 target，否则就变成完善花园了
            long long x = newFlowers - sm - (1LL * head * A[head] - f[head]);
            long long y = min(head ? A[head] + x / head : 0, target - 1LL);
            ans = max(ans, 1LL * i * full + 1LL * y * partial);
            sm += target - A[n - i];
        }
        return ans;
    }
};
```



