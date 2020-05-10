# 位运算技巧



一、6种基本位操作

|符号|描述|运算规则|
|:-:|:-:|:-:|
| & |And（按位与）|对应位上同时为 1 时，结果才为 1，否则为 0|
| $\mid$ |Or（按位或）|对应位上同时为 0 时，结果才为 0，否则为 1|
| ~ |Not（取反）|1 变 0，0 变 1|
| ^ |Xor（异或）|对应位上相同时输出 0，相异时输出 1|
| << |Left Shift（左移）|各二进位全部左移若干位，高位丢弃，低位补 0|
| \>> |Right Shift（右移）|各二进位全部右移若干位，低位丢弃，高位分情况|


注：

 1. 计算机内部使用补码作为机器数，正数的补码等于原码，负数的补码等于原码的符号位外取反加一，切记。另外注意机器字长，如果题目没有明确说明，一般默认是32位或64位
 2. 这6种操作符中，只有~取反是单目操作符，其它5种都是双目操作符
 3. 位操作只能用于整型数据
 4. 位操作运算优先级比较低（比加减乘除低），因此一般需要使用括号来保证正确的运算顺序，否则容易出错
 5. 右移运算（\>>）时，对无符号数，高位补0。对有符号数，各编译器处理方法不一样，有的补符号位（算术右移），有的补0（逻辑右移）
 6. Java中的右移（\>>）执行的是算术右移，高位补符号位；Java中的无符号（逻辑）右移有单独的运算符号：\>>>
 7. 另外位操作还有一些复合操作符，如 &=、|=、^=、<<=、\>>= 等
 
二、常用技巧

1、计算给定的整数对应的二进制数中 1 的个数。

```
public int countOnes(int n) {
    int count=0;
    while(n!=0) {
        n = n&(n-1);
        count++;
    }
    return count;
}
```

2、判断一个整数是否是 4 的 n 次方。思路很清晰，4 的 n 次方即为 2 的 2n 次方，因此其二进制形式必定是由一个 1 和偶数个 0 组成。

```
public boolean isPowerOfFour(int num) {
    int count0=0;
    int count1=0;
    while(num>0){
        if((num&1)==1){
            count1++;
        }else{
            count0++;
        }
        num>>=1;
    }
    return count1==1 && (count0%2==0);
}
```

3、仅用位运算求两个整数的和。

```
public int getSum(int a, int b) {
    //return b==0? a:getSum(a^b, (a&b)<<1);
    while(b!=0){
        int temp=a^b;
        b=(a&b)<<1;
        a=temp;
    }
    return a;
}
```

4、找出缺失的数。给定一个整型数组，每个数字都出现了两次，现在缺失了一个数字，即有一个数字只出现一次，找出这个数字。思路：利用异或运算的两个特性， 自己与自己异或结果为0，异或满足交换律

```
public int missingNumber(int[] nums) {
    int ret = 0;
    for (int i = 0; i < nums.length; ++i) {
        ret ^= nums[i];
    }
    return ret;
}
```

5、找出缺失的数。给定一个整型数组，长度为 $n$，取值范围为 $[0,...,n]$。找出缺失的那一个数。例如给定 $[0,1,3]$，返回 2。思路同上，把数组的 n 个数和完整的序列 $[0,...,n]$ 异或起来，最后结果就是只出现一次的也即在数组中缺失的那个数。

```
public static int missingNumber(int[] nums) {
    int ret = 0;
    for (int i = 0; i < nums.length; ++i) {
        ret ^= i;
        ret ^= nums[i];
    }
    return ret ^ nums.length;
}
```

6、判断奇偶。只要根据最未位是 0 还是 1 来决定，为 0 就是偶数，为 1 就是奇数。因此可以用 if((a & 1) == 0) 代替 if(a % 2 == 0) 来判断 a 是不是偶数。

7、不使用第三方变量交换两个数。利用一个数异或上自己等于 0 （消去这个数）的性质。

```
public void swap(int a, int b)  
{  
    if (a != b)  
    {  
        a ^= b;  
        b ^= a;  
        a ^= b;  
    }  
}
```

8、变换符号，即最高位 1 变 0，0 变 1。把该数取反加 1 即可。

```
public int signReversal(int a)  
{  
    return ~a + 1;  
}  
```

9、取绝对值。由上述可知对于负数可以通过对其取反加一来得到正数。因此现在只要判断输入的数是正数还是负数，如果是正数就直接输出，是负数就取反加一。对输入的数算术右移31位，由于高位补的是符号位，因此右移后如果原来是正数那么结果就是 0（0x 0000 0000），如果是负数那么右移的结果是 -1（0x FFFF FFFF）。

```
public int abs(int a)  
{  
    int i = a >> 31;  
    return i == 0 ? a : (~a + 1);  
} 
```

如果进一步考虑，注意到任何数与 0 异或等于自己，与 -1 异或等于取反，而代码中的 i 要么是 0，要么是 -1，因此 a 与 i 异或再减去 i 和原代码等价，并且这样省去了判断。

```
public int abs(int a)  
{  
    int i = a >> 31;  
    return (a ^ i) - i;  
} 
```

10、逆序一个二进制数。这里只关注正整数的有效位，例如，正整数 11 的二进制是 1011，逆序后得到 1101，输出 13。思路是对输入的数不断地取最低位并右移，取到的位赋到输出结果的最低位并不断左移。当输入的数减小到 0 时，算法完成。

```
public int reverseBit(int n) {
    int mask = 1, res = 0;
    while (n != 0) {
        res <<= 1;
        res |= (n & mask);
        n >>= 1;
    }
    return res;
}
```

11、逆序一个 32 位无符号整数的二进制。我们知道机器码是最高位是用于表示符号位的，但是 Java 中没有无符号整数的数据类型，怎办呢？


三、参考资料

 1. [位操作基础篇之位操作全面总结][1]
 2. [leetcode-A summary: how to use bit manipulation to solve problems easily and efficiently][2]
 3. [Stack Overflow-Reverse bits in number][3]
 4. [Stack Overflow-How can I invert bits of an unsigned byte in Java?][4]


  [1]: http://blog.csdn.net/morewindows/article/details/7354571
  [2]: https://leetcode.com/problems/sum-of-two-integers/discuss/84278/
  [3]: https://stackoverflow.com/questions/3165776/reverse-bits-in-number
  [4]: https://stackoverflow.com/questions/3324707/how-can-i-invert-bits-of-an-unsigned-byte-in-java

