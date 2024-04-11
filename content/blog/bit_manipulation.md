+++
title = 'Bit Manipulation'
date = 2024-04-08T21:47:41-07:00
draft = false
tags = ["Bit-manipulation", "C","C++", "Embedded"]
math = true
+++

### Bitwise Operators

The following are the bitwise operators which are available in C/C++ programming language.

| Operator | Meaning |
| -------- | ------- |
| & | Bitwise AND |
| \| | Bitwise OR |
| ^ | Bitwise XOR |
| ~ | Bitwise complement |
| << | Shift left |
| >> | Shift right |


### XOR

The main property of XOR is that it keep the bit same if both operands are same, else it flips the bit.

| A | B | Result |
| - | - | ------ |
| 0 | 0 |   0    |
| 0 | 1 |   1    |
| 1 | 0 |   1    |
| 1 | 1 |   0    |

### Few Properties of XOR

| Property      | Result |
| ------------- | ------ |
| A ^ 0         | A      |
| A ^ 1         | ~A     |
| A ^ A         |  0     |
| A ^ A ^ A     | A      |
| A ^ B ^ A     | B      |
| A ^ B ^ B     | A      |

# Few properties of Shift operators.

1. Left shifting the number by 1 - Multiplies the number by 2.
2. Right shifting the number by 1 - Divides the number by 2.

### Setting a particular bit of register or variable.

To set a specific bit of a register or variable, we can use OR operator in combination of shift left operator to move it to a the desired bit location to be set.

```C
    unsigned int a = 10;    // 1010 (Binary)
    a = a | (1<<3);         // Sets the 3th bit of the a;
```

### Clearing a particular bit of register or variable.

To clear a specific bit of a register or variable, we can use AND operator in combination of shift left and compliment operator to move it to the desired bit location to be cleared. The compliment operators inverts the bit to cleared and keep 1s at every other location to keep the original bit for rest while doing AND operation.

```C
    unsigned int a = 10;    // 1010 (Binary)
    a = a & (~(1<<4));      // Clears the 4th bit of the a;
```

### Toggle or flip bit of a register or variable.

To toggle or flip a bit, we can use XOR operator. To toggle a specific bit, we can can use XOR in combination with left shift operator to move it to the desired location.

```C
    unsigned int a = 10;    // 1010 (Binary)
    a = a ^ (1<<4)          // Toggles the 4th bit.
```

### Checking if a given bit is set.

To check if the bit is set, we can use AND operator. Create a mask using shift operator where 1 is at the desired location where bit-set needs to be checked.

```C
    unsigned int a = 10;          // 1010 (Binary)
    if( (a & (1<<3)) == 1 )       // Check if bit is set.
        printf("Bit is set\n");    
```

### Defining the functions in MACRO

The above function can also be written in form of C/C++ MACROS

```C
    #define SET_BIT(number, bit_position)        number |= (1<<bit_position)
    #define CLEAR_BIT(number, bit_position)      number &= ~(1<<bit_position)
    #define TOGGLE_BIT(number, bit_position)     number ^= (1<<bit_position)
    #define CHECK_BIT_SET(number, bit_position)  number &= (1<<bit_position) 
```

### Toggle or flip bits in the given range.

To toggle the bits of a non-negative number in a given range l and r, create a mask such that the bits in the range [l,r] must be 1 and else 0. The mask is then XORed with the input to toggle the bits.

The assumption is that r >= l and the number N is always a non-negative.

#### Approach 1:

```C
    int toggleBits(unsigned N, unsigned int l, unsigned r)
    {
        if( r<l )
            return;
            
        unsigned int mask = 0;
        for(unsigned int i=1; i<=(sizeof(mask)*8); i++)
        {
            if( i>=l && i<=r )
                mask |= (1<<(i-1));
        }
        return N^mask;
    }
```

### Cheking if the number is odd or even using bit-manupulation.

An odd number always has the binary 1 at the least significant position. We can simply check if the least significant bit is set.

