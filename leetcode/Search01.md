#### [35. 搜索插入位置](https://leetcode-cn.com/problems/search-insert-position/)

```c++
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        int n=nums.size();
        int l=0;
        int r=n-1;
        int m=(l+r)>>1;
        while(l<=r){
            m=(l+r)>>1;
            if(nums[m]==target)return m;
            else if(nums[m]<target)l=m+1;
            else r=m-1;  
            // cout<<l<<" "<<r<<endl;          
        }        
        return l;
    }
};
```

```c++
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        int n=nums.size();
        int l=0;
        int r=n;
        int m=(l+r)>>1;
        while(l<r){
            m=(l+r)>>1;
            // cout<<l<<" "<<r<<":"<<m<<endl;  
            if(nums[m]==target)return m;
            else if(nums[m]<target)l=m+1;
            else r=m;                      
        }        
        return l;
    }
};
```

```c++
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        return lower_bound(nums.begin(),nums.end(),target) - nums.begin();
    }
};
```

#### [202. 快乐数](https://leetcode-cn.com/problems/happy-number/)

```c++
class Solution {
public:
    unordered_map<int,int> map;
    bool isHappy(int n) {
        if(n==1)return true;
        int res=0;
        while(n){
            res+=(n%10)*(n%10);
            n/=10;
        }        
        if(map.count(res))return false;
        map[res]=1;
        cout<<res<<endl;
        return isHappy(res);
    }
};
```

快慢指针

```c++
class Solution {
public:
    int getNext(int n){
        int res=0;
        while(n){
            res+=(n%10)*(n%10);
            n/=10;
        }        
        return res;
    }
    bool isHappy(int n) {
        int s=n;
        int f=n;
        bool flag=true;
        // cout<<s<<" "<<f<<endl;
        while(f!=1&&(flag||s!=f)){
            flag=false;
            s=getNext(s);
            f=getNext(getNext(f));
            // cout<<s<<" "<<f<<endl;
        }
        return f==1;
    }
};
```

```c++
class Solution {
public:
    int getNext(int n){
        int res=0;
        while(n){
            res+=(n%10)*(n%10);
            n/=10;
        }        
        return res;
    }
    bool isHappy(int n) {
        int s=n;
        int f=getNext(n);
        while(f!=1&&s!=f){
            s=getNext(s);
            f=getNext(getNext(f));
            // cout<<s<<" "<<f<<endl;
        }
        return f==1;
    }
};
```

根据我们的探索，我们猜测会有以下三种可能。

最终会得到 11。
最终会进入循环。
值会越来越大，最后接近无穷大。
第三个情况比较难以检测和处理。我们怎么知道它会继续变大，而不是最终得到 11 呢？我们可以仔细想一想，每一位数的最大数字的下一位数是多少。

Digits	Largest	Next
1	9	81
2	99	162
3	999	243
4	9999	324
13	9999999999999	1053
对于 3 位数的数字，它不可能大于 243。这意味着它要么被困在 243 以下的循环内，要么跌到 1。4 位或 4 位以上的数字在每一步都会丢失一位，直到降到 3 位为止。所以我们知道，最坏的情况下，算法可能会在 243 以下的所有数字上循环，然后回到它已经到过的一个循环或者回到 1。但它不会无限期地进行下去，所以我们排除第三种选择。

