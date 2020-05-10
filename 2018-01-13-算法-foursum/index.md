# FourSum



一、TwoSum

给定一个整型数组，和一个 target，返回数组中两个数之和等于 target 的所有组合。不能输出重复组合。例如给定数组 [2,3,1,-4,8,3]，target = 4，则满足条件的非重复组合有 [3,1] 和 [-4,8]。

思路：因为要求输出的是数组元素而不是数组下标，因此可以对数组进行排序（注意，如果题目要求输出数组下标，就不应使用排序，而是使用 HashMap 来优化）。排序之后，使用两个指针分别从数组两头往中间移动搜索整个数组，为避免重复组合，需跳过重复的元素。当指针相遇时搜索结束，因此搜索的时间复杂度为 $O(n)$。（其实如果算上前面的排序操作那么时间复杂度不止$O(n)$。因为所有基于比较的排序算法的时间复杂度下界为 $O(nlogn)$，所以本算法的总时间复杂度实际上应是 $O(n+nlogn)=O(nlogn)$）


```
public List<List<Integer>> TwoSum(int[] nums, int target) {
    List<List<Integer>> list = new ArrayList<List<Integer>>();
    Arrays.sort(nums);
    int lo = 0, hi = nums.length - 1;
    while (lo < hi) {
        int sum = nums[lo] + nums[hi];
        if (sum == target) {
            list.add(Arrays.asList(nums[lo], nums[hi]));
            while (lo < hi && nums[lo] == nums[lo + 1]) lo++;
            while (lo < hi && nums[hi] == nums[hi - 1]) hi--;
            lo++;
            hi--;
        } else if (sum > target) hi--;
        else lo++;
    }
    return list;
}
```

二、ThreeSum

上述题目的改进版，给定整型数组和一个 target，输出数组中三个数之和等于 target 的所有非重复组合。

思路：可以转化成 TwoSum 问题，即对原数组中第 $i$ 个元素 $nums[i]$，用 target 减去这个元素的差作为剩下的子数组 $nums[i+1,\cdots,n-1]$ 的 TwoSum 问题的 target。相当与对每一个元素调用一次 TwoSum 来找剩下的两个元素，因为 TwoSum 的时间复杂度为 $O(n)$，因此 ThreeSum 的时间复杂度为 $O(n^2)$。

```
public List<List<Integer>> ThreeSum(int[] nums, int target) {
    List<List<Integer>> list = new ArrayList<List<Integer>>();
    if (nums == null || nums.length < 3) return list;

    Arrays.sort(nums);
    
    // ThreeSum
    for (int i = 0; i < nums.length - 2; i++) {
        if (i == 0 || nums[i] != nums[i - 1]) {

            // TwoSum
            int lo = i + 1, hi = nums.length - 1, t = target - nums[i];
            while (lo < hi) {
                int sum = nums[lo] + nums[hi];
                if (sum == t) {
                    list.add(Arrays.asList(nums[lo], nums[hi], nums[i]));
                    while (lo < hi && nums[lo] == nums[lo + 1]) lo++;
                    while (lo < hi && nums[hi] == nums[hi - 1]) hi--;
                    lo++;
                    hi--;
                } else if (sum > t) hi--;
                else lo++;
            }

        }
    }
    return list;
}
```

三、FourSum

再次改进版，给定一个整型数组和一个 target，求数组中四个元素之和等于 target 的所有非重复组合。

思路：类似地，先排序，然后选定一个元素，把剩下的右边子数组看作 ThreeSum 问题。推理同上，时间复杂度为 $O(n^3)$。

```
public static List<List<Integer>> FourSum(int[] nums, int target) {
    List<List<Integer>> ret = new ArrayList<List<Integer>>();
    Arrays.sort(nums);

    // four sum
    for (int i = 0; i < nums.length - 3; i++) {
        if (i == 0 || nums[i] != nums[i - 1]) {

            // three sum
            for (int j = i + 1; j < nums.length - 2; j++) {
                if (j == i + 1 || nums[j] != nums[j - 1]) {

                    // two sum
                    int lo = j + 1, hi = nums.length - 1, t = target - nums[i] - nums[j];
                    while (lo < hi) {
                        int sum = nums[lo] + nums[hi];
                        if (sum == t) {
                            ret.add(Arrays.asList(nums[i], nums[j], nums[lo], nums[hi]));
                            while (lo < hi && nums[lo] == nums[lo + 1]) lo++;
                            while (lo < hi && nums[hi] == nums[hi - 1]) hi--;
                            lo++;
                            hi--;
                        } else if (sum > t) hi--;
                        else lo++;
                    }

                }
            }

        }
    }

    return ret;
}
```

