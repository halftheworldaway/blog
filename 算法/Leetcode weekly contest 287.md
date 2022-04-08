#Leetcode weekly conetest 287
##1.[转化时间需要的最小操作数](https://leetcode-cn.com/problems/minimum-number-of-operations-to-convert-time/)
题目大意: 给出两个时间current和correct(current < correct), 你可以将current时间增加1, 5, 15, 60分钟, 问将current转化为correct的最小步骤

解法: 本质上是一个贪心过程，我们尽可能的去将current时间减去当前可拨动的最大分钟数, 便可以达到最优解。

Code:
```cpp

class Solution {
public:
    int convert(string time) {
        char ch0 = time[0], ch1 = time[1], ch3 = time[3], ch4 = time[4];
        int hour = (ch0 - '0') * 10 + ch1 - '0', min = (ch3 - '0') * 10 + ch4 - '0';
        return hour * 60 + min;
    }
    int convertTime(string current, string correct) {
        int time1 = convert(current);
        int time2 = convert(correct);
        int loss = time2 - time1;
        int cnt = 0;
        while(loss >= 60) {
            loss -= 60;
            cnt++;
        }
        while(loss >= 15) {
            loss -= 15;
            cnt++;
        }
        while(loss >= 5) {
            loss -= 5;
            cnt++;
        }
        while(loss >= 1) {
            loss -= 1;
            cnt++;
        }
        return cnt;
    }
};
```

##2.[找出输掉零场或一场比赛的玩家](https://leetcode-cn.com/problems/find-players-with-zero-or-one-losses/)
题目大意：找出比赛过的人里面，只输过一场的人或者没有输过的人，并将其按照参赛人员序号有序返回。

解法：利用有序容器(Ordered-set)来记录参赛选手，同时通过哈希表记录每名选手的胜利和失败场次，之后我们便可以遍历有序容器，按照题意进行处理即可。

Code:
```cpp

class Solution {
public:
    vector<vector<int>> findWinners(vector<vector<int>>& matches) {
        unordered_map<int, int> win; //记录每名参选人的胜利场次
        unordered_map<int, int> lose; //记录每名参选人的失败场次
        set<int> player; //有序容器记录参选选手的编号，方便后面进行查询
        vector<int> list1; 
        vector<int> list2;
        for(auto&& x : matches) {
            win[x[0]]++;
            lose[x[1]]++;
            player.insert(x[0]);
            player.insert(x[1]);
        }
        
        for(auto x : player) {
            //如果没参赛，那么直接跳过
            if(lose[x] + win[x] == 0) {
                continue;
            }
            if(lose[x] == 1) {
                list2.push_back(x);
            }
            if(lose[x] == 0) {
                list1.push_back(x);
            }
        }
        return {list1, list2};
    }
};
```

##3.[每个小孩最多能分到多少糖果](https://leetcode-cn.com/problems/maximum-candies-allocated-to-k-children/)

题目大意: 给出一堆糖果，我们将其中的任意一堆拆分，在经过若干次这样的拆分之后，我们便可以将已经分好的糖果堆发给指定数量的孩子们, 然而处于某种考量，我们必须要让每个孩子都分配到相同数量的糖果，问每个小孩可以拿到的糖果数量最大是多少。

解法：我们可以做出一种假设，假设我们可以按照每个孩子分给**x**个糖果来完成分配任务，那么很简单的我们就可以推理出，对于每个**y**，如果 **y**小于**x**,那么我也可以通过给每个孩子**y**个糖果来完成任务，故在这个答案空间里，是满足二分性质的，我们通过枚举上界和下界来进行二分迭代，然后再通过遍历序列来验证我们的当前所迭代的数值是否满足要求即可。

Code:
```cpp

class Solution {
public:
    bool check(vector<int>& c, long long target, long long k) {
        //判断target是否能够满足我们的题目要求
        long long cnt = 0;
        for(int i = 0; i < c.size(); ++i) {
            cnt += c[i] / target;
            if(cnt >= k) {
                return true;
            }
        }
        return false;
    }
    long long maximumCandies(vector<int>& c, long long k) {
        //二分答案 + 遍历
        long long sum = 0;
        for(int i = 0; i < c.size(); ++i) {
            sum += (long long)c[i];
        }
        if(sum < k) {
            return 0;
        }
        long long left = 0, right = sum / k + 1; //left和right是我们的上下界限定，为左闭右开区间
        while(left < right - 1) {
            long long mid = (left + right) / 2;
            if(check(c, mid, k)) {
                left = mid;
            } else {
                right = mid;
            }
        }
        return left;
    }
};
```

##4.[加密解密字符串](https://leetcode-cn.com/problems/encrypt-and-decrypt-strings/)
题目大意：虽然原题目描述有些考验我们的语文水平，但是加密过程实际上真的只是让我们对字符串(key)中的字符来根据value进行字符串替换，而解密则是这一过程的逆过程。

解法: 在比赛中，我的回溯代码被无情的TLE了，之后通过[灵茶山艾府](https://leetcode-cn.com/u/endlesscheng/)大佬的题解领悟到十分优雅的思路，即在解密过程中逆向思考，不是去考虑当前字符串解密过来之后是什么样，而是考虑原字典所有字符串加密的时候的所有可能，并将其通过哈希表记录下来.

Code:
```cpp

class Encrypter {
    array<string, 26> mp;
    unordered_map<string, int> cnt;
public:
    Encrypter(vector<char> &keys, vector<string> &values, vector<string> &dictionary) {
        for (int i = 0; i < keys.size(); ++i)
            mp[keys[i] - 'a'] = values[i];
        for (auto &s : dictionary)
            ++cnt[encrypt(s)];
    }

    string encrypt(string word1) {
        string res;
        for (char ch : word1) {
            auto &s = mp[ch - 'a'];
            if (s == "") return "";
            res += s;
        }
        return res;
    }

    int decrypt(string word2) { return cnt.count(word2) ? cnt[word2] : 0; } 
};
```



