## 分治法

## [50.Pow(x,n)](https://leetcode-cn.com/problems/powx-n/)

**快速幂**

**递归**

```c++
class Solution {
public:
    double quickMul(double x, int n){
        if(n==0){
            return 1.0;
        }
        double y=quickMul(x,n/2);
        return n%2==0?y*y:y*y*x;
    }
    double myPow(double x, int n) {
        return n>=0?quickMul(x,n):1.0/quickMul(x,n);
    }
};
```

*注：

- 复杂度分析

- 时间复杂度：$O(\log n)$，即为递归的层数。

- 空间复杂度：$O(\log n)$，即为递归的层数。这是由于递归的函数调用会使用栈空间。



**迭代**

```c++
class Solution {
public:
    double quickMul(double x, int n){
        double ans=1.0;
        // 贡献的初始值为 x
        double x_contribute=x;
        // 在对 N 进行二进制拆分的同时计算答案
        while(n){
            // cout<<n<<endl;
            //负数取余为负
            if(abs(n%2)==1){
                // 如果 N 二进制表示的最低位为 1，那么需要计入贡献
                ans*=x_contribute;
                // cout<<n<<" "<<ans<<endl;
            }
            // 将贡献不断地平方
            x_contribute *= x_contribute;
            // 舍弃 N 二进制表示的最低位，这样我们每次只要判断最低位即可
            n/=2;
        }
        
        return ans;
    }
    double myPow(double x, int n) {
        // cout<<(-1)%2<<endl;
        return n>=0?quickMul(x,n):1.0/quickMul(x,n);
    }
};
```

- 时间复杂度：$O(\log n)$，即为对 $n$ 进行二进制拆分的时间复杂度。
- 空间复杂度：$O(1)$。



之前只是用，板子选手，，，太菜了我

## [53.最大子序和](https://leetcode-cn.com/problems/maximum-subarray/)

**前缀和(题解里叫dp)**

```c++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int n=nums.size();
        int ans=INT_MIN;
        int ind=INT_MIN;
        for(int i=0;i<n;i++){
            if(ind<0){
                ind=0;
            }
            ind+=nums[i];
            ans=max(ans,ind);
        }
        return ans;
    }
};
```

写好看一点

```c++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int ans=nums[0];
        int ind=0;
        for(const auto &a:nums){
            ind=max(ind+a,a);
            ans=max(ans,ind);
        }
        return ans;
    }
};
```

- 时间复杂度：$O(n)$，其中 $n$ 为 nums 数组的长度。我们只需要遍历一遍数组即可求得答案。
- 空间复杂度：$O(1)$。我们只需要常数空间存放若干变量。



**分治**

```c++
class Solution {
public:

    struct Status{
        int lsum,rsum,msum,isum;
        // lSum 表示 [l, r]内以 ll 为左端点的最大子段和
        // rSum 表示 [l, r]内以 rr 为右端点的最大子段和
        // mSum 表示 [l, r]内的最大子段和
        // iSum 表示 [l, r]的区间和
    };
    
    Status pushUp(Status l,Status r){
        int isum=l.isum+r.isum;
        int lsum=max(l.lsum,l.isum+r.lsum);
        int rsum=max(r.rsum,r.isum+l.rsum);
        int msum=max(max(l.msum,r.msum),l.rsum+r.lsum);
        return (Status){lsum,rsum,msum,isum};
    }
    
    Status get(vector<int> &a, int l, int r) {
        if (l == r) return (Status) {a[l], a[l], a[l], a[l]};
        int m = (l + r) >> 1;
        Status lSub = get(a, l, m);
        Status rSub = get(a, m + 1, r);
        return pushUp(lSub, rSub);
    }

    int maxSubArray(vector<int>& nums) {
        return get(nums,0,nums.size()-1).msum;
    }
};
```

线段树

**复杂度分析**

假设序列 $a$ 的长度为 $n$。

