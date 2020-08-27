#### [1. 两数之和](https://leetcode-cn.com/problems/two-sum/)

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        vector<int> ans;
        unordered_map<int,int> map;
        int n=nums.size();
        for(int i=0;i<n;i++){
            if(map.count(target-nums[i])){
                return {i,map[target-nums[i]]};
            }
            else{
                map[nums[i]]=i;
            }
        }
        return ans;
    }
};
```

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        int n=nums.size();
        for(int i=0;i<n;i++){
            for(int j=i+1;j<n;j++){
                if(target==(nums[i]+nums[j]))return {i,j};
            }
        }
        return {};
    }
};
```

#### [15. 三数之和](https://leetcode-cn.com/problems/3sum/)

`3 Sum` 的问题其实就是一个 `2 Sum` 的升级版，因为 1 + 2 = 3 嘛。。

那就是外面一层循环，固定一个值，在剩下的数组里做 `2 Sum` 问题。

反正 `3 Sum` 怎么着都得 `O(n^2)` ，就可以先排序，反正不在乎排序的这点时间了，这样就可以用 `Two pointer` 来做了。

还需要注意的是，这道题返回的是**数值**，而非 `index`，所以它不需要重复的数值——The solution set must not contain duplicate triplets.

```c++
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> ans;
        sort(nums.begin(),nums.end());
        int n=nums.size();
        for(int i=0;i<n-1;i++){
            if(i>0 && nums[i]==nums[i-1])continue;
            int j=i+1;
            int k=n-1;
            int target=-nums[i];
            while(j<k){
                if(nums[j]+nums[k]==target){
                    ans.push_back({nums[i],nums[j],nums[k]});
                    j++;k--;
                    while(j<k && nums[j]==nums[j-1])j++;
                    while(j<k && nums[k]==nums[k+1])k--;
                }
                else if(nums[j]+nums[k]>target){
                    k--;
                }
                else{j++;}
            }
        }
        return ans;
    }
};
```

#### [16. 最接近的三数之和](https://leetcode-cn.com/problems/3sum-closest/)

排序+双指针

```c++
class Solution {
public:
    int threeSumClosest(vector<int>& nums, int target) {
        sort(nums.begin(),nums.end());
        int mincut=nums[0]+nums[1]+nums[2];
        int n=nums.size();
        for(int i=0;i<n-2;i++){
            int j=i+1,k=n-1;
            while(j<k){
                int sum3=nums[i]+nums[j]+nums[k];
                if(abs(sum3-target)<abs(mincut-target))mincut=sum3;
                if(sum3==target)return target;
                else if(sum3<target)j++;
                else k--;
            }
        }
        return mincut;
    }
};
```

#### [18. 四数之和](https://leetcode-cn.com/problems/4sum/)

这 4 个数，可以想成两两的 `2 Sum`，先把第一个 `2 Sum` 的结果存下来，然后在后续的数组中做第二个 `2 Sum`，这样就可以把时间降低到 `O(n^2)` 了。

这里要注意的是，为了**避免重复**，也就是下图的 `nums[x] + nums[y] + nums[z] + nums[k]` ，其实和 `nums[z] + nums[k] + nums[x] + nums[y]` 并没有区别，所以我们要**限制第二组的两个数要在第一组的两个数之后**哦。

