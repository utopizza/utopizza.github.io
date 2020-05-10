# java位运算符优先级



一、问题

leetcode第190题，逆序所输入的整数的32位bit，构成一个新的整数并输出：[190. Reverse Bits][1]

二、解决

思路不难，对输入的整数$n$，不断右移（带符号）并取其最低位（与上1），加到返回的整数上；返回的整数每次接收$n$的1位后，左移1位腾出最低位给下一次接收。如此循环32次即可。

```
public class Solution {
    public int reverseBits(int n) {
        int result=0;
        for(int i=0;i<32;i++){
            result=result<<1;
            result=result+(n&1);
            n=n>>>1;
        }
        return result;
    }
}
```

我一开始没有注意四则运算符和位运算符的优先级，默认了位运算是高于加减乘除的，所以上面第6行处我一开始写成：

```
result = result + n&1;
```

然后一直AC失败。我反复检查了我的思路也找不到出错的原因。无奈之下查看了discuss，发现我的思路和高票答案是一模一样的。不过别人的写法是：

```
result += n&1;
```

此时我才醒悟难道是运算符优先级的问题？我立马尝试加上括号，发现AC成功。后来查了资料才知道加减运算符的优先级是比按位与运算符的优先级高的：

```
int a = 2;
int b = 0;
int c = a + b & 1;
int d = a + (b & 1);
System.out.print("c=" + c + " , " + "d=" + d);
```

输出结果为：c=0，d=2

这件事充分说明了学习基础不扎实，需要深刻反省引以为戒，因此写到博客里记录一下。

  [1]: https://leetcode.com/problems/reverse-bits/description/

