# 字符串大整数相乘


在 leetcode 上看到一个巨强的解法，记录一下以加深记忆。原文参见：[Easiest JAVA Solution with Graph Explanation][1]

题目：[Multiply Strings][2]

题目大意为，给定两个字符串形式的非负整数，求它的乘积，以字符串形式返回。并且：

 1. 两个字符串的长度小于 110.
 2. 两个字符串只包含 0 到 9 的数字.
 3. 两个字符串不以任何 ‘0’ 开头.
 4. 不能使用编程语言内置的大整数库函数，也不能直接转换成整数求解.

leetcode 上某大神给出的思路：

 1. 位数分别为 $m$ 和 $n$ 的两个整数相乘，乘积的位数最大不超过 $m+n$
 2. 从左至右地，乘数的第 $[i]$ 位与被乘数的第 $[j]$ 位相乘的积，将被加到最终结果的第 $[i+j]$ 位和第 $[i+j+1]$ 位

![](/2018-01-29-算法-字符串大整数相乘/123.jpg)

因此，整个计算过程就可以直接模拟我们小学就学的竖式乘法计算过程。这也是令我惊讶的地方，小学就学会了的竖式乘法计算，但是十几年来（我）都没有发现这两个十分有用的规律

```
public static String multiply(String num1, String num2) {
    int m = num1.length();
    int n = num2.length();
    int[] pos = new int[m + n];

    for (int i = m - 1; i >= 0; i--) {
        for (int j = n - 1; j >= 0; j--) {
            int mul = (num1.charAt(i) - '0') * (num2.charAt(j) - '0');
            int p1 = i + j;
            int p2 = i + j + 1;
            int sum = mul + pos[p2];

            pos[p1] += sum / 10;
            pos[p2] = sum % 10;
        }
    }

    StringBuilder sb = new StringBuilder();
    for (int p : pos)
        if (!(sb.length() == 0 && p == 0))
            sb.append(p);

    return sb.length() == 0 ? "0" : sb.toString();
}
```
 
 
  [1]: https://leetcode.com/problems/multiply-strings/discuss/17605/Easiest-JAVA-Solution-with-Graph-Explanation
  [2]: https://leetcode.com/problems/multiply-strings/description/