```c++
class Solution {
public:
    vector<vector<int>> fourSum(vector<int>& nums, int target) {
        vector<vector<int>> set;
        unordered_map<int,vector<vector<int>>> map;
        sort(nums.begin(),nums.end());
        int n=nums.size();
        for(int i = 0; i < n - 3; i++) {
            for(int j = i + 1; j < n - 2; j++) {
                int currSum = nums[i] + nums[j];
                vector<vector<int>> pairs;
                if(map.count(currSum))pairs=map[currSum];
                pairs.push_back({i,j});
                map[currSum]=pairs;
            }
        }
        // 在其后做two sum
        for(int i = 2; i < n - 1; i++) {
            for(int j = i + 1; j < n; j++) {
                int currSum = nums[i] + nums[j];
                vector<vector<int>> prevPairs;
                if(map.count(target-currSum)){prevPairs=map[target-currSum];}
                else{continue;}
                for(vector<int> pair:prevPairs){
                    if(pair[1]<i){
                        set.push_back({nums[pair[0]],nums[pair[1]],nums[i],nums[j]});
                    }
                }
            }
        }
        sort(set.begin(),set.end());
        set.erase(unique(set.begin(),set.end()),set.end());
        return set;
    }
};


// class Solution {
//     public List<List<Integer>> fourSum(int[] nums, int target) {
//             Set<List<Integer>> set = new HashSet<>();
//     Map<Integer, List<List<Integer>>> map = new HashMap<>();
//     Arrays.sort(nums);
//     // 先处理第一对，把它们的sum存下来
//     for(int i = 0; i < nums.length - 3; i++) {
//       for(int j = i + 1; j < nums.length - 2; j++) {
//         int currSum = nums[i] + nums[j];
//         List<List<Integer>> pairs = map.getOrDefault(currSum, new ArrayList<>());
//         pairs.add(Arrays.asList(i, j));
//         map.put(currSum, pairs);
//       }
//     }
    
//     // 在其后做two sum
//     for(int i = 2; i < nums.length - 1; i++) {
//       for(int j = i + 1; j < nums.length; j++) {
//         int currSum = nums[i] + nums[j];
//         List<List<Integer>> prevPairs = map.get(target - currSum);
//         if(prevPairs == null) {
//             continue;
//         }
//         for(List<Integer> pair : prevPairs) {
//           if(pair.get(1) < i) {
//             set.add(Arrays.asList(nums[pair.get(0)], nums[pair.get(1)], nums[i], nums[j]));
//           }
//         }
//        }
//      }
//      return new ArrayList<>(set);
//     }
// }
```



```java
class Solution {
    public List<List<Integer>> fourSum(int[] nums, int target) {
            Set<List<Integer>> set = new HashSet<>();
    Map<Integer, List<List<Integer>>> map = new HashMap<>();
    Arrays.sort(nums);
    // 先处理第一对，把它们的sum存下来
    for(int i = 0; i < nums.length - 3; i++) {
      for(int j = i + 1; j < nums.length - 2; j++) {
        int currSum = nums[i] + nums[j];
        List<List<Integer>> pairs = map.getOrDefault(currSum, new ArrayList<>());
        pairs.add(Arrays.asList(i, j));
        map.put(currSum, pairs);
      }
    }
    
    // 在其后做two sum
    for(int i = 2; i < nums.length - 1; i++) {
      for(int j = i + 1; j < nums.length; j++) {
        int currSum = nums[i] + nums[j];
        List<List<Integer>> prevPairs = map.get(target - currSum);
        if(prevPairs == null) {
            continue;
        }
        for(List<Integer> pair : prevPairs) {
          if(pair.get(1) < i) {
            set.add(Arrays.asList(nums[pair.get(0)], nums[pair.get(1)], nums[i], nums[j]));
          }
        }
       }
     }
     return new ArrayList<>(set);
    }
}
```

```c++
class Solution {
public:
    vector<vector<int>> fourSum(vector<int>& nums, int target) {
        vector<vector<int>> result;
        sort(nums.begin(), nums.end());
        for (int k = 0; k < nums.size(); k++) {
            // 这中剪枝是错误的，这道题目target 是任意值 
            // if (nums[k] > target) {
            //     return result;
            // }
            // 去重
            if (k > 0 && nums[k] == nums[k - 1]) {
                continue;
            }
            for (int i = k + 1; i < nums.size(); i++) {
                // 正确去重方法
                if (i > k + 1 && nums[i] == nums[i - 1]) {
                    continue;
                }
                int left = i + 1;
                int right = nums.size() - 1;
                while (right > left) {
                    if (nums[k] + nums[i] + nums[left] + nums[right] > target) {
                        right--;
                    } else if (nums[k] + nums[i] + nums[left] + nums[right] < target) {
                        left++;
                    } else {
                        result.push_back(vector<int>{nums[k], nums[i], nums[left], nums[right]});
                        // 去重逻辑应该放在找到一个四元组之后
                        while (right > left && nums[right] == nums[right - 1]) right--;
                        while (right > left && nums[left] == nums[left + 1]) left++;

                        // 找到答案时，双指针同时收缩
                        right--;
                        left++;
                    }
                }

            }
        }
        return result;
    }
};

```

#### [49. 字母异位词分组](https://leetcode-cn.com/problems/group-anagrams/)

