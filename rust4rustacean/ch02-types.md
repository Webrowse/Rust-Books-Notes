# Types

## Types in Memory

The fundamental role of types is to tell how to interpret bits of memory.  
for eg, 0b10111101 is 189 in u8 and -67 in i8.

### Alignment
The pointers points to the bytes and not bits, hence the information will be saved in a multiple of 8 (because 8bits = 1byte).   
u8 is byte aligned, u16 is 2 byte aligned, u32 is 4 byte aligned, u64 is 8 byte align.   
Complex types will be largest sized byte alignment.  

### Layout
to bound in-memory representation, the keyword is #[repr(xyz)].   
- repr(C)  : to mimic C,C++ compiler memory design
- repr(transparent) : used for types with single field, ensures that inner struct and outer struct will have same layout

in repr(C) if our struct need 12 bytes, we will pad it with 4 more bytes to make it aligned (16 bytes).
in repr(rust) compiler arrange the needs of bytes itself, that's why it doesn't gurentee the design.

for lowest bandwidth, you can use the most efficient layout: #[repr(packed)],   
tradeoffs: slower, and can crash if CPU only support aligned arguments.

for giving larger alignment than it technically needs: #[repr(align(n))]


