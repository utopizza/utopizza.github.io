# 二分查找



一、原始二分查找

二分查找的思想很简单，就是在一个有序序列里查找一个目标时，先与中位数比较，如果目标等于中位数，则直接返回。如果目标小于中位数，就去前半部分继续查找。如果目标大于中位数，就去后半部分查找，如此重复直至不可再分。如果找到则返回 mid，否则返回 -1 表示序列中不存在改目标。

```
public int BinarySearch(int[] nums, int target) {
    int lo = 0, hi = nums.length - 1;
    while (lo <= hi) {
        int mid = lo + (hi - lo) / 2;
        if (nums[mid] == target) return mid;
        else if (nums[mid] > target) hi = mid - 1;
        else lo = mid + 1;
    }
    return -1;
}
```

二、半有序数组的二分查找

进阶问题，在一个部分有序（这里只考虑升序）的旋转数组里，使用二分查找来找到最大或者最小的值。所谓旋转数组，就是一个已按升序排序的数组，循环左移或者右移若干位，例如 $[4,5,6,0,1,2,3]$。先给出寻找最小值的代码。

```
public int BinarySearchMinForRotateArray(int[] nums) {
    int lo = 0, hi = nums.length - 1;
    while (lo < hi) {
        int mid = (lo + hi) / 2;
        if (nums[mid] > nums[nums.length-1]) lo = mid + 1;
        else hi = mid;
    }
    return lo;
}
```

下面是寻找最大值的代码，之前我写不出来，当数组是完全有序（也即最小值是第一个数，最大值是最后一个数）的时候，程序会死循环。后发现使用一个小技巧：`mid = (lo + hi + 1) / 2` 就可以解决这个问题。因为这样可以使得 mid 指针向右偏，因为数组是升序的，最大值会在最小值的左边，所以能够调整到刚好在找到最大值的时候退出循环。这里有些复杂的边界调整，暂时还没整理出一个规律。

```
public int BinarySearchMaxForRotateArray(int[] nums) {
    int lo = 0, hi = nums.length - 1;
    while (lo < hi) {
        int mid = (lo + hi + 1) / 2;
        if (nums[mid] < nums[0]) hi = mid - 1;
        else lo = mid;
    }
    return lo;
}
```

三、二分查找思想实现整数除法运算

应用问题，要求不能用乘法、除法和模运算来实现一个除法。为了简单表示，这里暂不考虑小数除法和溢出的问题。思路是，通过不断把除数加倍，来逼近被除数。如果某次加倍后会超过被除数，则用被除数减去当前的除数累加和，把剩下的差作为新的逼近目标，重复调用自身来递归地逼近。

```
// a/b
public int dividedByBinarySearch(int a, int b) {
    if (a < b) return 0;
    int sum = b;
    int multiply = 1;
    while (sum + sum <= a) {
        sum += sum;
        multiply += multiply;
    }
    return multiply + dividedByBinarySearch(a - sum, b);
}
```

四、二分查找寻找指定元素的起始与结束下标

给定一个升序的整型数组，里面的元素会重复若干个，使用二分查找确定指定元素的起始和结束位置。例如，给定 [1,2,3,4,4,4,4,5]，指定目标为 4，则返回 [3,6]。思路是使用两次二分查找，分别向左和向右找最顶端的指定元素。这个例子应该能比较好地说明 `mid = (lo + hi + 1) / 2` 这个技巧，目的是保证 mid 指针一直等于 target。

```
public int[] BinarySearchRange(int[] nums, int target) {
    int[] ret = new int[2];
    ret[0] = -1;
    ret[1] = -1;
    if (nums == null || nums.length == 0) return ret;

    // find the left top
    int lo = 0, hi = nums.length - 1;
    while (lo < hi) {
        int mid = (lo + hi + 1) / 2;
        if (target <= nums[mid]) hi = mid;
        else lo = mid + 1;
    }
    if (nums[lo] == target) ret[0] = lo;

    // find the right top
    lo = 0;
    hi = nums.length - 1;
    while (lo < hi) {
        int mid = (lo + hi) / 2;
        if (target >= nums[mid]) lo = mid;
        else hi = mid - 1;
    }
    if (nums[lo] == target) ret[1] = lo;

    return ret;
}
```

