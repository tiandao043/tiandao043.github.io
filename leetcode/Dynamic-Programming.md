## 动态规划

#### [5. 最长回文子串](https://leetcode-cn.com/problems/longest-palindromic-substring/)

```c++
class Solution {
public:
    string longestPalindrome(string s) {
        int n=s.length();
        vector<vector<int>> dp(n,vector<int>(n));
        string ans;
        for(int l = 0; l < n; ++l){
            for(int i = 0; i + l < n; ++i){
                int j=i+l;
// cout<<i<<" "<<j<<" "<<dp[i][j]<<":"<<l<<endl;
//                 cout<<ans<<endl;
                if(l==0)dp[i][j]=1;
                else if(l==1)dp[i][j]=(s[i]==s[j]);
                else dp[i][j]=(s[i]==s[j] && dp[i+1][j-1]);
                if(dp[i][j]&&l+1>ans.length())ans=s.substr(i,l+1);                
            }
        }
        return ans;
    }
};
```

#### [72. 编辑距离](https://leetcode-cn.com/problems/edit-distance/)

若 A 和 B 的最后一个字母相同：

$\begin{aligned} D[i][j] &= \min(D[i][j - 1] + 1, D[i - 1][j]+1, D[i - 1][j - 1])\\ &= 1 + \min(D[i][j - 1], D[i - 1][j], D[i - 1][j - 1] - 1) \end{aligned}$
	


若 A 和 B 的最后一个字母不同：

$D[i][j] = 1 + \min(D[i][j - 1], D[i - 1][j], D[i - 1][j - 1])$


