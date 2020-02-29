# Bits, Bytes and Integers
* There is also binary point and numbers can be represented as fracional in binary as well

## 1 Byte
```
Binary
    From 00000000_2
    To   11111111_2
Decimal
    From   0_10
    To   255_10
Hex
    From   0_16
    To    FF_16
```

|Hex|Decimal|Binary|
|-----|------|-----|
|0|0|0000|
|1|1|0001|
|2|2|0010|
|3|3|0011|
|4|4|0100|
|5|5|0101|
|6|6|0110|
|7|7|0111|
|8|8|1000|
|9|9|1001|
|A|10|1010|
|B|11|1011|
|C|12|1100|
|D|13|1101|
|E|14|1110|
|F|15|1111|

## C Data Representation

```
1 Byte  =  8 bit
2 Bytes = 16 bit
4 Bytes = 32 bit
8 Bytes = 64 bit
```
|C Data Type|Typical 32-bit| Typical 64-bit|x86-64|
|-----|-----|-----|
|char|1|1|1|
|short|2|2|2|
|int|4|4||4|
|long|4|8|8|
|float|4|4|4|
|double|8|8|8|
|pointer|4|8|8|

**64-bit machines are called 64-bit because of the point size on the archtecture** 

## Shift Operators
* x << y left  shift x bit-vector by y positions
```
x = 0110 0001
x << 3
x = 0000 1000
```

* Two types of right shifts: Logical, Arithmatic
* Logical: x >> y right shift x bit-vector by y positions, fill with zeros
* Arithmatic: x >> y right shift x bit-vector by y positions, fill with most significant bit
```
x = 1110 0001
x >> 3 (logical)
x = 0001 1100
x >> 3 (arithmatic)
x = 1111 1100
```

* Important note: When shifting a word x by y where y < 0 or y >= word-size-x then behavior is undefined. Consider example below:

```
x is a word of 1 byte (8 bit)
x << 8 will return x on many machines and some might return 0.
This is because x << 8 will be processed as x << (y mod 8)
```

# Encoding Intgers
How integers are stored in bits. There are two ways
1. Unsigned 
2. Twos Complement

```
Let x be the word of size w and x_i be the ith bit
x_w ... x_4 x_3 x_2 x_1 x_0
Un-Signed
---------
Integer = SUM_(i=0->w-1)[x_i * 2^i]
        = x_w * 2^w + ... + x_1 * 2^1 + x_0 * 2^0  

x = 10101
w = 5

Place = 16 8 4 2 1
x     =  1 0 1 0 1
Value = 16+0+4+0+1 = 21


value = 16 8 4 2 1
Two-Complement
--------------
Integer = SUM_(i=0->w-2)[x_i * 2^i] - x_(w-1) * 2^(w-1)
        = -x_(w-1) * 2^(w-1) + ... + x_1 * 2^1 + x_0 * 2^0  


x = 10101
w = 5

Place = -16 8 4 2 1
x     =   1 0 1 0 1
Value = -16+0+4+0+1 = -11
```

* The most significant bit is called the **sign bit** in two-complement representation
* Note a trick ```A word of size w with all bits set to 1 is has value one less than a word of size w+1 with the most significant bit set to 1 ```

```
# Max Ranges
Unsigned : ```0 <= x <= 2^w - 1```
UMax = 2^w - 1
UMin = 0
Signed : ```-2^(w-1) <= x <= 2^w - 1```
TMax =   2^(w-1) -1
TMin =  -2^(w-1)

Note that
|TMax| = |TMin| - 1
|UMax| = 2*|TMax| +1
```

|Word Size|1 Byte 8|2 Byte 16|4 Bytes 32|8 Byte 64|
|UMax|255|65535|4 Billion|-|
|TMax|127|32767|2 Billion|-|
|TMin|-128|-32768|-2 Billion|-|

* When comparing signed and unsigned things are casted to unsigned implecely
* Note that casting means that the underlying bits will pe interpretated as 
  two's complement bits or as unsigned number
* We should never cast int to unsigned
```
std::cout << (unsigned) -1 << std::endl;
4294967295
```

## Sign Extension
* Making a number to be stored in a larger word size but representing the same value

```
Let x be unsigned 4-bit number
x = 1110 = -2

Q. Convert x to 8-bit number:
A. Rule is to copy the leading bit in all the new spaces
answer = 1111 1110 (-2)
```

## Truncate
When number is unsigned 
```
Let x be unsigned with wordsize w
and let new smaller word size be w'

then w' will have value value(w) mod 2^(w')
```

# Unsigned Addition
* USum of two words of size w
* Requires w+1 bits but we trucate
* USum is (u+v) mod (2^w)

# Two complement Addition
* Just like regular sum
* 4-bit -8,...,7
```
    1101 -3
 +  0101  5
  ------
    0011  2

Note that the sum overflowed but we ignored it and yet 
we still got the correct answer!
2-complement it is very important to stick to the fixed size
```

## Negative Overflow/Positive Overflow
* Overflow when the true sum of the twos complement cannot
be represented using word size w.
* Negative Overflow: when two negative numbers sum up to a number which is too negative to represent in size w so it overflows to a positive number
* Positive Overflow : when two positive number sum up to a number which is too positive to represent in size w so it overflows to a negative number

# Multiplication 
* Do the true multiplication and truncate
* Remember that in for unsigned it will be u*v mod 2^w
* For twos complement its true product just truncating w

# Multiply by 2
* Shift left by 1 (Unsigned)
* Shift left by 

# Divide by 2
* Shift right by 1 (Unsigned) -- will be rounded down
* Shift right by 1 (Signed) if it's positive it will work fine
  Since MSB will be 0
* Shift right by 1 (Signed) (arthmatic shift)
  but this will divide by 2 and round downwards. we want to round towards 0. Trick is to add 1 and then do the shift

## Negate a number (mutliple by -1)
* complement and increment
* take complement of the number and add one

## Slowness
* shift 1 cycle
* multiplication 3 cycles
* division 10 cycles