Example:
* 53 (0011 1101)2
* 99 (0110 0011)2

```C
    unsigned int number = 99;
    bool is_odd = number & 1;
```

### Checking if the number is power of 2.

The number which are power of 2 all have only single bit set in its binary representation. We can use this to deduce if the number is power of 2.

Example:
* 02 = (0010)2
* 04 = (0100)2
* 32 = (0010 0000)2

#### Approach 1: Count the number of set bits in the number.
```C
    unsigned int number = 32;
    unsigned int num_set_bits = 0;
    while(number != 0)
    {
        if(number & 1)
            num_set_bits += 1;
        number >>= 1;
    }
    if( num_set_bits == 1 )
        printf("Number is power of 2");
```
#### Approach 2: The power of 2 number always have 1 at the most significant position. 
```C
    unsigned int number = 32;
    if( number & (number-1) )
        printf("Number is not power of 2");
    else
        printf("Number is power of 2");
```

### Swaping the bits at given positions of an interger.

Given a non-negative integer N, and position i and j. Swap the bits of integer N at position i and j.

#### Aproach 1: 

Example: N =  (000**10**101)2, i = 3, j = 4

First extract the bits at position $i$ and $j$, from $N$. 
`A = (N>>i) & 1 = 0` 
`B = (N>>j) & 1 = 1` 

If `A != B`, only then the bits would be swapped, else we return the same N. From XOR properties, we know that `A ^ 1 = ~A`. We need to create a mask where we have bits set at position i and j. 

``mask = (1<<i) | (1<<j)``.

Finally, we can do XOR to toggle the bits at the position of $i$ and $j$.
``N = N ^ mask``

```C
    unsigned int N = 99;
    unsigned int i = 2;
    unsigned int j = 4;

    if(i == j)
        return;

    unsigned int A = N>>i & 1;
    unsigned int B = N>>j & 1;
    if (A == B)
        return N;
    unsigned int mask = (1<<i) | (1<<j);
    return N = N ^ mask;
```

### Swap all even or odd bits.

To swap all even and odd bits, we would need two mask to extract the odd and even bits from the number.

Assumption: The size of int is 32-bit.

``odd_mask  = number & 0x55555555
  even_mask = number & 0xAAAAAAAA
``
Left Shit the odd_mask by one and the right shift the even_mask by one. The resulting mask is then ORed together to get the swaped values.

```C
    unsigned int number = 32;
    unsigned int even_mask = number & 0xAAAAAAAA;
    unsigned int odd_mask  = number & 0x55555555;
    number = ( even_mask >> 1 | odd_mask << 1);
```

### Finding the sign of an interger using bitwise.

The negative integer are stored in the form of 2s compliment in the memory. The 2's compliment is calculated by adding $1$ to 1's compliment (obtained by inverting the bits).
MSB is reserved for sign. Negative number are represented by 1 and positive by zero.

*Why Negative numbers are stored in 2's compliment: It allows the same circuit to perform addition and subtraction.*

Example:

$13_{10} = (1101)_{2}$

1's compliment = $(0010)_{2}$

2's compliment = $(0011)_{2}$

In memory = $(10011)_{2}$

To check if the number is positive or negative, we can simply check the sign at the most significant bit.

```C
    int a = -5;
    if( (a != 0) && (a >> 31) )    // Assuming that int is 32-bit in size.
        printf("a is negative\n");
    else
        printf("a is positive\n");
```

### Swap endianess of a 32-bit integer.

Example:
* Little Endian: 0000 0000 0000 0000 0000 0000 0001 0000
* Big Endian:    0000 0001 0000 0000 0000 0000 0000 0000

To swap the endianess, create a mask for each bytes to extract their value. The mask values are then ORed together at the correct positions to get the final value.

```C
    unsigned int a = 16;
    unsigned int swapped_a = ((0xFF & a) << 24) | ((0xFF00 & a) << 8) | ((0xFF0000 & a) >> 8) | ((0xFF000000 & a) >> 24);
```

### Clear all the LSB bits from a given position.