即使在代码中你不需要处理第三种情况，你仍然需要理解为什么它永远不会发生，这样你就可以证明为什么你不处理它。[LeetCode-Solution](https://leetcode-cn.com/problems/happy-number/solution/kuai-le-shu-by-leetcode-solution/)


下一个值可能比自己大的最大数字是什么？根据我们之前的分析，我们知道它必须低于 243。因此，我们知道任何循环都必须包含小于 243 的数字，用这么小的数字，编写一个能找到所有周期的强力程序并不困难。

如果这样做，您会发现只有一个循环：$4 \rightarrow 16 \rightarrow 37 \rightarrow 58 \rightarrow 89 \rightarrow 145 \rightarrow 42 \rightarrow 20 \rightarrow 4$。所有其他数字都在进入这个循环的链上，或者在进入 1 的链上。

因此，我们可以硬编码一个包含这些数字的散列集，如果我们达到其中一个数字，那么我们就知道在循环中。

```c++
class Solution {
public:
    int getNext(int n){
        int res=0;
        while(n){
            res+=(n%10)*(n%10);
            n/=10;
        }        
        return res;
    }
    set<int> m={4, 16, 37, 58, 89, 145, 42, 20};

    bool isHappy(int n) {
         while (n != 1 && !m.count(n)) {
            n = getNext(n);
            // cout<<n<<endl;
        }
        return n == 1;
    }
};
```

**复杂度分析**

- 时间复杂度：$O(\log n)$。和上面一样。
- 空间复杂度：$O(1)$，我们没有保留我们所遇到的数字的历史记录。硬编码哈希集的大小是固定的。

#### [205. 同构字符串](https://leetcode-cn.com/problems/isomorphic-strings/)

```c++
class Solution {
public:
    bool isIsomorphic(string s, string t) {
        int n=s.length();
        for(int i=0;i<n;i++){
            if(s.find(s[i])!=t.find(t[i]))
            return false;
        }
        return true;
    }
};
```

#### [242. 有效的字母异位词](https://leetcode-cn.com/problems/valid-anagram/)

```c++
class Solution {
public:
    bool isAnagram(string s, string t) {
        sort(s.begin(),s.end());
        sort(t.begin(),t.end());
        return s==t;
    }
};
```

#### [290. 单词规律](https://leetcode-cn.com/problems/word-pattern/)

1.定义两个map，为何定义两个呢？ 防止[aaaa]与[cat dog dog cat]、[abba]与[cat cat cat cat]时输出true
2.用stringstream可以自动输出词组
3.判断：!(ss >> s)判断pattern长度是否大于str长度，且将ss容器中字符串赋值给s
4.判断：(map.count(c) == 1 && map[c] != s) || (rmap.count(s) == 1 && rmap[s] != c)) 来判断是否匹配
5.判断：(ss >> s) ? false : true 判断str长度是否大于pattern长度