- 时间复杂度：假设我们把递归的过程看作是一颗二叉树的先序遍历，那么这颗二叉树的深度的渐进上界为 $O(\log n)$，这里的总时间相当于遍历这颗二叉树的所有节点，故总时间的渐进上界是 $O(\sum_{i = 1}^{\log n} 2^{i - 1}) = O(n)$，故渐进时间复杂度为 $O(n)$。

- 空间复杂度：递归会使用 $O(\log n)$ 的栈空间，故渐进空间复杂度为 $O(\log n)$。

  

「分治」，它不仅可以解决区间 [0, n - 1][0,n−1]，还可以用于解决任意的子区间 [l, r][l,r] 的问题。如果我们把 [0, n - 1][0,n−1] 分治下去出现的所有子区间的信息都用堆式存储的方式记忆化下来，即建成一颗真正的树之后，我们就可以在 $O(\log n)$ 的时间内求到任意区间内的答案，我们甚至可以修改序列中的值，做一些简单的维护，之后仍然可以在 $O(\log n)$ 的时间内求到任意区间内的答案，对于大规模查询的情况下，这种方法的优势便体现了出来。这棵树就是上文提及的一种神奇的数据结构——**线段树**。

## [169.多数元素](https://leetcode-cn.com/problems/majority-element/)

```c++
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        int ind=nums[0];
        int cnt=0;
        for(const auto& a:nums){
            if(ind==a){
                cnt++;
            }
            else{
                cnt--;
            }
            if(cnt<0){
                cnt=0;
                ind=a;
            }
        }
        return ind;
    }
};
```

```c++
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        sort(nums.begin(),nums.end());
        return nums[nums.size()/2];
    }
};
```

**随机化**有点有趣mark一下

```c++
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        while (true) {
            int candidate = nums[rand() % nums.size()];
            int count = 0;
            for (int num : nums)
                if (num == candidate)
                    ++count;
            if (count > nums.size() / 2)
                return candidate;
        }
        return -1;
    }
};
```

- 时间复杂度：理论上最坏情况下的时间复杂度为 $O(\infty)$，因为如果我们的运气很差，这个算法会一直找不到众数，随机挑选无穷多次，所以最坏时间复杂度是没有上限的。然而，运行的期望时间是线性的。为了更简单地分析，先说服你自己：由于众数占据超过 数组一半的位置，期望的随机次数会小于众数占据数组恰好一半的情况。因此，我们可以计算随机的期望次数（下标为 prob 为原问题，mod 为众数恰好占据数组一半数目的问题）：

$$\begin{aligned} E(\textit{iters}_{prob}) &\leq E(\textit{iters}_{mod}) \\ &= \lim_{n\to\infty} \sum_{i=1}^{n} i \cdot \frac{1}{2^i} \\ &= 2 \end{aligned}$$


计算方法为：当众数恰好占据数组的一半时，第一次随机我们有 $\frac{1}{2}$  的概率找到众数，如果没有找到，则第二次随机时，包含上一次我们有 $\frac{1}{4}$  的概率找到众数，以此类推。因此期望的次数为$ i * \frac{1}{2^i}$ 的和，可以计算出这个和为 $2$，说明期望的随机次数是常数。每一次随机后，我们需要 $O(n)$ 的时间判断这个数是否为众数，因此期望的时间复杂度为 $O(n)$。

- 空间复杂度：$O(1)$。随机方法只需要常数级别的额外空间。

**hash**

```c++
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        unordered_map<int, int> counts;
        int majority = 0, cnt = 0;
        for (int num: nums) {
            ++counts[num];
            if (counts[num] > cnt) {
                majority = num;
                cnt = counts[num];
            }
        }
        return majority;
    }
};
```

