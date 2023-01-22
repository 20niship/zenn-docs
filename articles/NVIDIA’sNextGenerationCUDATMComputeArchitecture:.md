---
title: "NVIDIA’s Next Generation  CUDATM Compute Architecture: "
emoji: ""
type: ""
topics: undefined
published: false
---

### A Quick Refresher on CUDA

CUDAはC, C++, Fortran, OpenCL等のプログラミング言語で書かれたプログラムを実行するための NVIDIA GPUのハードウェア/ソフトウェアのアーキテクチャです。  
CUDAプログラムは並列したカーネルと呼ばれています。カーネルとは、複数の並列したスレッドを通して並列してプログラムを実行できます。そしてGPUは格子状に並んだ並列したスレッドブロックでカーネルプログラムの命令を実行します。

CUDAの並列実行  
In the CUDA parallel programming model, each thread has a per-thread private memory space used for register spills, function calls, and C automatic array variables. Each thread block has a per-Block shared memory space used for inter-thread communication, data sharing, and result sharing in parallel algorithms. 

Grids of thread blocks share results in Global Memory space after kernel-wide global synchronization.   

#### Hardware Execution

CUDA’s hierarchy of threads maps to a hierarchy of processors on the GPU; a GPU executes one or more kernel grids; a streaming multiprocessor (SM) executes one or more thread blocks; and CUDA cores and other execution units in the SM execute threads. The SM executes threads in groups of 32 threads called a warp. While programmers can generally ignore warp execution for functional correctness and think of programming one thread, they can greatly improve performance by having threads in a warp execute the same code path and access memory in nearby addresses.   

### An Overview of An Overview of An Overview of An Overview of the Fermi Architecture the Fermi Architecture the Fermi Architecture the Fermi Architecture

The first Fermi based GPU, implemented with 3.0 billion transistors, features up to 512 CUDA cores. A CUDA core executes a floating point or integer instruction per clock for a thread. The 512 CUDA cores are organized in 16 SMs of 32 cores each. The GPU has six 64-bit memory partitions, for a 384-bit memory interface, supporting up to a total of 6 GB of GDDR5 DRAM memory. A host interface connects the GPU to the CPU via PCI-Express. The GigaThread global scheduler distributes thread blocks to SM thread schedulers.   