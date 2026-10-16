# About TSC

TSC is a 16-bit educational processor and instruction set architecture (ISA)
designed by **Jeff Penfold** and **J. Kelly Flanagan** at the Computer Science
Department of **Brigham Young University (BYU)** for the freshman-level
computer organization course CS 143 (circa 1998–2001).

The original course materials are no longer online.

## Motivation

The course was created to "remove the magic" surrounding the internal
operation of computers. Students first wrote TSC assembly programs on a
graphical virtual machine, then implemented TSC itself at the logic level
(LogicWorks) and ran their own programs on it. The textbook, labs,
assembler, and simulator were published on the Web.

To keep it simple enough to be built in one semester, TSC was designed as:

- a 16-bit, load/store, multi-cycle, shared-bus processor
- with fixed-length (16-bit) instructions
- four general-purpose registers (`$0`–`$3`)
- a 64K-word memory (16-bit words, word-addressed)
- a nine-function ALU and a microprogrammed control unit
- one I/O port accessed by dedicated instructions (`RWD`, `WWD`)

## Documentation

Version 1.0 describes TSC as defined in the original BYU CS 143 course
materials. Both documents were compiled and reorganized from those
materials.

- [TSC ISA 1.0](isa-1-0/README.md): machine overview, instruction formats,
  opcodes and function codes, and the operation and encoding of each
  instruction.
- [TSC ABI 1.0](abi-1-0/README.md): assembly language, assembler directives,
  pseudo-instructions, procedure calls, and the binary file format.

## Reference

J. Penfold and J. K. Flanagan, "A First Year Computer Organization Course on
the Web: Make the Magic Disappear," *Proceedings of the 2000 Workshop on
Computer Architecture Education (WCAE '00)*, 2000.
doi:[10.1145/1275240.1275246](https://doi.org/10.1145/1275240.1275246)

Original course materials: `http://www.cs.byu.edu/courses/cs143/`

## Copyright

The original BYU course materials carry the following notice:

> Copyright 1999–2000, J. Kelly Flanagan.
> Permission is granted to copy this work for non-profit educational and
> research use only provided this copyright notice remains intact.
> All other rights reserved.

PyTSC is an independent implementation of the TSC ISA written from its
published specification. It does **not** include the original BYU text,
labs, or software, and it is not affiliated with or endorsed by BYU or the
original authors. If you redistribute any of the original course materials,
follow the notice above.