- 时间复杂度：$O(n)$，其中 n 是数组 nums 的长度。我们遍历数组 nums 一次，对于 nums 中的每一个元素，将其插入哈希表都只需要常数时间。如果在遍历时没有维护最大值，在遍历结束后还需要对哈希表进行遍历，因为哈希表中占用的空间为 $O(n)$（可参考下文的空间复杂度分析），那么遍历的时间不会超过 $O(n)$。因此总时间复杂度为 $O(n)$。

- 空间复杂度：$O(n)$。哈希表最多包含 $n - \lfloor \dfrac{n}{2} \rfloor*n*−⌊2*n*⌋$个键值对，所以占用的空间为 $O(n)$。这是因为任意一个长度为 n 的数组最多只能包含 n 个不同的值，但题中保证 nums 一定有一个众数，会占用（最少） $\lfloor \dfrac{n}{2} \rfloor + 1$个数字。因此最多有 $n - (\lfloor \dfrac{n}{2} \rfloor + 1)$ 个不同的其他数字，所以最多有 $n - \lfloor \dfrac{n}{2}⌋$个不同的元素。



**分治**

**思路**

如果数 a 是数组 nums 的多数元素，如果我们将 nums 分成两部分，那么 a 必定是至少一部分的多数元素。

我们可以使用反证法来证明这个结论。假设 a 既不是左半部分的众数，也不是右半部分的众数，那么 a 出现的次数少于 l / 2 + r / 2 次，其中 l 和 r 分别是左半部分和右半部分的长度。由于 l / 2 + r / 2 <= (l + r) / 2，说明 a 也不是数组 nums 的多数元素，因此出现了矛盾。所以这个结论是正确的。

这样以来，我们就可以使用分治法解决这个问题：将数组分成左右两部分，分别求出左半部分的多数元素 a1 以及右半部分的多数元素 a2，随后在 a1 和 a2 中选出正确的多数元素。

**算法**

我们使用经典的分治算法递归求解，直到所有的子问题都是长度为 1 的数组。长度为 1 的子数组中唯一的数显然是众数，直接返回即可。如果回溯后某区间的长度大于 1，我们必须将左右子区间的值合并。如果它们的众数相同，那么显然这一段区间的众数是它们相同的值。否则，我们需要比较两个众数在整个区间内出现的次数来决定该区间的众数。

```c++
class Solution {
public:
    bool check(vector<int>& nums, int target, int l, int h){
        int cnt=0;
        for(int i=l;i<=h;i++){
            if(nums[i]==target)cnt++;
        }
        return cnt>(h-l+1)/2;
    }
    int mErec(vector<int>& nums,int l,int r){
        if(l==r)return nums[l];
        int m=(l+r)>>1;
        int lm=mErec(nums,l,m);
        int rm=mErec(nums,m+1,r);
        if(check(nums,lm,l,r))return lm;
        if(check(nums,rm,l,r))return rm;
        return -1;
    }
    int majorityElement(vector<int>& nums) {
        return mErec(nums,0,nums.size()-1);
    }
};
```

**复杂度分析**

- 时间复杂度：$O(n\log n)$。函数 majority_element_rec() 会求解 2 个长度为 $\dfrac{n}{2}$ 的子问题，并做两遍长度为 $n$ 的线性扫描。因此，分治算法的时间复杂度可以表示为：

$$T(n) = 2T(\frac{n}{2}) + 2n$$

根据 主定理，本题满足第二种情况，所以时间复杂度可以表示为：

$$\begin{aligned} T(n) &= \Theta(n^{log_{b}a}\log n) \\ &= \Theta(n^{log_{2}2}\log n) \\ &= \Theta(n \log n) \\ \end{aligned}$$

- 空间复杂度：$O(\log n)$。尽管分治算法没有直接分配额外的数组空间，但在递归的过程中使用了额外的栈空间。算法每次将数组从中间分成两部分，所以数组长度变为 1 之前需要进行 $O(\log n)$ 次递归，即空间复杂度为 $O(\log n)$。