[LeetCode-Solution](https://leetcode-cn.com/problems/edit-distance/solution/bian-ji-ju-chi-by-leetcode-solution/)

```c++
class Solution {
public:
    int minDistance(string word1, string word2) {
        int n=word1.length();
        int m=word2.length();
        if(n==0||m==0)return n+m;
        vector<vector<int>> dp(n+1,vector<int>(m+1));
        for(int i=0;i<=n;i++)dp[i][0]=i;
        for(int j=0;j<=m;j++)dp[0][j]=j;

        for(int i=1;i<=n;i++)
        for(int j=1;j<=m;j++){
            if(word1[i-1]==word2[j-1]){
                dp[i][j]=1+min(dp[i][j-1],min(dp[i-1][j],dp[i-1][j-1]-1));
            }
            else{
                dp[i][j]=1+min(dp[i][j-1],min(dp[i-1][j],dp[i-1][j-1]));
            }
        }
        return dp[n][m];
    }
};
```

**复杂度分析**

- 时间复杂度 ：O(mn)O(mn)，其中 mm 为 word1 的长度，nn 为 word2 的长度。

- 空间复杂度 ：O(mn)O(mn)，我们需要大小为 O(mn)O(mn) 的 DD 数组来记录状态值。

#### [198. 打家劫舍](https://leetcode-cn.com/problems/house-robber/)

``` c++
class Solution {
public:
    int rob(vector<int>& nums) {
        int n=nums.size();
        if(n==0)return 0;
        // dp[i][j] 前i个房间最多金额,且第i个房间状态为j
        vector<vector<int>> dp(n+1,vector<int>(2));
        for(int i=0;i<=n;i++)dp[i][0]=0;
        dp[0][1]=nums[0];
        for(int i=1;i<n;i++){
            dp[i][0]=max(dp[i-1][0],dp[i-1][1]);
            dp[i][1]=max(dp[i-1][0]+nums[i],dp[i][0]);
            // cout<<dp[i][0]<<" "<<dp[i][1]<<endl;
        }
        dp[n][0]=max(dp[n-1][0],dp[n-1][1]);
        return dp[n][0];
    }
};
```

还可以滚动数组

```c++
class Solution {
public:
    int rob(vector<int>& nums) {
        if (nums.empty()) {
            return 0;
        }
        int size = nums.size();
        if (size == 1) {
            return nums[0];
        }
        vector<int> dp = vector<int>(size, 0);
        dp[0] = nums[0];
        dp[1] = max(nums[0], nums[1]);
        for (int i = 2; i < size; i++) {
            dp[i] = max(dp[i - 2] + nums[i], dp[i - 1]);
        }
        return dp[size - 1];
    }
};
```

```c++
class Solution {
public:
    int rob(vector<int>& nums) {
        if (nums.empty()) {
            return 0;
        }
        int size = nums.size();
        if (size == 1) {
            return nums[0];
        }
        int first = nums[0], second = max(nums[0], nums[1]);
        for (int i = 2; i < size; i++) {
            int temp = second;
            second = max(first + nums[i], second);
            first = temp;
        }
        return second;
    }
};
```



#### [213. 打家劫舍 II](https://leetcode-cn.com/problems/house-robber-ii/)

```c++
class Solution {
public:
    int rob(vector<int>& nums) {
        int n=nums.size();
        if(n==0)return 0;
        if(n==1)return nums[0];
        vector<int> nums1=nums;
        nums1.pop_back();
        vector<int> nums2=nums;
        nums2.erase(nums2.begin());
        return max(rob1(nums1),rob1(nums2));
    }

    int rob1(vector<int>& nums) {
        int n=nums.size();
        if(n==0)return 0;
        if(n==1)return nums[0];
        // dp[i][j] 前i个房间最多金额,且第i个房间状态为j
        vector<int> dp(n+1);
        // for(int i=0;i<=n;i++)dp[i][0]=0;
        dp[0]=nums[0];
        dp[1]=max(dp[0],nums[1]);
        for(int i=2;i<n;i++){
            // dp[i]=max(dp[i-1][0],dp[i-1][1]);
            dp[i]=max(dp[i-2]+nums[i],dp[i-1]);
            // cout<<dp[i][0]<<" "<<dp[i][1]<<endl;
            // cout<<dp[i]<<endl;
        }
        // dp[n][0]=max(dp[n-1][0],dp[n-1][1]);
        
        return dp[n-1];
    }
};
```

#### [337. 打家劫舍 III](https://leetcode-cn.com/problems/house-robber-iii/)

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
    /* 返回一个大小为 2 的数组 arr
arr[0] 表示不抢 root 的话，得到的最大钱数
arr[1] 表示抢 root 的话，得到的最大钱数 */
    vector<int> dp(TreeNode* root){
        if(root==nullptr)return{0,0};
        vector<int> l=dp(root->left);
        vector<int> r=dp(root->right);
        int rob=root->val+l[0]+r[0];
        int not_rob=max(l[0],l[1])+max(r[0],r[1]);
        return {not_rob,rob};
    }
    int rob(TreeNode* root) {
        vector<int> res=dp(root);
        return max(res[0],res[1]);
    }
};
```



```java
Map<TreeNode, Integer> memo = new HashMap<>();
public int rob(TreeNode root) {
    if (root == null) return 0;
    // 利用备忘录消除重叠子问题
    if (memo.containsKey(root)) 
        return memo.get(root);
    // 抢，然后去下下家
    int do_it = root.val
        + (root.left == null ? 
            0 : rob(root.left.left) + rob(root.left.right))
        + (root.right == null ? 
            0 : rob(root.right.left) + rob(root.right.right));
    // 不抢，然后去下家
    int not_do = rob(root.left) + rob(root.right);
    
    int res = Math.max(do_it, not_do);
    memo.put(root, res);
    return res;
}
```

[labuladong](https://leetcode-cn.com/problems/house-robber-ii/solution/tong-yong-si-lu-tuan-mie-da-jia-jie-she-wen-ti-by-/)

#### [516. 最长回文子序列](https://leetcode-cn.com/problems/longest-palindromic-subsequence/)

```c++
class Solution {
public:
    // 在子串 s[i..j] 中，最长回文子序列的长度为 dp[i][j]
    int longestPalindromeSubseq(string s) {
        int n=s.length();
        vector<vector<int>> dp(n, vector<int>(n, 0));
        for (int i = 0; i < n; i++) dp[i][i] = 1;

        for(int i=n;i>=0;i--){
            for(int j=i+1;j<n;j++){
                if(s[i]==s[j])dp[i][j]=dp[i+1][j-1]+2;
                else dp[i][j]=max(dp[i+1][j],dp[i][j-1]);
            }
        }
        return dp[0][n-1];
    }
};
```

#### [674. 最长连续递增序列](https://leetcode-cn.com/problems/longest-continuous-increasing-subsequence/)

```c++
class Solution {
public:
    int findLengthOfLCIS(vector<int>& nums) {
        int n=nums.size();
        if(n==0)return 0;
        int ans=0;
        int ind=nums[0];
        int index=1;
        for(int i=1;i<n;i++){
            if(nums[i]>ind){
                index++;                
            }
            else{
                ans=max(ans,index);
                index=1;
            }
            ind=nums[i];
            // cout<<ind<<" "<<index<<endl;
        }
        // cout<<index;
        return max(ans,index);;
    }
};
```

