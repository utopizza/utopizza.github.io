# 回溯法


回溯法 （Backtracking） 是一种选优搜索法，又称为试探法，按选优条件向前搜索，以达到目标。但当探索到某一步时，发现原先选择并不优或达不到目标，就退回一步重新选择，这种走不通就退回再走的技术为回溯法，而满足回溯条件的某个状态的点称为“回溯点”。（摘自百度百科）

此方法一般适用于求“有多少种解”的题目，如N皇后问题的“一共有多少种走法”，数独问题的“一共有多少种填法”，数组和问题的“一共有多少种组合使得数组中的某些元素之和等于目标值”等等。这种问题的特点是搜索空间很大，并且往往伴有约束条件，如N皇后问题的“每个皇后不能在同一直线上”，数独问题的“每行每列每个小九宫格都由1到9组成并且不重复”，数组和问题的“元素之和等于给定目标值”。善于利用这些约束条件，往往可以跳过大量的不可能存在解的搜索空间，极大地减少计算量。此方法也称“剪枝法”。

下面是 leetcode 的一些相关题目的解法。

一、数组和

1、[Combination Sum][1]

```
public List<List<Integer>> combinationSum(int[] nums, int target) {
    List<List<Integer>> ret = new ArrayList<>();
    Arrays.sort(nums);
    backtrack(ret,new ArrayList<>(),nums,target,0);
    return ret;
}

public void backtrack(List<List<Integer>> soluList, List<Integer> solu, int[] nums, int remain, int start){
    if(remain<0) return;
    else if(remain==0) soluList.add(new ArrayList(solu));
    else{
        for(int i=start;i<nums.length;i++){
            solu.add(nums[i]);
            backtrack(soluList,solu,nums,remain-nums[i],i);
            solu.remove(solu.size()-1);
        }
    }
}
```

2、[Combination Sum II][2]

```
public List<List<Integer>> combinationSum2(int[] nums, int target) {
    List<List<Integer>> ret=new ArrayList<>();
    Arrays.sort(nums);
    backtrack(ret,new ArrayList<>(),nums,target,0);
    return ret;
}

private void backtrack(List<List<Integer>> soluList, List<Integer> solu, int[] nums, int remain, int start){
    if(remain<0) return;
    else if(remain==0) soluList.add(new ArrayList(solu));
    else{
        for(int i=start;i<nums.length;i++){
            if(i>start&&nums[i-1]==nums[i]) continue;
            solu.add(nums[i]);
            backtrack(soluList,solu,nums,remain-nums[i],i+1);
            solu.remove(solu.size()-1);
        }
    }
}
```

二、子集组合

1、[Subsets][3]

```
public List<List<Integer>> subsets(int[] nums) {
    List<List<Integer>> soluList=new ArrayList<>();
    backtrack(soluList, new ArrayList<>(), nums, 0);
    return soluList;
}

private void backtrack(List<List<Integer>> soluList, List<Integer> solu, int[] nums, int start){
    soluList.add(new ArrayList<>(solu));
    for(int i=start;i<nums.length;i++){
        solu.add(nums[i]);
        backtrack(soluList,solu,nums,i+1);
        solu.remove(solu.size()-1);
    }
}
```

2、[Subsets II][4]

```
public List<List<Integer>> subsetsWithDup(int[] nums) {
    List<List<Integer>> soluList=new ArrayList<>();
    Arrays.sort(nums);
    backtrack(soluList,new ArrayList<>(),nums,0);
    return soluList;
}

private void backtrack(List<List<Integer>> soluList, List<Integer> solu, int[] nums, int start){
    soluList.add(new ArrayList<>(solu));
    for(int i=start;i<nums.length;i++){
        if(i>start&&nums[i-1]==nums[i]) continue;
        solu.add(nums[i]);
        backtrack(soluList,solu,nums,i+1);
        solu.remove(solu.size()-1);
    }
}
```

三、全排列

1、[Permutations][5]

```
public List<List<Integer>> permute(int[] nums) {
    List<List<Integer>> soluList=new ArrayList<>();
    backtrack(soluList,new ArrayList<>(),nums);
    return soluList;
}
    
private void backtrack(List<List<Integer>> soluList, List<Integer> solu, int[] nums){
    if(solu.size()==nums.length) soluList.add(new ArrayList<>(solu));
    else{
        for(int i=0;i<nums.length;i++){
            if(solu.contains(nums[i])) continue;
            solu.add(nums[i]);
            backtrack(soluList, solu, nums);
            solu.remove(solu.size()-1);
        }
    }
}
```

2、[Permutations II][6]

```
public List<List<Integer>> permuteUnique(int[] nums) {
    List<List<Integer>> soluList=new ArrayList<>();
    Arrays.sort(nums);
    backtrack(soluList,new ArrayList<>(),nums,new boolean[nums.length]);
    return soluList;
}

private void backtrack(List<List<Integer>> soluList, List<Integer> solu, int[] nums, boolean[] used){
    if(solu.size()==nums.length) soluList.add(new ArrayList<>(solu));
    else{
        for(int i=0;i<nums.length;i++){
            if(used[i]) continue;
            if(i>0&&nums[i-1]==nums[i]&&!used[i-1]) continue;
            used[i]=true;
            solu.add(nums[i]);
            backtrack(soluList,solu,nums,used);
            solu.remove(solu.size()-1);
            used[i]=false;
        }
    }
}
```


  [1]: https://leetcode.com/problems/combination-sum/description/
  [2]: https://leetcode.com/problems/combination-sum-ii/description/
  [3]: https://leetcode.com/problems/subsets/description/
  [4]: https://leetcode.com/problems/subsets-ii/description/
  [5]: https://leetcode.com/problems/permutations/description/
  [6]: https://leetcode.com/problems/permutations-ii/description/