[OrangeMan](https://leetcode-cn.com/problems/word-pattern/solution/cjian-ji-dai-ma-shuang-bai-7xing-by-orangeman/)

```c++
class Solution {
public:
    bool wordPattern(string pattern, string str) {
        unordered_map<char, string> map;
        unordered_map<string, char> rmap;
        stringstream ss(str);
        string s;
        for(char c:pattern){
            if(!(ss>>s) || (map.count(c) && map[c]!=s) || (rmap.count(s) && rmap[s]!=c))return false;
            map[c]=s;rmap[s]=c;
        }
        return !(ss>>s);
    }
};
```

#### [349. 两个数组的交集](https://leetcode-cn.com/problems/intersection-of-two-arrays/)

```c++
class Solution {
public:
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
        unordered_set<int> set;
        unordered_set<int> nums1_set(nums1.begin(),nums1.end());
        for(auto num:nums2){
            if(nums1_set.count(num))set.insert(num);
        }
        return vector<int>(set.begin(),set.end());
    }
};
```

#### [350. 两个数组的交集 II](https://leetcode-cn.com/problems/intersection-of-two-arrays-ii/)

```c++
class Solution {
public:
    vector<int> intersect(vector<int>& nums1, vector<int>& nums2) {
        vector<int> ans;
        unordered_map<int,int> nums1_set;
        for(auto num:nums1){
            if(nums1_set.count(num))nums1_set[num]++;
            else nums1_set[num]=1;
        }
        for(auto num:nums2){
            if(nums1_set.count(num)){
                ans.push_back(num);
                nums1_set[num]--;
                if(nums1_set[num]==0)nums1_set.erase(num);
            }
        }
        return ans;
    }
};
```

```c++
class Solution {
public:
    vector<int> intersect(vector<int>& nums1, vector<int>& nums2) {
        sort(nums1.begin(),nums1.end());
        sort(nums2.begin(),nums2.end());
        vector<int> ans;
        set_intersection(nums1.begin(),nums1.end(),nums2.begin(),nums2.end(),back_inserter(ans));
        return ans;
    }
};
```

```c++
class Solution {
public:
    vector<int> intersect(vector<int>& nums1, vector<int>& nums2) {
        sort(nums1.begin(),nums1.end());
        sort(nums2.begin(),nums2.end());
        vector<int> ans;
        set_intersection(nums1.begin(),nums1.end(),nums2.begin(),nums2.end(),inserter(ans,ans.begin()));
        return ans;
    }
};
```

#### [410. 分割数组的最大值](https://leetcode-cn.com/problems/split-array-largest-sum/)

```c++
class Solution {
public:
    int check(vector<int>& nums, int ind, int m){
        int ans=0;
        int sum=0;
        int n=nums.size();
        for(int i=0;i<n;i++){
            sum+=nums[i];
            if(nums[i]>ind)return -1;
            if(sum>ind){
                sum=0;
                ans++;
                i--;
            }
            cout<<sum<<" "<<ans<<endl;
        }
        ans++;
        // cout<<ans<<endl;
        return ans;
    }
    int splitArray(vector<int>& nums, int m) {
        int l=0;
        int r=INT_MAX;
        while(l<r){
            int mid=(l+r)>>1;
            // cout<<l<<" "<<r<<":"<<mid<<endl;
            int ind=check(nums,mid,m);
            // cout<<ind<<endl;
            if(ind>m){
                // cout<<mid<<endl;
                l=mid+1;
            }
            else if(ind==m){
                r=mid;
            }
            else if(ind==-1){
                l=mid+1;
            }
            else{
                r=mid;
            }
        }
        cout<<check(nums,4,m)<<endl;
        return l;
    }
};
```

```c++
class Solution {
public:
    bool check(vector<int>& nums, int ind, int m){
        int ans=1;
        int sum=0;
        int n=nums.size();
        for(int i=0;i<n;i++){                     
            if(sum+nums[i]>ind){
                sum=nums[i];
                ans++;                
            }else{
                sum+=nums[i];   
            }
            // cout<<sum<<" "<<ans<<endl;
        }
        // ans++;
        // cout<<ans<<endl;
        return ans<=m;
    }
    int splitArray(vector<int>& nums, int m) {
        int l=0;
        long long int r=INT_MAX;
        for (int i = 0; i < nums.size(); i++) {
            r += nums[i];
            if (l < nums[i]) {
                l = nums[i];
            }
        }

        while(l<r){
            int mid=(l+r)>>1;
            cout<<l<<" "<<r<<":"<<mid<<endl;
            // int ind=check(nums,mid,m);
            // cout<<ind<<endl;
            if(check(nums,mid,m)){
                // cout<<mid<<endl;
                r=mid;
            }
            // else if(ind==m){
            //     r=mid;
            // }
            // else if(ind==-1){
            //     l=mid+1;
            // }
            else{
                l=mid+1;
            }
        }
        // cout<<check(nums,4,m)<<endl;
        return l;
    }
};
```

$f[i][j]$ 表示将数组的前 $i$ 个数分割为 $j$ 段所能得到的最大连续子数组和的最小值。

状态转移方程：

$$f[i][j] = \min_{k=0}^{i-1} \Big\{ \max(f[k][j-1], \textit{sub}(k+1,i)) \Big\}$$
[LeetCode-Solution](https://leetcode-cn.com/problems/split-array-largest-sum/solution/fen-ge-shu-zu-de-zui-da-zhi-by-leetcode-solution/)

```c++
class Solution {
public:
    int splitArray(vector<int>& nums, int m) {
        int n = nums.size();
        vector<vector<long long>> f(n + 1, vector<long long>(m + 1, LLONG_MAX));
        vector<long long> sub(n + 1, 0);
        for (int i = 0; i < n; i++) {
            sub[i + 1] = sub[i] + nums[i];
        }
        f[0][0] = 0;
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= min(i, m); j++) {
                for (int k = 0; k < i; k++) {
                    f[i][j] = min(f[i][j], max(f[k][j - 1], sub[i] - sub[k]));
                }
            }
        }
        return (int)f[n][m];
    }
};
```



#### [451. 根据字符出现频率排序](https://leetcode-cn.com/problems/sort-characters-by-frequency/)

```c++
class Solution {
public:
    string frequencySort(string s) {
        unordered_map<char, int> charMap;
        for (const auto ch : s) { charMap[ch]++; }
        sort(s.begin(), s.end(), [&](const char ch1, const char ch2){ return charMap[ch1] == charMap[ch2] ?  ch1 > ch2 : charMap[ch1] > charMap[ch2]; });
        return s;
    }
};
```

```c++
class Solution {
public:
    string frequencySort(string s) {
        unordered_map<char, int> charMap;
        for (const auto ch : s) { charMap[ch]++; }
        sort(s.begin(), s.end(), [&](char &a, char &b) { return charMap[a] > charMap[b] || (charMap[a] == charMap[b] && a < b); });
        return s;
    }
};
```

```c++
class Solution {
public:
    string frequencySort(string s) {
        unordered_map<char, int> ump;
        for (const auto ch : s) { ump[ch]++; }
        priority_queue<pair<int, char>> pq;
        for (const auto &m : ump) {
            pq.push({m.second, m.first});
        }        
        string ret;
        while (!pq.empty()) {
            auto t = pq.top(); 
            pq.pop();
            ret.append(t.first, t.second);
        }
        return ret;
    }
};
```

利用下标

```c++
class Solution {
public:
    string frequencySort(string s) {
        unordered_map<char, int> ump;
        for (const auto &c : s) {
            ++ump[c];
        }
        vector<string> vec(s.size() + 1);
        string res;
        for (const auto &m : ump) {
            vec[m.second].append(m.second, m.first);
        }
        for (int i = s.size(); i > 0; --i) {
            if (!vec[i].empty()) {
                res.append(vec[i]);
            }
        }
        return res;
    }
};
```

#### [540. 有序数组中的单一元素](https://leetcode-cn.com/problems/single-element-in-a-sorted-array/)

```c++
class Solution {
public:
    int singleNonDuplicate(vector<int>& nums) {
        int ans=0;
        for(auto num:nums)ans^=num;
        return ans;
    }
};
```

二分

```c++
class Solution {
public:
    int singleNonDuplicate(vector<int>& nums) {
        int l=0;
        int r=nums.size()-1;
        // if(r==0)return nums[0];
        while(l<=r){
            int mid=(l+r+1)>>1;
            if(mid==0)return nums[0];
            // cout<<mid<<endl;
            bool flag=(r-mid)%2==0;
            if(nums[mid+1]==nums[mid]){
                if(flag){
                    l=mid+2;
                }
                else{
                    r=mid-1;
                }
            }
            else if(nums[mid-1]==nums[mid]){
                if(flag){
                    r=mid-2;
                }
                else{
                    l=mid+1;
                }
            }
            else{
                return nums[mid];
            }
        }
        return -1;
    }
};
```

```c++
class Solution {
public:
    int singleNonDuplicate(vector<int>& nums) {
        int l=0;
        int r=nums.size()-1;
        while(l<r){
            int mid=(l+r)>>1;
            bool flag=(r-mid)%2==0;
            if(nums[mid+1]==nums[mid]){
                if(flag){
                    l=mid+2;
                }
                else{
                    r=mid-1;
                }
            }
            else if(nums[mid-1]==nums[mid]){
                if(flag){
                    r=mid-2;
                }
                else{
                    l=mid+1;
                }
            }
            else{
                return nums[mid];
            }
        }
        return nums[l];
    }
};
```

