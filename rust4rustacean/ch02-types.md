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

### Complex Types
Tuple : Represented like a struct with fields of same type in same order.  
Array : Represented as a continuous sequence of the contained type with no padding between elements.   
Union : Each variant is independently chosen. Alignment is maximum across all the variants.  
Enumeration : Same as Union, with one hidden field, that depends on the number of field which discriminator will be used.

## Dynamically Sized Types and Wide Pointers

Dynamically sized types (DSTs) e.g. dyn Iterator, [u8] are unknown at compile time.ut compiler seek Sized at compile time.  
So, *wide pointer* solves the problem, it is sized (2 x u8, one place for pointer, another to save extra info).   
extra info in case of slice is the length of the slice.  
<T: ?Sized> meaning is may be not sized. Box and Arc supports wide pointer, both supports T: ?Sized.  

## Traits and Trait Bounds

These act as a glue, allowing types to work together even if they weren't defined together.   

### Compilation and Dispatch
when you write Vec<T> and contruct using i32, compiler copy paste all the T with i32.  
In realilty, it only copies the part that you are using, That is why you never call find on Vec<i32>.

**Static Dispatch (Generics)**: When using generic type parameters (e.g., fn compute<T: Trait>), the compiler creates separate copies of the function for each concrete type used. This process is called monomorphization. This results in highly optimized code but can increase compilation time and binary size.

**Dynamic Dispatch**: When using a trait object (like &dyn Trait), the actual type is unknown at compile time. The wide pointer passed during dynamic dispatch contains a pointer to a vtable (virtual method table). The vtable holds the addresses of the specific methods for the concrete type, which are looked up at runtime. This reduces compile time but prevents specific type optimizations. 

**Trait Object Safety**: A trait that cannot be used as a dyn Trait is not object-safe, usually because its methods return Self or are themselves generic   

