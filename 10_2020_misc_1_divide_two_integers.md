# 29. Divide Two Integers

Given two integers `dividend` and `divisor`, divide two integers without using multiplication, division, and mod operator.

Return the quotient after dividing `dividend` by `divisor`.

The integer division should truncate toward zero, which means losing its fractional part. For example, truncate(8.345) = 8 and truncate(-2.7335) = -2.


Note:

- Assume we are dealing with an environment that could only store integers within the 32-bit signed integer range: [−2^31,  2^31 − 1]. For this problem, assume that your function returns 231 − 1 when the division result overflows.


## Reference Solution

```c++
class Solution {
public:
    int HALF_INT_MIN = -1073741824;

    int divide(int dividend, int divisor) {

        // Special case: overflow.
        if (dividend == INT_MIN && divisor == -1) {
            return INT_MAX;
        }

        int negatives = 2;
        if (dividend > 0) {
            negatives--;
            dividend = -dividend;
        }
        if (divisor > 0) {
            negatives--;
            divisor = -divisor;
        }

        int quotient = 0;

        // very classic use of two level while loops.
        while (divisor >= dividend) {

            int powerOfTwo = -1;
            int value = divisor;

            while (value >= HALF_INT_MIN && value + value >= dividend) {
                value += value;
                powerOfTwo += powerOfTwo;
            }

            quotient += powerOfTwo;
            dividend -= value;
        }

        if (negatives != 1) {
            quotient = -quotient;
        }
        return quotient;
    }
};
```

My attempt. The `nested while` structure is very illuminating.

```c++
class Solution {
public:
    int divide(int dividend, int divisor) {
        if (dividend == INT_MIN && divisor == -1) return INT_MAX;
        
        bool non_negative = true;
        if (dividend > 0 && divisor < 0 || dividend < 0 && divisor > 0 ) {
            non_negative = false;
        }
        dividend = -abs(dividend);
        divisor = -abs(divisor);
        
        // dividend = -10
        // divisor = -2
        int quo = 0;
        while (divisor >= dividend) {
            // reinitialize the start point
            int val = divisor;
            int powOfTwo = 1;
            
            // exponentially increase to test the limit
            while (val > INT_MIN/2 && val + val >= dividend) {
                val += val;
                powOfTwo += powOfTwo;
            }
            
            // reap the results of one iteration of expo increase
            dividend -= val;
            quo -= powOfTwo;
        }
        
        return non_negative ? -quo : quo;
    }
};
```