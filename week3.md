# Mafia Week 3

2017-12-18

This week Pei  presented **bit mamnipulation**.

# Content

* ['&' operator](#andopt)
* ['|' operator](#oropt)
* ['^' operator](#xoropt)
* [Representing & Manipulating Sets](#represent)

**[Reference](#ref)**

Bit Manipulation

### '&' operator {#andopt}

**Test n-th bit:**

```python
if num & 1 << n: #if n-th bit is 1
    doSomething()
else: #if n-th bit is 0
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

Extract right most '1':

```python
n & ~(n - 1) # or n & -x
#Explaination:
#n        = xxxxx100
#~(n - 1) = x̄x̄x̄x̄x̄100
#--------------------
#    &    = 00000100
```

**Some Example**:

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

## & Problem

### Bitwise AND of Numbers Range

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

## '\|' operator {#oropt}

**reverse bit**

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

## '^' operator{#xoropt}

Given an array of integers, every element appears twice except for one. Find that single one.

```python
class Solution(object):
def singleNumber(self, nums):
    ret = 0
    for i in nums:
        ret ^= i
    return ret
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

## Reference{#ref} 

<cite>[Computer Systems: A Programmer's Perspective][1]</cite>

[1]:http://csapp.cs.cmu.edu/

<cite>[15-513: Intro to Computer Systems][1]</cite>

[1]:http://www.cs.cmu.edu/~213/schedule.html






