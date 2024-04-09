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

To toggle or flip a bit, we can use XOR operator. The main property of XOR is that it keep the bit same if both operands are same, else it flips the bit.

| A | B | Result |
| - | - | ------ |
| 0 | 0 |   0    |
| 0 | 1 |   1    |
| 1 | 0 |   1    |
| 1 | 1 |   0    |

To toggle a specific bit, we can can use XOR in combination with left shift operator to move it to the desired location.

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

#### Approach 1: - Count the number of set bits in the number.
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
#### Approach 2: - Count the number of set bits in the number.
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

