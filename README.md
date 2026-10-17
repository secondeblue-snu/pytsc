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

## TSC in Use

Beyond its original course at BYU, TSC has been used in computer organization and computer architecture courses at other universities, where students implement TSC processors in Verilog or Logisim.

The following public repositories (22 in total) show how TSC has been taught and used:

* [kssteven418/snuece_architecture](https://github.com/kssteven418/snuece_architecture) (2017): Vivado projects for a single-cycle CPU, a pipelined CPU, a cache, and DMA
* [iamggyu/2018_spring](https://github.com/iamggyu/2018_spring/tree/master/Computer%20Organization_001) (2018): Course materials, including the lab handout and skeleton code for a single-cycle TSC CPU
* [hansungk/tsc-cpu](https://github.com/hansungk/tsc-cpu) (2018): Multi-cycle and pipelined CPUs with data forwarding and selectable branch predictors, a cache, and DMA with cycle stealing, with lab reports
* [seunghyukcho/pipelined-cpu-verilog](https://github.com/seunghyukcho/pipelined-cpu-verilog) (2019): A pipelined CPU, with the benchmark program used to test it
* [Zeta611/cpu-design](https://github.com/Zeta611/cpu-design) (2019): A TSC CPU running on a Spartan-3E FPGA board, displaying register values on an LCD and LEDs
* [HamHyungkyu/csed311-labs](https://github.com/HamHyungkyu/csed311-labs) (2020): Single-cycle, multi-cycle, and pipelined CPUs, followed by a pipelined CPU with a BTB-based branch predictor and data forwarding, a 2-way set-associative cache, and DMA
* [sjyoopostech/source_CSED311](https://github.com/sjyoopostech/source_CSED311) (2020): Single-cycle, multi-cycle, and pipelined CPUs, a cache compared against a no-cache baseline, and DMA
* [deokhk/CSED311](https://github.com/deokhk/CSED311) (2021): A single-cycle CPU, a multi-cycle CPU with a microcoded controller, a pipelined CPU with data forwarding, and a cache
* [2opk/csed311_lab](https://github.com/2opk/csed311_lab) (2021): Single-cycle, multi-cycle, and pipelined CPUs with a branch predictor, a cache compared against a no-cache baseline, and DMA
* [huijjj/Architecture](https://github.com/huijjj/Architecture) (2021): Single-cycle, multi-cycle, and pipelined CPUs, a cache, and DMA, with notes on the difficulty and pitfalls of each lab
* [jeongjjw/Pipeline_cpu](https://github.com/jeongjjw/Pipeline_cpu) (2021): A pipelined CPU with a BTB-based branch predictor and hazard detection
* [betarixm/CSED311](https://github.com/betarixm/CSED311) (2021): Single-cycle, multi-cycle, and pipelined CPUs, a cache compared against a no-cache baseline, and DMA, with lab reports and datapath diagrams
* [ckswjd99-at-snu/ComputerArchitecture-2021-2](https://github.com/ckswjd99-at-snu/ComputerArchitecture-2021-2) (2021): Single-cycle and multi-cycle CPUs, a pipelined CPU with hazard detection and data forwarding, FIFO and fully associative caches, and DMA
* [akash-ambekar/Physical-Design-of-5-Stage-Pipelined-CPU](https://github.com/akash-ambekar/Physical-Design-of-5-Stage-Pipelined-CPU) (2023): Physical design of a 5-stage pipelined TSC CPU, from synthesis through place-and-route to GDSII, using Cadence Genus, Tempus, and Innovus
* [siliilagi/byuh-cs-coursework](https://github.com/siliilagi/byuh-cs-coursework/tree/main/CS210) (2023): TSC assembly labs and Logisim datapath circuits, along with the original BYU assembler and simulator
* [navytuner/computer-organization-DMA](https://github.com/navytuner/computer-organization-DMA) (2023): A pipelined CPU with branch prediction, a cache, and DMA
* [tk4n9/TSC-CPU](https://github.com/tk4n9/TSC-CPU) (2023): Single-cycle, multi-cycle, and pipelined CPUs with configurable branch prediction and data forwarding, a direct-mapped cache, and DMA with optional cycle stealing
* [junand0/single-cycle-cpu](https://github.com/junand0/single-cycle-cpu) (2024): A single-cycle CPU for a subset of TSC (`ADD`, `ADI`, `LHI`, `JMP`, `WWD`), with test logs and waveforms
* [Heejoon-Kwon/tsc-single-cycle-cpu](https://github.com/Heejoon-Kwon/tsc-single-cycle-cpu) (2026): A single-cycle CPU for the same TSC subset
* [GalacticLearner/perceptron-branch-predictor](https://github.com/GalacticLearner/perceptron-branch-predictor) (2026): A perceptron branch predictor added to a pipelined TSC CPU, built to measure the hardware and cycle overhead of the predictor
* [dyp0122/Proj3_CA](https://github.com/dyp0122/Proj3_CA) (2026): A single-cycle CPU for the same TSC subset
* [emmalee0913/proj-3](https://github.com/emmalee0913/proj-3) (2026): A single-cycle CPU for the same TSC subset

## License
PyTSC is distributed under the BSD 3-Clause License. See [LICENSE](LICENSE).

## Acknowledgement
PyTSC is based on the [PyRISC Project](https://github.com/snu-csl/pyrisc)
by Jin-Soo Kim, Seoul National University.
Portions of the code are derived from PyRISC and are subject to its license.
See [NOTICE](NOTICE).
