## 1.[解密消息](https://leetcode.cn/problems/decode-the-message/)
**解题思路**: 我们用一个哈希表来记录下我们的这些第一次字符的出现位置，然后在遍历需要解密的字符串，利用已经记录好的哈希表来进行解密。
**Tag**: 哈希表

Code:
```cpp

class Solution {
public:
    string decodeMessage(string key, string message) {
        unordered_map<char, char> hash;
        char ch = 'a';
        for(int i = 0; i < key.size(); ++i) {
            if(key[i] == ' ') {
                continue;
            }
            if(hash.find(key[i]) == hash.end()) {
                hash[key[i]] = ch;
                ch++;
            }
        }
        string ret;
        for(auto&& ch : message) {
            if(ch == ' ') {
                ret += " ";
            } else {
                ret += char(hash[ch]);
            }
        }
        return ret;
    }
};
};
```

## 2.[螺旋矩阵IV](https://leetcode.cn/problems/spiral-matrix-iv/)
**解题思路**：本质上和[螺旋矩阵](https://leetcode.cn/problems/spiral-matrix/)都是模拟问题，我们从外圈遍历到内圈，然后逐渐缩小范围即可。
**Tag**:模拟
Code:
```cpp
class Solution {
public:
    vector<vector<int>> spiralMatrix(int m, int n, ListNode* head) {
        vector<vector<int>> ret(m, vector<int>(n, -1));
        int top = 0, bottom = m - 1, left = 0, right = n - 1;
        while(top <= bottom && left <= right) {
            for(int i = left; i <= right; ++i) {    
                if(head == nullptr) {
                    return ret;
                }
                ret[top][i] = head->val;
                head = head->next;
            }
            
            for(int i = top + 1; i <= bottom; ++i) {
                if(head == nullptr) {
                    return ret;
                }
                ret[i][right] = head->val;
                head = head->next;
            }
            
            for(int i = right - 1; i >= left; --i) {
                if(head == nullptr) {
                    return ret;
                }
                ret[bottom][i] = head->val;
                head = head->next;
            }
            
            for(int i = bottom - 1; i > top; --i) {
                if(head == nullptr) {
                    return ret;
                }
                ret[i][left] = head->val;
                head = head->next;
            }
            ++top;
            --bottom;
            ++left;
            --right;
        }
        return ret;
    }
};

```

## 3.[知道秘密的人数](https://leetcode.cn/problems/number-of-people-aware-of-a-secret/)
**解题思路**: 这题的难点其实在于状态的定义，如果定义dp[i]为第i天知道秘密的人数，我们就很容易把自己绕晕，因为这种状态定义方式无法让我们区分出当前知道秘密的人数中，能够分享秘密的人是多少，马上要遗忘的人有多少，可以分享秘密的人有多少。所以一种比较好的状态定义方式可以定义如下，我们令dp[i]在第i天新知道秘密的人数，这样的话，dp[i]就可以很简单由[i - forget + 1, i - delay]的区间内转移过来。
**Tag**: 线性动态规划，难点在于状态定义
Code:
```cpp
class Solution {
private:
    const int mod = 1e9 + 7;
public:
    int peopleAwareOfSecret(int n, int delay, int forget) {
        vector<int> f(n + 1);
        f[1] = 1;
        int ret = 0;
        for(int i = 2; i <= n; ++i) {
            int start, end;
            start = max(i - forget + 1, 1);
            end = i - delay;
            if(end < 1) {
                continue;
            }
            for(int j = start; j <= end; ++j) {
                f[i] = (f[i] + f[j]) % mod;
            }
        }
        for(int i = n - forget + 1; i <= n; ++i) {
            ret = (ret + f[i]) % mod;
        }
        return ret;
    }
};
```

## 4.[网格图中递增路径的数目](https://leetcode-cn.com/problems/maximum-total-beauty-of-the-gardens/)
**解题思路**: 其实个人觉得思维难度并没有第三题大，我们可以很容易的想到令dp[i][j]为在(i, j)点为终点的路径数量，再结合题目中要求递增的需求，我们只要遍历每个格子周围的比它小的元素的路径数量，并且将其加和即可。
**Tag**:动态规划
Code:
```cpp
class Solution {
private:
    int rows, cols;
    const int mod = 1e9 + 7;
    vector<vector<int>> dir = {
        {-1, 0},
        {1, 0}, 
        {0, -1},
        {0, 1}
    };
    vector<vector<long long>> dp;
    int dfs(vector<vector<int>>& grid,  int row, int col){
        if(dp[row][col]) {
            return dp[row][col];
        }
        dp[row][col] = 1;

        for(int i = 0; i < dir.size(); ++i) {
            int xx = row + dir[i][0];
            int yy = col + dir[i][1];
            if(xx < rows && xx >= 0 && yy >= 0 && yy < cols && grid[xx][yy] < grid[row][col]) {
                dp[row][col] = (dp[row][col] + dfs(grid, xx, yy)) % mod;
            }
        }

        return dp[row][col];
    }
public:
    int countPaths(vector<vector<int>>& grid) {
        rows = grid.size(); cols = grid[0].size();
        dp.resize(rows, vector<long long>(cols, 0));
        int ans = 0;
        for(int i = 0; i < rows; ++i) {
            for(int j = 0; j < cols; ++j) {
                ans = (ans + dfs(grid, i, j)) % mod;
            }
        }

        return ans;
    }
};
```