```c++
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        vector<vector<string>> ans;
        unordered_map<string,vector<string>> map;
        for(auto s:strs){
            string t=s;
            sort(t.begin(),t.end());
            map[t].push_back(s);
        }
        for(auto n:map){
            ans.push_back(n.second);
        }
        return ans;
    }
};
```

```c++
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        vector<vector<string>> res;
        unordered_map <double,vector<string> > m;
        double a[26]={2,3,5,7,11,13,17,19,23,29,31,37,41,43,47,53,59,61,67,71,73,79,83,89,97,101};
        for(string& s : strs)
        {
            double t = 1;
            for(char c : s)
            t *= a[c - 'a'];

            m[t].push_back(s);          //t为单词对应的质数乘积，m[t]则为该单词的异位词构成的vector
        }
        for(auto& n : m)                //n为键和值组成的pair
            res.push_back(n.second);
        return res;
    }
};

```

#### [149. 直线上最多的点数](https://leetcode-cn.com/problems/max-points-on-a-line/)

之前周赛写的

```c++
class Solution {
public:
    int maxPoints(vector<vector<int>>& points) {
        int n=points.size();
        if(n<3)return n;

        int Max=0;
        for(int i=0;i<n;i++){
            int same=1;
            for(int j=i+1;j<n;j++){
                int count=0;
                if(points[i][0]==points[j][0]&&points[i][1]==points[j][1])same++;
                else{
                    count++;
                    long long x = (long long) (points[i][0]-points[j][0]);
                    long long y = (long long) (points[i][1]-points[j][1]);

                    for(int k=j+1;k<n;k++){
                        if(x*(points[i][1]-points[k][1])==y*(points[i][0]-points[k][0]))count++;
                    }                   
                }
                Max=max(Max,same+count);                
            }
            //if(Max>n/2)return Max;
        }
        return Max;
    }
};
```

```c++
class Solution {
public:
    int g(int x, int y) {
        if (y == 0) return x;
        return g(y, x % y);
    }
    int maxPoints(vector<vector<int>>& points) {
        unordered_map<string,int> m;
        int n=points.size();
        if(n<3)return n;

        int Max=0;
        for(int i=0;i<n;i++){
            int same=1;
            for(int j=i+1;j<n;j++){
                int count=0;
                if(points[i][0]==points[j][0]&&points[i][1]==points[j][1])same++;
                else{
                    count++;
                    int x = (points[i][0]-points[j][0]);
                    int y = (points[i][1]-points[j][1]);

                    int gcd = g(x, y);
                    string key = to_string (x / gcd)+ "-" + to_string (y / gcd);
                    // cout<<m.count(key)<<endl;
                    if(m.find(key)==m.end()){
                        m[key]=1;
                        //cout<<m[key]<<endl;                        
                    }
                    else{
                        m[key]++;
                    }

                    // for(int k=j+1;k<n;k++){
                    //     if(x*(points[i][1]-points[k][1])==y*(points[i][0]-points[k][0]))count++;
                    // }                   
                }
                     
            }
            if (m.empty())
                    Max = max(Max, same);
                else {
                    for (auto val : m) //cout<<val.second<<endl;
                        Max = max(Max, val.second + same); 
                } 
                m.clear();     
            //if(Max>n/2)return Max;
        }
        return Max;
    }
};
```

#### [217. 存在重复元素](https://leetcode-cn.com/problems/contains-duplicate/)

```c++
class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
        unordered_map<int,int> m;
        for(int num:nums){
            if(m.count(num))return true;
            m[num]=1;
        }
        return false;
    }
};
```

#### [219. 存在重复元素 II](https://leetcode-cn.com/problems/contains-duplicate-ii/)

```c++
class Solution {
public:
    bool containsNearbyDuplicate(vector<int>& nums, int k) {
        unordered_map<int,int> m;
        int i=0;
        for(int num:nums){
            if(m.count(num)){
                if(i-m[num]<=k)return true;
            }
            m[num]=i;
            i++;
        }
        return false;
    }
};
```

#### [220. 存在重复元素 III](https://leetcode-cn.com/problems/contains-duplicate-iii/)

我们换种思路。 由于本题对索引有要求，因此直接排序破坏了原来的索引的做法是不行的。 我们考虑使用桶排序。

- 我们将数据分到 M 个桶 中。

- 每个数字nums[i] 都被我们分配到一个桶中

