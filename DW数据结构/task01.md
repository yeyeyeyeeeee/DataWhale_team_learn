# DW数据结构与算法学习任务----Task01：数组

**1. 利用动态数组解决数据存放问题。**

思路：vector容器

```c++
include<iostream>
include<vector>

using nammespace std;

int main()
{
    int n;
    cin>>n;	//输入n个数据
    vector<int> res;
    for(int i = 0;i < n;i++){
        int temp;
        cin>>temp;
        res.push_back(temp)
    }
    
    
    return 0;
}
```



**[2.托普利茨矩阵问题](<https://leetcode-cn.com/problems/toeplitz-matrix/>)**

```c++
class Solution {
public:
    bool isToeplitzMatrix(vector<vector<int>>& matrix) {
        int m = matrix.size(), n = matrix[0].size();
        int p = m - 1, q = 0;
        while(p >= 0 && q < n){
            int val = matrix[p][q];
            int i = p, j = q;
            while(i+1 < m && j+1 < n){
                if(matrix[i+1][j+1] != val) return false;
                i++,j++; 
            }
            if(p > 0) p--;
            else q++;
        }
        return true;
    }
};
```

**[3.三数之和](<https://leetcode-cn.com/problems/3sum/>)**

```c++
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> res;
        sort(nums.begin(),nums.end());
        if (nums.empty() || nums.back() < 0 || nums.front() > 0) return {};
        for(int i = 0;i < ((int)nums.size()-2); i++){
            if(nums[i] > 0) break;
            if(i > 0 && nums[i] == nums[i-1])   continue;
            int j = i + 1, k = (int)nums.size() - 1;
            int target = 0 - nums[i];
            while(j < k){
                if(target == (nums[j] + nums[k])) {
                    res.push_back({nums[i],nums[j],nums[k]});
                    while(j < k && nums[j] == nums[j+1]) j++;
                    while(j < k && nums[k] == nums[k-1]) k--;
                    j++; k--;
                }
                else if((nums[j]+nums[k]) < target) j++;
                else k--;
            }
        }
        return res;
    }
};
```



