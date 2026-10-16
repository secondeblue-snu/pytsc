# TSC Instruction Set Architecture 1.0

This document defines the instruction set of TSC, a 16-bit teaching
processor from BYU CS 143 (1999–2000). Version 1.0 describes the
instruction set as defined in the original BYU course materials.

The assembly language and binary file format are described in
[TSC ABI 1.0](../abi-1-0/README.md).

## Contents

1. [Machine overview](#1-machine-overview)
2. [Notation](#2-notation)
3. [Instruction formats](#3-instruction-formats)
4. [Opcode and function-code maps](#4-opcode-and-function-code-maps)
5. [Instruction summary](#5-instruction-summary)
6. [Arithmetic and logical instructions](#6-arithmetic-and-logical-instructions)
7. [Data transfer instructions](#7-data-transfer-instructions)
8. [Control instructions](#8-control-instructions)

---

## 1. Machine overview

| Item | Description |
|---|---|
| Word size | 16 bits. Registers, memory words, and instructions are all one word wide. |
| Registers | Four general-purpose registers, `$0`–`$3`. No register is hard-wired to zero. `JAL` and `JRL` write the return address into `$2`. |
| Program counter | 16-bit `PC`. Fetching an instruction adds 1 to `PC`. |
| Memory | 65,536 words (addresses `0x0000`–`0xFFFF`), **word-addressed**. Instructions and data share the same memory. |
| I/O | One 16-bit input port, read by `RWD`, and one 16-bit output port, written by `WWD`. Both instructions wait for a handshake with the device. |
| Interrupts | Can be enabled with `ENI` and disabled with `DSI`. The interrupt mechanism itself is not specified. |
| Status flags | None. Conditional branches test register values directly. |

Arithmetic is 16-bit two's complement. Results are truncated to 16 bits and
overflow is not reported.

## 2. Notation

| Notation | Meaning |
|---|---|
| `PC` | Address of the instruction being executed. |
| `PC + 1` | Address of the next instruction. |
| `$rs`, `$rt`, `$rd` | Register selected by the `rs`, `rt`, or `rd` field. |
| `x[m:n]` | Bits *m* down to *n* of `x`. |
| `a ‖ b` | Concatenation of `a` (high-order bits) and `b` (low-order bits). |
| `0ⁿ` | *n* zero bits. |
| `sext(x)` | `x` sign-extended to 16 bits. |
| `zext(x)` | `x` zero-extended to 16 bits. |
| `M[a]` | Memory word at address `a`. |
| `←` | Assignment. |

## 3. Instruction formats

Every instruction is one 16-bit word in one of three formats.

| Field | Bits | Use |
|---|---|---|
| `opcode` | 15–12 | Selects the instruction. Opcode 15 selects the R-format group, where `funct` names the instruction. |
| `rs` | 11–10 | Source register. |
| `rt` | 9–8 | Second source register, or destination register in I-format instructions. |
| `rd` | 7–6 | Destination register in R-format instructions. |
| `funct` | 5–0 | Function code of an R-format instruction. |
| `imm` | 7–0 | 8-bit immediate, or a branch or memory offset. |
| `target` | 11–0 | 12-bit jump target. |

The hardware ignores fields an instruction does not use. The assembler sets
them to zero.

The 8-bit immediate is widened to 16 bits in one of three ways:

## 4. Opcode and function-code maps

## 5. Instruction summary

| Mnemonic | Name | Fmt | Opcode | Funct | Operation |
|---|---|:-:|:-:|:-:|---|
| [`ADD`](#add--add) | Add | R | 15 | 0 | `$rd ← $rs + $rt` |
| [`SUB`](#sub--subtract) | Subtract | R | 15 | 1 | `$rd ← $rs − $rt` |
| [`AND`](#and--bitwise-and) | Bitwise AND | R | 15 | 2 | `$rd ← $rs & $rt` |
| [`ORR`](#orr--bitwise-or) | Bitwise OR | R | 15 | 3 | `$rd ← $rs \| $rt` |
| [`NOT`](#not--bitwise-not) | Bitwise NOT | R | 15 | 4 | `$rd ← ~$rs` |
| [`TCP`](#tcp--twos-complement) | Two's complement | R | 15 | 5 | `$rd ← −$rs` |
| [`SHL`](#shl--shift-left) | Shift left | R | 15 | 6 | `$rd ← $rs[14:0] ‖ 0` |
| [`SHR`](#shr--arithmetic-shift-right) | Arithmetic shift right | R | 15 | 7 | `$rd ← $rs[15] ‖ $rs[15:1]` |
| [`ADI`](#adi--add-immediate) | Add immediate | I | 4 | – | `$rt ← $rs + sext(imm)` |
| [`ORI`](#ori--or-immediate) | OR immediate | I | 5 | – | `$rt ← $rs \| zext(imm)` |
| [`LHI`](#lhi--load-high-immediate) | Load high immediate | I | 6 | – | `$rt ← imm ‖ 0⁸` |
| [`LWD`](#lwd--load-word) | Load word | I | 7 | – | `$rt ← M[$rs + sext(imm)]` |
| [`SWD`](#swd--store-word) | Store word | I | 8 | – | `M[$rs + sext(imm)] ← $rt` |
| [`RWD`](#rwd--read-word) | Read word | R | 15 | 27 | `$rd ← input port` |
| [`WWD`](#wwd--write-word) | Write word | R | 15 | 28 | `output port ← $rs` |
| [`BNE`](#bne--branch-if-not-equal) | Branch if not equal | I | 0 | – | if `$rs ≠ $rt`: `PC ← PC + 1 + sext(imm)` |
| [`BEQ`](#beq--branch-if-equal) | Branch if equal | I | 1 | – | if `$rs = $rt`: `PC ← PC + 1 + sext(imm)` |
| [`BGZ`](#bgz--branch-if-greater-than-zero) | Branch if greater than zero | I | 2 | – | if `$rs > 0`: `PC ← PC + 1 + sext(imm)` |
| [`BLZ`](#blz--branch-if-less-than-zero) | Branch if less than zero | I | 3 | – | if `$rs < 0`: `PC ← PC + 1 + sext(imm)` |
| [`JMP`](#jmp--jump) | Jump | J | 9 | – | `PC ← (PC + 1)[15:12] ‖ target` |
| [`JAL`](#jal--jump-and-link) | Jump and link | J | 10 | – | `$2 ← PC + 1`; `PC ← (PC + 1)[15:12] ‖ target` |
| [`JPR`](#jpr--jump-register) | Jump register | R | 15 | 25 | `PC ← $rs` |
| [`JRL`](#jrl--jump-register-and-link) | Jump register and link | R | 15 | 26 | `$2 ← PC + 1`; `PC ← $rs` |
| [`HLT`](#hlt--halt) | Halt | R | 15 | 29 | stop execution |
| [`ENI`](#eni--enable-interrupts) | Enable interrupts | R | 15 | 30 | enable interrupts |
| [`DSI`](#dsi--disable-interrupts) | Disable interrupts | R | 15 | 31 | disable interrupts |

In the examples that follow, the hexadecimal number after each instruction
is its encoding.

---

## 6. Arithmetic and logical instructions

### ADD — Add

```
ADD $rd, $rs, $rt
```

**Operation:** `$rd ← $rs + $rt`

Adds two registers and writes the sum to `$rd`.

```asm
ADD $3, $1, $2      ; F6C0   $3 ← $1 + $2
```

### SUB — Subtract

```
SUB $rd, $rs, $rt
```

**Operation:** `$rd ← $rs + ~$rt + 1` (that is, `$rs − $rt`)

Subtracts `$rt` from `$rs` and writes the difference to `$rd`. If `$rs`
and `$rt` are the same register, the result is zero.

```asm
SUB $1, $1, $0      ; F441   $1 ← $1 − $0
```

### AND — Bitwise AND

```
AND $rd, $rs, $rt
```

**Operation:** `$rd ← $rs & $rt`

```asm
AND $2, $0, $3      ; F382   $2 ← $0 & $3
```

### ORR — Bitwise OR

```
ORR $rd, $rs, $rt
```

**Operation:** `$rd ← $rs | $rt`

```asm
ORR $0, $0, $1      ; F103   $0 ← $0 | $1
```

### NOT — Bitwise NOT

```
NOT $rd, $rs
```

**Operation:** `$rd ← ~$rs`

Inverts every bit of `$rs`.

```asm
NOT $1, $2          ; F844   $1 ← ~$2
```

### TCP — Two's complement

```
TCP $rd, $rs
```

**Operation:** `$rd ← ~$rs + 1`

Negates `$rs`.

```asm
TCP $3, $3          ; FCC5   $3 ← −$3
```

### SHL — Shift left

```
SHL $rd, $rs
```

**Operation:** `$rd ← $rs[14:0] ‖ 0`

Shifts `$rs` left by one bit and fills bit 0 with zero. The original
documentation calls this an arithmetic shift. Shifting left is the same in
both its arithmetic and its logical form.

```asm
SHL $0, $0          ; F006   $0 ← $0 << 1
```

### SHR — Arithmetic shift right

```
SHR $rd, $rs
```

**Operation:** `$rd ← $rs[15] ‖ $rs[15:1]`

Shifts `$rs` right by one bit and keeps the sign bit, so a negative value
stays negative.

```asm
SHR $2, $1          ; F487   $2 ← $1 >> 1 (signed)
```

### ADI — Add immediate

```
ADI $rt, $rs, imm
```

**Operation:** `$rt ← $rs + sext(imm)`

The immediate must be in the range −128 to 127. `ADI $rt, $rs, 0` copies
`$rs` to `$rt`.

```asm
ADI $1, $1, 1       ; 4501   $1 ← $1 + 1
ADI $0, $3, -2      ; 4CFE   $0 ← $3 − 2
```

### ORI — OR immediate

```
ORI $rt, $rs, imm
```

**Operation:** `$rt ← $rs | zext(imm)`

The immediate is **zero-extended**, so the high byte of `$rs` passes through
unchanged. `ORI` is the instruction that fills in the low byte after `LHI`.

```asm
ORI $2, $2, 0x34    ; 5A34   $2 ← $2 | 0x0034
```

---

## 7. Data transfer instructions

### LHI — Load high immediate

```
LHI $rt, imm
```

**Operation:** `$rt ← imm ‖ 0⁸`

Places the immediate in the high byte of `$rt` and clears the low byte.
Because there is no zero register, `LHI $rt, 0` is the usual way to clear a
register.

```asm
LHI $2, 0x12        ; 6212   $2 ← 0x1200
```

### LWD — Load word

```
LWD $rt, $rs, offset
```

**Operation:** `$rt ← M[$rs + sext(offset)]`

```asm
LWD $1, $3, 0       ; 7D00   $1 ← M[$3]
LWD $0, $2, -1      ; 78FF   $0 ← M[$2 − 1]
```

### SWD — Store word

```
SWD $rt, $rs, offset
```

**Operation:** `M[$rs + sext(offset)] ← $rt`

The register that holds the data, `$rt`, comes first, as in `LWD`.

```asm
SWD $1, $3, 2       ; 8D02   M[$3 + 2] ← $1
```

### RWD — Read word

```
RWD $rd
```

**Operation:** `$rd ← input port`

Waits until the input device signals that a word is ready, then reads that
word into `$rd`.

```asm
RWD $0              ; F01B   $0 ← input port
```

### WWD — Write word

```
WWD $rs
```

**Operation:** `output port ← $rs`

Puts `$rs` on the output port and signals the output device. Execution
continues only after the device acknowledges the word.

```asm
WWD $1              ; F41C   output port ← $1
```

---

## 8. Control instructions

### Branches

The four branches are I-format instructions. Each one compares a register
with another register or with zero. If the condition holds, execution
continues at

```
PC + 1 + sext(offset)
```

which means the offset counts from the instruction **after** the branch.
The reachable range is 128 words back to 127 words forward from that
instruction. If you write a label as the offset, the assembler computes the
offset for you. If you write a number, the assembler encodes that number
unchanged.

`BGZ` and `BLZ` compare the register as a signed (two's complement) value.

### BNE — Branch if not equal

```
BNE $rs, $rt, offset
```

**Operation:** if `$rs ≠ $rt`: `PC ← PC + 1 + sext(offset)`

```asm
BNE $0, $1, -3      ; 01FD   if $0 ≠ $1, go back 2 words from this branch
```

### BEQ — Branch if equal

```
BEQ $rs, $rt, offset
```

**Operation:** if `$rs = $rt`: `PC ← PC + 1 + sext(offset)`

```asm
BEQ $2, $3, 4       ; 1B04   if $2 = $3, skip the next 4 words
```

### BGZ — Branch if greater than zero

```
BGZ $rs, offset
```

**Operation:** if `$rs > 0`: `PC ← PC + 1 + sext(offset)`

```asm
BGZ $1, -6          ; 24FA   if $1 > 0, go back 5 words from this branch
```

### BLZ — Branch if less than zero

```
BLZ $rs, offset
```

**Operation:** if `$rs < 0`: `PC ← PC + 1 + sext(offset)`

```asm
BLZ $0, 2           ; 3002   if $0 < 0, skip the next 2 words
```

### Jumps

`JMP` and `JAL` take a 12-bit target. They keep the top four bits of the
program counter and replace the other twelve with the target:

```
PC ← (PC + 1)[15:12] ‖ target
```

As a result, `JMP` and `JAL` can only reach addresses inside the current
4,096-word region of memory. To reach any address, load it into a register
(see [`LAD`](../abi-1-0/README.md#lad--load-address)) and use `JPR` or `JRL`.

> [!NOTE]
> The original prose takes the top four bits from the jump instruction's
> own address (`PC[15:12]`). The original formal operation and microprogram
> take them after the PC has been incremented (`(PC + 1)[15:12]`), and this
> document follows them. The two differ only for a jump in the last word of
> a 4,096-word region, such as `0x0FFF`.

### JMP — Jump

```
JMP target
```

**Operation:** `PC ← (PC + 1)[15:12] ‖ target`

```asm
JMP 0x040           ; 9040   at 0x3A10, jumps to 0x3040
```

### JAL — Jump and link

```
JAL target
```

**Operation:** `$2 ← PC + 1`, then `PC ← (PC + 1)[15:12] ‖ target`

Calls a procedure. The return address goes into `$2`, and `JPR $2` returns.

```asm
JAL 0x200           ; A200   at 0x0105: $2 ← 0x0106, jumps to 0x0200
```

### JPR — Jump register

```
JPR $rs
```

**Operation:** `PC ← $rs`

```asm
JPR $2              ; F819   return from a procedure called by JAL or JRL
```

### JRL — Jump register and link

```
JRL $rs
```

**Operation:** `$2 ← PC + 1`, then `PC ← $rs`

Calls a procedure at any address.

```asm
JRL $1              ; F41A   $2 ← return address, PC ← $1
```

> [!NOTE]
> `JRL` writes `$2` before it jumps, as the original microprogram does. As a
> result, `JRL $2` jumps to the next instruction and only sets `$2` to
> `PC + 1`.

### HLT — Halt

```
HLT
```

Stops the machine. No further instruction is fetched.

```asm
HLT                 ; F01D
```

### ENI — Enable interrupts

```
ENI
```

Enables interrupts. If interrupts are already enabled, nothing happens.

```asm
ENI                 ; F01E
```

### DSI — Disable interrupts

```
DSI
```

Disables interrupts. If interrupts are already disabled, nothing happens.

```asm
DSI                 ; F01F
```

> [!NOTE]
> The original specification defines the encodings of `ENI` and `DSI` but
> not what an interrupt does: which events raise one, where control goes,
> and how the interrupted program resumes.

---

<sub>TSC was designed by Jeff Penfold and J. Kelly Flanagan at Brigham Young
University. This document was compiled and reorganized from the CS 143
course materials (Performance Evaluation Laboratory, BYU, 1999–2000). Its
text and figures are newly written, and it is not affiliated with or
endorsed by BYU or the original authors. The copyright notice of the
original materials is given in [About TSC](../README.md).</sub>
