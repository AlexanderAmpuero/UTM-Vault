# IEEE
## Integers Base 2
123 written base 2 is found by repeatedly mod 2 then divide by 2. 
123 % 2 = 1
123 / 2 = 61
61 % 2 = 1
61 / 2 = 30
30 % 2 = 0
30 / 2 = 15
15 % 2 = 1
15 / 2 = 7
7 % 2 = 1
7 / 2 = 3
3 % 2 = 1
3 / 2 = 1
1 % 2 = 1
1 / 2 = 0

Thus 123 is 1111011 base 2. 
To see why this works, note that if 123 was already written in base 2, we would be manipulating the bits of 123 by pulling off the least significant bit (%2) and then shifting right (div 2)

111101  %  2 = 1
111101 div 2 = 11110
11110   %  2 = 0
11110  div 2 = 1111
...

## Converting Base 10 to Base 2
### Integer
Convert 263 into binary
263 / 2 = 131 (remainder 1)
131 / 2 = 65 (remainder 1)
65 / 2 = 32 (remainder 1)
32 / 2 = 16 (remainder 0)
16 / 2 = 8 R0
8 / 2 = 4 R0
4 / 2 = 2 R0
2 / 2 = 1 R0
1 / 2 = 1 R1
Thus 263 in base 2 is 100000111

### Representable Decimal
We split its components into 12 and 0.25.
12 / 2 = 6 (0 remainder)
6 / 2 = 3 (0 remainder)
3 / 2 = 1 (1 remainder)
1 / 2 = 1 (1 remainder)
Thus 12 = 1100

0.25 x 2 = 0.5 (0 bit)
0.5 x 2 = 1 (1 bit)
Thus 0.25 = 0.01

Adding these together, we get 1100.01

### Non-Representable Decimal
Convert 75.3 into Binary
75 / 2 = 37 R1
37 / 2 = 18 R1
18 / 2 = 9 R0
9 / 2 = 4 R1
4 / 2 = 2 R0
2 / 2 = 1 R0
1 / 2 = 1 R1
=1001011

0.3 x 2 = 0.6 (0 bit)
0.6 x 2 = 1.2 (1 bit)
0.2 x 2 = 0.4 (0 bit)
0.4 x 2 = 0.8 (0 bit)
0.8 x 2 = 1.6 (1 bit)
0.6 x 2 = 1.2 (1 bit)
0.2 x 2 = 0.4 (0 bit)
So 0.3 = 0.01001 repeating
So 75.3 = 1001011.01001 repeating

Since we have an infinitely repeating portion, this number is not representable in base 2. This is not foreign though, we have many similar situations in base 10, like 1/3 = 0.333 repeating. 

## Scientific Notation
Whether we are in base 10 or base 2, we have the ability to write umbers in the form:
+/- d1.d2 d3 ... .dn x 2^(e)

Where "+/-" is the sign
d1 d2 d3 ... dn is called "the mantissa"
and e is "the exponent

### Ex 1 - Denormalized Numbers
Write 75.3 in scientific notation for both base 10 and base 2
Base 10: 7.53 x 10^2
Base 2: 1.00101101001 x 2^6 with 1001 repeating

### Ex 2 - Normalized Numbers
Notice how a binary number's leading digit is always 1 in scientific notation.
12.25 = 1100.01 in binary = 1.10001 x 2^3
263 = 100000111 = 1.00000111 x 2^8

Therefore a binary number in scientific notation is "normalized"

### Theory v. Practice
In theory, we can represent any rational number perfectly on paper by simply indicating that there is a repeating segment. However, what if we only had a limited number of digits to use? I'm lazy, and don't want to type out 32 digits, so for this text file let's pretend we have a memory limitation of 9 digits in the mantissa. 

With 9 digits, then 263 = 1.00000111 x 10^8 can be perfectly represented. 
But 75.3 = 1.00101101001 x 2^6 would be 1.00101101 x 2^6 (missing the last 3 digits of the repeated pattern 001)

The root of floating point error stems from having to truncate numbers that need have more digits than available bits of memory. Any non-representable number will inherently have truncation at any level of precision. However, even some representable numbers may have truncation if they are large enough (or have too many decimal numbers)

Floating point errors occur when we missing things like those last 3 digits, since we have finite bits, we have finite storage for each value. So we can come close and approximate it. 

There is an international standard called IEEE 754 that defines an algorithms/pattern that tells us how to store the required information from a normalized binary number as a sequence of 32 bits.
## IEEE 754 Floating Point Single Precision Standard
The IEEE 754 Floating Point Single Precision Standard defines how 32 bits (4 bytes) are used to represent floating point numbers. 

s[eeeeeeee]mmmmmmmmmmmmmmmmmmmmmmm
1       8                                              23 bits
Which represents the number...
s 1.mmmmmmmmmmmmmmmmmmmmmmm x 2^(eeeeeeee-127)
Where *s* is the sign of the bit. 0 means *positive*, 1 means *negative*
[eeeeeeee] is the exponent, with 8 bits available. The real exponent is eeeeeeee-127

For Normalized numbers, 
eeeeeeee!=00000000
eeeeeeee!=11111111

00000001-127 to 111111110-127 = -126 to 127
Thus the range of exponents is -126 to 127

### Ex 1 — 12.25 in Computer Memory
i) convert base 10 number to binary. 12.25     =  1100.01
ii) Normalize binary number.               1100.01 = 1.10001 x 2^3
iii) Calculating Binary (127 + exp).      127 + 3 = 10000010
iv) Map out all the components of the IEEE format:
	sign bit: 0
	exp bits: 10000010
	Mantissa: 10001000000000000000000 (right padded with leading 0's to total 23 bits)

Therefore, 12.25 in IEEE is 01000001010001000000000000000000

Note: float     = 32 bits (pattern above)
	  double = 64 bits
			sign: 1 bit
			exp: 11 bits (with an offset of 1023, instead of 127)
			mantissa: 52 bits (instead of 32)

### EX 2 — 0.1 in Computer Memory
i) Convert base 10 number to binary. 
0.1 = 
0.1 x 2 = 0.2 (0 bit)
0.2 x 2 = 0.4 (0 bit)
0.4 x 2 = 0.8 (0 bit)
0.8 x 2 = 1.6 (1 bit)
0.6 x 2 = 1.2 (1 bit)
0.2 x 2 = 0.4 (0 bit)
Thus we have 000110011 repeating. 
ii) Normalize binary number 000110011 = 1.100110011... x 2^-4
iii) Binary (127 + exp) 127 + -4 = 123 = 
123 / 2 = 61 R1
61 / 2 = 30 R1
30 / 2 = 15 R0
15 / 2 = 7 R1
7 / 2 = 3 R1
3 / 2 = 1 R1
1 / 2 = 0 R1
0 / 2 = 0 R0
Thus 123 = 01111011
iv) Map out the IEEE 754 components
	sign bit: 0
	exp bit: 01111011
	Mantissa: 10011001100110011001101 (last bit rounds up as repetition continues)

Therefore 0.1 in binary is 00111101110011001100110011001101
	

