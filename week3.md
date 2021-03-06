# Mafia Week 3

Date Time: 2017-12-18

This week Pei  presented **bit mamnipulation**.

# Content

* ['&' operator](#andopt)
* ['|' operator](#oropt)
* ['^' operator](#xoropt)
* [Representing & Manipulating Sets](#represent)
* [Problems](#problems)

**[Reference](#ref)**

#Bit Manipulation

### '&' operator {#andopt}


**Test n-th bit:**

```python
if num & 1 << n: 
    #if n-th bit is 1
    doSomething()
else: 
    #if n-th bit is 0
    doSomething()
```

**Clear rightmost '1':**

```python
n & (n - 1)
#Explaination:
#n     = XXXXX100
#n - 1 = XXXXX011
#-----------------
#    & = XXXXX000
```

**Extract right most '1':**

```python
n & ~(n - 1) # or n & -x
#Explaination:
#n        = xxxxx100
#~(n - 1) = x̄x̄x̄x̄x̄100
#--------------------
#    &    = 00000100
```

**Examples**:

Count number of 1

```python
def CountNumberOfOne(num):
    count = 0
    while num :
        num &= (num - 1)
        count += 1
    return count
```

Power of Two

```python
def isPowerOfTwo(num):
    return num > 0 and not num & num - 1
```

## '\|' operator {#oropt}
set n-th bit to 1
```python
def setBitN1(x, n):
    return x | (1<<(n - 1))
```

set consecutive k bits to 1
```python
def setNbitsto1(x, k):
    return x | ((1<<k)-1)
```

set consecuitive 0 to 1
```
x|(x-1)
```



## '^' operator{#xoropt}
```
x ^ 0 = x

x ^ x = 0

x ^ 0xF...F = ~x     //depends on the length of bits

x ^ (-x) = 0xF...F  //depends on the length of bits

a ^ b = c => a ^ c = b, b ^ c = a // swap

a ^ b ^ c = a ^ (b ^ c) = (a ^ b) ^ c // associative
```

**Example**:

```python
//swap two variables
def swap(a, b):
    a = a^b
    b = a^b
    a = a^b
    return a, b

```



## Representing & Manipulating Sets{#represent}




We can use w bit vector presents subset of {0, 1, 2 .... w-1}.


$$
a\_j = 1 \space if \space j\in A
$$


Assume the original set is {0, 1, 2, 3, 4, 5, 6, 7}.

01101001 represent the set {0, 3, 5, 6}

7**65**4**3**21**0**

01010101 reprenset the set {0, 2, 4, 6}

7**6**5**4**3**2**1**0**

The operations between two bit vector could represent the operation between two sets.



* ### '&' \(AND\) operation represents Intersection

    01101001 & 01010101 = 01000001 represents {0, 6}
* ### '|' (OR) operation represents Union

    01101001 | 01010101 = 01111101 represents {0, 2, 3, 4, 5, 6}
    
* ### '^' (XOR) operation represents Symmetric difference

    01101001 ^ 01010101 = 00111100 represents {2, 3, 4, 5}

* ### '~' (NOT) operation represents complement
    ~01101001 = 10010110 represents {1, 2, 4, 7}

    ~01010101 = 10101010 represents {1, 3, 5, 7}

##Problems{#problems}


###[Leetcode 78.Subsets](https://leetcode.com/problems/subsets/)

This problem can be solved by backtracking. Meanwhile, we can also use bit manipulation to solve it. We use bit to present the set.
Assume there are n element in the set. The count of the subset would be $$2^n$$, which could be represented by $$[ 0\space to \space (2^n-1)]$$. So we just need to iterate the number from 0 to $$(2^n-1)$$ and convert them to set.
```python
class Solution(object):
    def subsets(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        res = []

        def convert2set(val, nums, length):
            set_list = []
            for i in range(length):
                if val & (1 << i):
                    set_list.append(nums[i])
            return set_list

        length = len(nums)
        count = 1 << length
        for i in range(count):
            res.append(convert2set(i, nums, length))
        return res
```

###[Leetcode 136. Single Number](https://leetcode.com/problems/single-number/)
The key to solve this problem is the <cite>[The Magic of XOR][3]</cite>
. 

```
x^x = 0
0^x = x
x^y^x = x^x^y = y
```

```python
class Solution(object):
    def singleNumber(self, nums):
        ret = 0
        for i in nums:
            ret ^= i
        return ret

```



###[leetcode 137. Single Number II](https://leetcode.com/problems/single-number-ii/)
This problem can be solve by math.
```python
class Solution(object):
    def singleNumber(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        if [] or len(nums) == 1:
            return sum(nums)
        return (3 * sum(set(nums)) - sum(nums))/2
```
This problem also can be solved by bit manipulation. We use 32 bit binary number to present the target. Fro example the input list is [2, 5, 5, 5]. At first, we convert them to binary format.
```
0010
0101
0101
0101
```
So, we check every bit(0 to 32) of every num in the list. If the count of that bit mod 3 is 1, which means this bit also in the target number. If the mod result is 0, which means this bit not in the target number. **But, using th python, for the negative number we need to check the sign bit by ourselves.**
```python
class Solution(object):
    def singleNumber(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        res = 0
        for i in range(32):
            count = 0
            for num in nums:
                if num & (1 << i):
                    count += 1
            rem = count % 3
            if rem and i == 31:
                res -= 1<<i
            else:
                res |= rem * (1<<i)
        return res
```


###[Leetcode 190.Reverse Bits](https://leetcode.com/problems/reverse-bits/)

Given an integer, reverse its bits.
Brute force:

```python
def reverseBits(self, n):
    ret = 0
    for i in range(0,32):
        ret <<= 1
        ret |= n & 1
        n >>= 1
    return ret
```

Another approach:

For example: ABCDEFGH

```
ABCDEFGH >> 4 = 0000ABCD
ABCDEFGH << 4 = EFGH0000
0000ABCD | EFGH0000 = EFGHABCD

EFGHABCD & 00110011 = 00GH00CD
EFGHABCD & 11001100 = EF00AB00
00GH00CD << 2 = GH00CD00
EF00AB00 >> 2 = 00EF00AB
GH00CD00 & 00EF00AB = GHEFCDAB

GHEFCDAB & 01010101 = 0H0F0D0B
GHEFCDAB & 10101010 = G0E0C0A0
0H0F0D0B << 1 = H0F0D0B0
G0E0C0A0 >> 1 = 0G0E0C0A
H0F0D0B0 | 0G0E0C0A = HGFEDCBA
Reversed!
```

Code:

```python
def reverseBits(self, n):
n = (n >> 16) | (n << 16)
n = ((n & 0xFF00FF00) >> 8) | ((n & 0x00FF00FF) << 8)
n = ((n & 0xF0F0F0F0) >> 4) | ((n & 0x0F0F0F0F) << 4)
n = ((n & 0xCCCCCCCC) >> 2) | ((n & 0x33333333) << 2)
n = ((n & 0xAAAAAAAA) >> 1) | ((n & 0x55555555) << 1)
return n
```





###[Leetcode 201. Bitwise AND of Numbers Range](https://leetcode.com/problems/bitwise-and-of-numbers-range/)

Given a range \[m, n\] where 0 &lt;= m &lt;= n &lt;= 2147483647, return the bitwise AND of all numbers in this range, inclusive.

For example, given the range \[5, 7\], you should return 4.

**Solution**:  
A brute force way to solve this problem would be doing **&** from m to n. The time complexity would be O\(n\).

However, we can observe that:

If n &gt; m, \[m,n\] must contains even numbers and odd numbers. So, if we doing bitwise & from m to n, the last bit of the result must be 0.

Inspired by this observation, instead of doing & from m to n, we can simply focus on m and n.

For example:

```
m = 5, n = 7. 
n > m, so the last bit of the result is 0
Now, we shift n and m to the left.
m = 101 >> 1 = 10
n = 111 >> 1 = 11 
n is still bigger m, so the second last bit of the result is also 0.
Then, we shift again.
m = 10 >> 1 = 1
n = 11 >> 1 = 1
Now, n == m = 1
So, the result should be 100
```

The code is like this:

```python
def rangeBitwiseAnd(self, m, n):
    count = 0
    while m != n:
        m >>= 1
        n >>= 1
        count += 1
    return m << count
```

Since n can be represented by log\(n\) bits. At worst case, there will be log\(n\) shifts until m == n. So, the time complexity is O\(logn\).




###[leetcode 260. Single Number III](https://leetcode.com/problems/single-number-iii/) 
This problem has two targets. So we need to divide the list into to part. The first part contains one target, the other part contains the other target. Assume the target is [3, 5]:
```
0011
0101
```
When we use the xor operation on all nums in the list and get the result k. We can get the right most one from k. We use this bit as the mask to divide the list into two parts. Each part has one target. 
```python
class Solution(object):
    def singleNumber(self, nums):
        """
        :type nums: List[int]
        :rtype: List[int]
        """
        a = b = ret = 0
        for num in nums:
            ret ^= num
        mask = ret & ~(ret-1)
        for num in nums:
            if num & mask:
                a ^= num
            else:
                b ^= num
        return [a,b]
        
```
###[leetcode 318. Maximum Product of Word Lengths](https://leetcode.com/problems/maximum-product-of-word-lengths/)
We can use 26 bits to represent each word in the word list. Use **'and'** operation to check wheather two different words has intersection.
```python
class Solution(object):
    def maxProduct(self, words):
        """
        :type words: List[str]
        :rtype: int
        """
        def convert2num(string):
            num = 0
            for char in string:
                num |= 1<<(ord(char)-ord('a'))
            return num
        temp = []
        for word in words:
            temp.append(convert2num(word))
        res = 0
        for i in range(len(temp)):
            for j in range(i+1, len(temp)):
                if temp[i] & temp[j] == 0:
                    res = max(res, len(words[i]) * len(words[j]))
        return res
```



###[Leetcode 320. Generalized Abbreviation](https://leetcode.com/problems/generalized-abbreviation/)
This problem we also need to use n bits to represent the different combinations of the word. **But, we also need to count consecutive '0' in each combination.**
```python
class Solution(object):
    def generateAbbreviations(self, word):
        """
        :type word: str
        :rtype: List[str]
        """
        def convert2abbr(num, count, word):
            abbr = ""
            zero, i = 0, 0
            flag = False
            while i < count:
                if not num & (1 << i):
                    if flag:
                        zero += 1
                    else:
                        flag = True
                        zero = 1
                else:
                    if flag:
                        flag = False
                        abbr = abbr + str(zero)
                    abbr = abbr + word[i]
                i += 1
            if flag:
                abbr += str(zero)
            return abbr

        res = []
        length = len(word)
        count = 1 << length
        for i in range(count):
            res.append(convert2abbr(i, length, word))
        return res
```




## Reference{#ref} 

<cite>[Computer Systems: A Programmer's Perspective][1]</cite>

[1]:http://csapp.cs.cmu.edu/

<cite>[15-513: Intro to Computer Systems][2]</cite>

[2]:http://www.cs.cmu.edu/~213/schedule.html

<cite>[The Magic of XOR][3]</cite>

[3]:https://www.cs.umd.edu/class/sum2003/cmsc311/Notes/BitOp/xor.html




