# Alignments and padding for data structs in C and Rust

## Trailing padding

Stuct size must be a multiple of the maximum align of its fields

`(MAX_ALIGN - (STRUCT_SIZE_NO_PADDING % MAX_ALIGN)) %MAX_ALGIN` last mod needed for the edge case that its already aligned

Given a max align of 4, and size 133.

(4 - (133 % 4)) % 4 = 3 bytes of padding

## Internal padding

Each field must start at an offset of a multiple of its alignment

internal padding occurs when a smaller field precedes a larger field

all offsets are inclusive

## Small internal Padding

u32 is a size 4, so it needs to start at a multiple of 4
```rust
#[repr(C)]
struct
    a: u8,
    _pad: [u8; 3],
    b: u32,
```

a, size 1. offset 0

padding: 3 ofset 1-3

b, size 4. offset 4 - 7

No trailing cuz size is 8 (0-7)


## bigger offset

```rust
#[repr(C)]
struct
    a: u8
    b: u64
    c: u32
```

u64 needs align 8

a, size 1. Offset 0

padding 7 bytes. Offset 1-7

b, size 8. offset 8-15.

c, size 4. Offset 16 - 19

padding 4 bytes. Offset 20 - 23. Size 0-23 (24): needed to be a multiple of MAX_ALIGN or 8 here

so a C safe POD struct looks like

```rust
#[repr(C)]
struct
    a: u8
    _pad_a: [u8; 7]
    b: u64
    c: u32
    _pad_trail: [u8; 4]
```