- 分配的依据就是 nums[i] // (t + 1)

- 这样相邻桶内的数字最多相差2 * t + 1

- 不相邻的桶一定不满足相差小于等于t

- 同一个桶内的数字最多相差t

  

  1. 因此如果命中同一个桶内，那么直接返回True

  2. 如果命中相邻桶，我们再判断一下是否满足 相差 <= t

  3. 否则返回False

     **需要注意的是，由于题目有索引相差k的要求，因此要维护一个大小为k的窗口，定期清除桶中过期的数字。**

[fe-lucifer](https://leetcode-cn.com/problems/contains-duplicate-iii/solution/220-cun-zai-zhong-fu-yuan-su-iii-cong-on2-dao-on-p/)

```c++
class Solution {
public:
    bool containsNearbyAlmostDuplicate(vector<int>& nums, int k, int t) {
        if(t<0)return false;
        //t+1可能会溢出，所以要+ 1LL
        long long mod=t+1LL;
         unordered_map<long long,long long> buck;
        for(int i=0;i<nums.size();i++){
            long long nth = nums[i] / mod;
            //可能nums[i]为负数，比如-4 / 5 以及 -4 / 5都等于0，所以负数要向下移动一位
            if(nums[i] < 0) nth--;
            //这里要用find 不能直接[],因为可能本身存储的数字就为0
            if(buck.find(nth)!=buck.end()) 
                return true;
            else if(buck.find(nth-1)!=buck.end() && abs(nums[i] - buck[nth-1]) <= t)
                return true;
            else if(buck.find(nth+1)!=buck.end() && abs(nums[i] - buck[nth+1]) <= t)
                return true;
            buck[nth] = nums[i];
            if(i >= k)
            {
                buck.erase(nums[i - k] / mod);
            }
        }
        return false;
    }
};
```

#### [447. 回旋镖的数量](https://leetcode-cn.com/problems/number-of-boomerangs/)

```c++
class Solution {
public:
    int numberOfBoomerangs(vector<vector<int>>& points) {
        int ans=0;
        unordered_map<int,int> m;

        for(int i=0;i<points.size();i++){
            int a=points[i][0];
            int b=points[i][1];
            for(int j=0;j<points.size();j++){
                if(i==j)continue;
                //printf("%d,%d\n",points[i][0],points[i][1]);
                //printf("%d,%d\n\n",points[j][0],points[j][1]);
                int dis1=(a-points[j][0])*(a-points[j][0])+(b-points[j][1])*(b-points[j][1]);
                // if(m.count(dis1)==1)ans++;
                // else{
                //     m[dis1]=1;
                // }
                m[dis1]++;
            }
            for(auto v:m){
                ans += v.second * (v.second - 1);
            }
            m.clear();
        }
        return ans;
    }
};
```

```c++
class Solution {
public:
    int numberOfBoomerangs(vector<vector<int>>& points) {
        unordered_map<double, int> p_map;
        int count = 0;
        for (int i = 0; i < points.size(); ++i) {
        // 记得计算下一个点前要先把哈希表清空
        p_map.clear();
        for (int j = 0; j < points.size(); ++j) {
          double dis = sqrt(pow(points[i][0] - points[j][0], 2) + pow(points[i][1] - points[j][1], 2));
          p_map[dis]++;
          count += (p_map[dis] - 1) * 2;
        }
      }
      return count;
    }
};
```

#### [454. 四数相加 II](https://leetcode-cn.com/problems/4sum-ii/)

```c++
class Solution {
public:
    int fourSumCount(vector<int>& A, vector<int>& B, vector<int>& C, vector<int>& D) {
        unordered_map<int, int> umap; //key:a+b的数值，value:a+b数值出现的次数
        // 遍历大A和大B数组，统计两个数组元素之和，和出现的次数，放到map中 
        for (int a : A) {
            for (int b : B) {
                umap[a + b]++;
            }
        }
        int count = 0; // 统计a+b+c+d = 0 出现的次数
        // 在遍历大C和大D数组，找到如果 0-(c+d) 在map中出现过的话，就把map中key对应的value也就是出现次数统计出来。
        for (int c : C) {
            for (int d : D) {
                if (umap.find(0 - (c + d)) != umap.end()) {
                    count += umap[0 - (c + d)];
                }
            }
        }
        return count;
    }
};
```

