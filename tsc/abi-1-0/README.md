# TSC ABI 1.0

This document describes the assembly language, calling convention, and
binary file format of TSC.

Unlike the instruction set, these are not part of a formal specification.
They describe the conventions of the tools used in BYU CS 143, chiefly its
assembler and loader. Other courses that use TSC may follow different
conventions. Version 1.0 describes these conventions as given in the
original BYU course materials.

The instructions and notation are defined in
[TSC ISA 1.0](../isa-1-0/README.md).

## Contents

1. [Assembly language](#1-assembly-language)
2. [Assembler directives](#2-assembler-directives)
3. [Pseudo-instructions](#3-pseudo-instructions)
4. [Procedure calls](#4-procedure-calls)
5. [Binary file format](#5-binary-file-format)
6. [Example program](#6-example-program)

---

## 1. Assembly language

A source line has up to four parts, separated by spaces or tabs:

```
[label]   mnemonic   operands   [; comment]
```

- **Label.** Optional. A label is a name that starts with a letter and
  contains letters and digits. Its value is the address of the line it is
  on.
- **Mnemonic.** An instruction, a pseudo-instruction, or a directive.
  Directive names start with a period (`.`).
- **Operands.** Separated by commas. An operand is one of the following:
  - a register: `$0`–`$3`
  - a number in decimal, or in hexadecimal with the prefix `0x`
  - a label
  - an expression of two terms joined by `+`, `-`, `*`, or `/`, such as
    `TABLE + 4` or `SIZE * 2`. The assembler evaluates it to a constant.
    Expressions are not allowed as **branch** offsets.
- **Comments.** Everything after `;` is ignored. A line whose first
  character is `*` is a comment line.

## 2. Assembler directives

| Directive | Label | Meaning |
|---|---|---|
| `.ORG address` | not allowed | The next instruction or data word goes at `address`. Each `.ORG` starts a new segment. If the program has no `.ORG`, assembly starts at address 0. |
| `.END [operand]` | not allowed | Marks the end of the source. The assembler ignores everything after it. Every program must end with `.END`. The operand is optional; it is ignored but is commonly used to name the entry point. |
| `.BSC v1, v2, …` | usual | Allocates one word per value and initializes the words to those values. The values may be numbers, previously defined labels, or expressions. |
| `.BSS n` | usual | Allocates `n` words and sets them to `0x0000`. `n` must be positive. |
| `label .EQU value` | required | Gives `label` the constant `value`. No memory is allocated. |

A label used inside an operand of `.ORG`, `.BSC`, `.BSS`, or `.EQU` must be
defined earlier in the source.

## 3. Pseudo-instructions

Pseudo-instructions are written like instructions, but the assembler
replaces each one with real instructions.

### LAD — Load address

```
LAD $rt, value
```

**Operation:** `$rt ← value` (any 16-bit constant)

No single TSC instruction can load a 16-bit constant, so the assembler
expands `LAD` into [`LHI`](../isa-1-0/README.md#lhi--load-high-immediate) followed by
[`ORI`](../isa-1-0/README.md#ori--or-immediate):

```asm
LHI $rt, value[15:8]
ORI $rt, $rt, value[7:0]
```

Despite the name, `value` does not have to be an address. For example:

```asm
LAD $1, 0xBEEF      ; 61BE   LHI $1, 0xBE
                    ; 55EF   ORI $1, $1, 0xEF
```

## 4. Procedure calls

The hardware provides two call instructions and one return instruction:

| Purpose | Instruction | Reach |
|---|---|---|
| Call | [`JAL target`](../isa-1-0/README.md#jal--jump-and-link) | Within the current 4,096-word region |
| Call | [`JRL $rs`](../isa-1-0/README.md#jrl--jump-register-and-link) | Any address (load it with `LAD` first) |
| Return | [`JPR $2`](../isa-1-0/README.md#jpr--jump-register) | Any address |

Both call instructions write the return address to `$2`, so `$2` serves as
the link register. A procedure that makes a call of its own must save `$2`
before the call and restore it afterwards.

> [!NOTE]
> TSC 1.0 defines nothing beyond the link register. There is no stack
> pointer, no rule for passing arguments or results, and no division into
> caller-saved and callee-saved registers. Each program documents its own
> conventions.

## 5. Binary file format

The assembler writes a binary file of 16-bit words. The file holds one or
more segments, one per `.ORG`.

| Words | Contents |
|---|---|
| 1 | `S`, the number of segments |
| 2 | Start address of segment 1 |
| 3 | `N₁`, the number of words in segment 1 |
| 4 … 3 + `N₁` | Contents of segment 1 |
| next 2 words | Start address and length of segment 2, followed by its contents, and so on through segment `S` |

A loader reads the segment count first. For each segment it reads the start
address and the length, then copies that many words into memory starting
at that address.

## 6. Example program

This program adds up a five-element table and sends the sum to the output
port.

```asm
* Sum the five words at TABLE and write the total to the output port.
        .ORG  0x0000
START   LAD   $3, TABLE       ; 0000: 6300  0001: 5F10   $3 ← address of TABLE
        LHI   $0, 0           ; 0002: 6000               sum ← 0
        LHI   $1, 0           ; 0003: 6100
        ADI   $1, $1, 5       ; 0004: 4505               count ← 5
LOOP    LWD   $2, $3, 0       ; 0005: 7E00               $2 ← next element
        ADD   $0, $0, $2      ; 0006: F200               sum ← sum + $2
        ADI   $3, $3, 1       ; 0007: 4F01               advance the pointer
        ADI   $1, $1, -1      ; 0008: 45FF               count ← count − 1
        BGZ   $1, LOOP        ; 0009: 24FB               offset = 0x0005 − 0x000A = −5
        WWD   $0              ; 000A: F01C               output 14 (0x000E)
        HLT                   ; 000B: F01D

        .ORG  0x0010
TABLE   .BSC  3, 1, 4, 1, 5   ; 0010–0014
        .END  START
```

The assembler produces this binary file:

```
0002                                                  2 segments
0000 000C                                             segment 1: 12 words at 0x0000
6300 5F10 6000 6100 4505 7E00 F200 4F01 45FF 24FB F01C F01D
0010 0005                                             segment 2: 5 words at 0x0010
0003 0001 0004 0001 0005
```

---

<sub>TSC was designed by Jeff Penfold and J. Kelly Flanagan at Brigham Young
University. This document was compiled and reorganized from the CS 143
course materials (Performance Evaluation Laboratory, BYU, 1999–2000). Its
text and figures are newly written, and it is not affiliated with or
endorsed by BYU or the original authors. The copyright notice of the
original materials is given in [About TSC](../README.md).</sub>
