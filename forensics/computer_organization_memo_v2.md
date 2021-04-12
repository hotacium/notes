# Computer Organization

modern computer = ***software*** + ***hardware***

* software: operating system, application software
* hardware: Central Processing Unit (CPU), main memory, input/output devices, secondary memory, a bus to communicate between defferent parts

(modern computer is composed of software and hardware

## CPU, Central Processing Unit

CPU ***receices*** and ***decodes*** instructions from memory.

special units in CPU
* ***Arithmetic Logic Unit*** (ALU) : performs operations with ***numbers*** and handful of ***registers***.
* Registers: allows ***quick access*** to data and instructions stored in memory.


(CPU <-- Data Bus --> RAM):
(CPU <-- Address Bus --> RAM): 

## Data Strage

* RAM: holds running programs and data that thte programs use. Fast but expensive.
* Secondary storage: stores programs and data when a computer is off. (e.g. hard disk, solid state drive)
* Removable storage: archives data which is not accessed frequently.

(CPU <-- input/output channels --> secondary storage / removable storage)


## Data Representation
Data is represented by ***encoding***
(Encoding: converting message or data into code, e.g. string to 0/1)


## Memory Alignment and Byte Ordering
how data is arranged and accessed in computer memory.

32bit -> 4 byte = one chunk
1. 0x1000, ..., 0x1003
2. 0x1004, ..., 0x1007

64bit -> 8 byte = one chunk
1. 0x1000, ..., 0x1007
2. 0x1008, ..., 0x100f
3. 0x1010, ..., 0x1017

#### access misaligned memory data on ***32***bit.
```
// 1 represents data btye
// 0 represents non-data btye

memory
| 0 1 1 1 | 1 0 0 0 |

break memory into chunk 1 and 2, and shift in order to combine them into 4-byte
-> {
	chunk 1: 1 | 1 1 1 0 | ( Shift 1 byte up )
	chunk 2: | 0 0 0 1 | ( Shift 3 btye down)
}

Combine two 4-byte chunks into one 4-byte chunk.
-> | 1 1 1 1 |
```
-> slower access to the memory

### Example C and memory size
```c
struct Student {
	int id; // int -> 4 byte
	char province[3]; // char -> 1 byte
	int age; // int -> 4 byte
};

// 4 + 1*3 + 4 = 11 byte -> 12 byte (padded 1 byte by a compiler)
```
#### Little-Endian and Big-Endian (Byte ordering)
byte of multiple-byte data element can be arranged adepending on tis order in which byte addressable memory is stored.

* Little-Endian
* Big-Endian

e.g. Store "90AB12CD" 
```
// "90, AB, 12, CD"

Little-Endian
0x1000: CD
0x1001: 12
0x1002: AB
0x1003: 90

Big-Endian
0x1000: 90
0x1001: AB
0x1002: 12
0x1003: CD
```
Problem to transfer data between different byte ordering systems.
-> XDR (External Data Representation): a standard data ***serialization format***, which allows data to be transferred between ***different*** kinds of cumputer systems.


