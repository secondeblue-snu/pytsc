# PyTSC
The Educational TSC Toolset in Python

> **Note:** PyTSC is based on the [PyRISC](https://github.com/snu-csl/pyrisc)
> project originally developed by Jin-Soo Kim (Seoul National University).

## About TSC
TSC is an educational 16-bit processor originally designed by Jeff Penfold
and J. Kelly Flanagan at Brigham Young University.
The original TSC course materials are copyrighted by their authors.
See [tsc/README.md](tsc/README.md) for its origin, references, and
copyright notice.

## Prerequisites

The PyTSC toolset requires Python version 3.9 or higher. In addition, the
PyTSC toolset depends on Python modules such as `numpy` and `pyelftools`.
These modules can be installed on Ubuntu 22.04LTS as follows:

```
$ sudo apt-get install python3-numpy python3-pyelftools
```

## Related Tools

[secondeblue-snu/tsc-tools](https://github.com/secondeblue-snu/tsc-tools)
provides a simple assembler and disassembler for TSC, written as Jupyter
notebooks, along with example TSC assembly programs.
The assembler is a minimal tool for small test programs: it supports only
a few directives (`.ORG`, `.BSC`, `.BSS`, `.END`) and does only limited
error checking. It is not a full-featured assembler.

## License
PyTSC is distributed under the BSD 3-Clause License. See [LICENSE](LICENSE).

## Acknowledgement
PyTSC is based on the [PyRISC Project](https://github.com/snu-csl/pyrisc)
by Jin-Soo Kim, Seoul National University.
Portions of the code are derived from PyRISC and are subject to its license.
See [NOTICE](NOTICE).
