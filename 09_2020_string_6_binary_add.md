# 67. Binary Addition

Normal version:

```c++
class Solution {
public:
    string addBinary(string a, string b) {
        // emulate how the hardware adder does things.
        // in1, in2, out, carry_in, carry_out
        // carry_out = ((in1 ^ in2) & carry_in ) | (in1 & in2)
        // in1 in2 carry_in   out carry_out
        // 0   0   0          0   0
        // 0.  0.  1.         1   0
        // 0.  1   0.         1   0
        // 0.  1.  1.         0   1
        // 1   0   0          1   0
        // 1.  0.  1.         0   1
        // 1.  1   0.         0   1
        // 1.  1.  1.         1   1    
        // out = in1 ^ in2 ^ carry_in
        
        int len_a = a.length();
        int len_b = b.length();
        for (int i = 0; i < len_a-len_b; i++) {
            b = "0" + b;
        }
        for (int i = 0; i < len_b-len_a; i++) {
            a = "0" + a;
        }    
        int len = max(len_a, len_b);
        
        string res = "";
        unsigned char carry = 0;
        for (int i = 0; i < len; i++) {
            unsigned char in1 = a[len-i-1]-'0';
            unsigned char in2 = b[len-i-1]-'0';
            
            unsigned char out = in1 ^ in2 ^ carry;
            carry = ((in1 ^ in2) & carry ) | (in1 & in2);
            
            res += to_string(out);
        }
        if (carry) res += "1";
        
        reverse(res.begin(), res.end());
        
        return res;
    }
};
```


Bit manipulatino version:

```c++
class Solution
{
    public:
    string addBinary(string a, string b)
    {
        bitset<128> A{a};
        bitset<128> B{b};
        
        bitset<128> C = A ^ B;            // Add numbers without carry.
        bitset<128> carry = (A & B) << 1; // Shift because carry adds to bits one level up.
        
        while (carry.any())
        {
            bitset<128> tmp = C ^ carry;
            carry = (C & carry) << 1;
            C = tmp;
        }
        string s{C.to_string()};
		// Strip leading zeros.
        s = s.erase(0, s.find_first_not_of('0'));
        if (!s.size())
        {
            return "0";
        }
        return s;
    }
};
```