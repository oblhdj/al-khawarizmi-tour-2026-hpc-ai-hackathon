# Al-Khawarizmi Tour 2026 Online HPC & AI Hackathon

A practical starter pack for a short online hackathon combining high-performance computing, parallel programming, performance optimization, and agentic AI for scientific computing.

## Theme and focus

- High-Performance Computing (HPC)
- Parallel programming and performance optimization
- Agentic AI applied to scientific computing
- CPU/GPU computing, benchmarking, and scalability
- Python/PyTorch, C, C++, and Julia

The strongest projects should combine a trusted reference implementation, measurable performance improvement, numerical validation, and reproducible benchmarking.

## Recommended project ideas

### 1. Agentic GPU Kernel Optimizer

Build an agent that optimizes two small kernels, such as matrix multiplication plus softmax or layer normalization.

Workflow:

1. Inspect a reference implementation.
2. Generate CUDA, Triton, C++, or PyTorch candidates.
3. Compile and run each candidate.
4. Reject candidates that fail numerical validation.
5. Benchmark accepted candidates.
6. Keep the best candidate and generate an optimization report.

Measure latency, throughput, speedup, maximum absolute/relative error, memory use, compilation time, and candidate pass rate.

### 2. FlashAttention-inspired exact attention benchmark

Compare naive PyTorch attention with a tiled, memory-efficient, Triton, or CUDA implementation. Focus on reducing memory traffic while preserving numerical results.

Measure latency versus sequence length, peak memory, speedup, precision-related error, and forward/backward performance.

### 3. Distributed parameter sweep with MPI and an AI scheduler

Use MPI to distribute heat diffusion, wave propagation, reaction-diffusion, Ising-model, Monte Carlo, or toy materials simulations. Add dynamic scheduling that predicts runtime and reduces worker idle time.

Compare static assignment, a dynamic task queue, and agent-assisted scheduling. Measure makespan, parallel efficiency, load imbalance, communication overhead, scheduler overhead, and agreement with a serial reference.

### 4. Adaptive CPU/GPU stencil solver

Implement a 2D or 3D heat or wave solver with NumPy, C/OpenMP, PyTorch, and GPU backends. Dynamically choose CPU or GPU based on grid size, iteration count, transfer cost, and measured throughput.

Measure time per iteration, total runtime, effective bandwidth, transfer time, utilization, and the CPU/GPU crossover point.

### 5. Multi-backend scientific kernel portability study

Implement one kernel—reduction, histogram, stencil, sparse matrix-vector multiplication, or n-body interaction—in C/OpenMP, CUDA, NumPy, PyTorch, Julia, or Triton. Compare correctness, implementation effort, performance, scalability, and portability.

### 6. Agentic scientific simulation workflow

Create an agent that proposes parameters or experiments for a deterministic Ising, Lennard-Jones, surrogate, or materials simulation. The agent may suggest actions, but a deterministic validator must enforce scientific and computational correctness.

### 7. LLM-assisted performance regression detector

Build a CI-style pipeline that runs correctness tests and benchmarks, compares results with a baseline, flags regressions, and asks an agent to explain likely causes such as allocations, synchronization, loss of vectorization, or communication overhead.

## HPC reading guide

For a structured learning path that applies to all seven project ideas, see:

- [`docs/HPC-READING-GUIDE.md`](docs/HPC-READING-GUIDE.md)

Recommended first-reading sequence:

1. HPC Carpentry foundations
2. LLNL parallel computing introduction
3. Roofline model overview
4. CUDA best-practices memory/performance sections
5. FlashAttention intro (IO-aware optimization)
6. mpi4py basics
7. PyTorch Profiler workflow

## Best recommendations

- **Best overall:** Agentic GPU Kernel Optimizer
- **Best with limited hardware:** Adaptive CPU/GPU Stencil Solver
- **Best distributed project:** MPI Parameter Sweep with Dynamic Scheduling

## Public repositories

- PyTorch benchmark suite: https://github.com/pytorch/benchmark
- Triton: https://github.com/triton-lang/triton
- NVIDIA CUDA Samples: https://github.com/NVIDIA/cuda-samples
- mpi4py: https://github.com/mpi4py/mpi4py
- LLNL RAJA: https://github.com/LLNL/RAJA
- LLNL HPC benchmark data: https://github.com/llnl/ice4hpc_data
- ParBench: https://github.com/Scientific-Computing-Lab/ParBench
- HPCAgent-Bench: https://ited.edu.kg/spcl/HPCAgent-Bench
- Atrex-Bench: https://github.com/alibaba/atrex-bench
- ORNL matsim-agents: https://github.com/ORNL/matsim-agents
- RIKEN HPC Agentic SDK: https://github.com/RIKEN-RCCS/HPC-Agentic-SDK

Use these as references rather than copying a large production system. For a short hackathon, start with one small kernel and add your own validation and benchmarking layer.

## Relevant papers and technical reports

- Roofline model: “Roofline: An Insightful Visual Performance Model for Multicore Architectures” — https://users.cs.duke.edu/~lkw34/papers/roofline-cacm2008.pdf
- FlashAttention: “Fast and Memory-Efficient Exact Attention with IO-Awareness” — https://arxiv.org/abs/2205.14135
- Triton: “Triton: An Intermediate Language and Compiler for Tiled Neural Network Computations” — https://github.com/triton-lang/triton
- Scientific computing in the age of agentic AI — https://www.biorxiv.org/content/10.64898/2026.07.29.741496v2.full
- Leveraging AI for Productive and Trustworthy HPC Software — https://arxiv.org/abs/2505.08135
- Empowering Scientific Workflows with Federated Agents — https://arxiv.org/abs/2505.05428
- ECAS: Validation-Gated Scientific Application Execution — https://arcxiv.org/abs/2609.14211
- AutoKernel: Autonomous GPU Kernel Optimization — https://arxiv.org/abs/2603.21331

## HPC + AI best practices

### Correctness first

- Establish a simple, readable reference implementation.
- Test small hand-checkable inputs and edge cases.
- Compare with NumPy, SciPy, PyTorch, an analytical solution, or a serial implementation.
- Define explicit absolute and relative error tolerances.
- Test non-power-of-two dimensions and multiple dtypes.

### Benchmark rigorously

- Warm up kernels before timing.
- Synchronize GPU work before reading timestamps.
- Use repeated runs and report median/p50 and variance or percentiles.
- Separate compilation time from execution time.
- Record hardware, driver, compiler, library, and input metadata.
- Benchmark several problem sizes instead of one favorable case.

### Profile before optimizing

Inspect arithmetic intensity, memory bandwidth, cache behavior, branch divergence, occupancy, kernel-launch overhead, synchronization, communication, and load balance. Use the roofline model to explain whether the workload is compute-bound, memory-bound, latency-bound, or communication-bound.

### Reduce data movement

Prioritize tiling, blocking, contiguous layouts, structure-of-arrays where appropriate, operator fusion, buffer reuse, batching, and avoiding unnecessary host-device transfers. FlashAttention is a useful example of improving performance primarily by reducing memory traffic.

### Keep agents constrained

Use a sandboxed interface that permits reading code, generating patches, compiling, running bounded benchmarks, and inspecting profiling results. Keep correctness checks independent of the agent. Log prompts, generated code, compiler flags, hardware, seeds, inputs, results, and rejected candidates.

### Language and tooling norms

For C/C++: enable warnings, use sanitizers, RAII, CMake, clear ownership, and explicit synchronization. For Python/PyTorch: use type hints, explicit devices and dtypes, pytest, a linter such as Ruff, and machine-readable benchmark output. For MPI: test with one process first, verify collective ordering, measure communication separately, and use nonblocking communication only when completion semantics are understood.

## Three-week plan

### Week 1 — Baseline and correctness

Choose one kernel or simulation, implement the reference, add tests, define tolerances, build the benchmark harness, and record baseline results.

### Week 2 — Optimization

Profile the baseline, implement one or two optimizations, test multiple sizes and dtypes, and compare every result with the reference.

### Week 3 — Agentic layer and presentation

Add candidate generation, autotuning, scheduling, or regression diagnosis. Track all candidates, produce plots and tables, and prepare a short demonstration showing the baseline, optimized result, correctness check, bottleneck, and speedup.

## Suggested repository structure

```text
hpc-ai-hackathon/
├── README.md
├── pyproject.toml
├── environment.yml
├── src/
│   ├── reference/
│   ├── cpu/
│   ├── cuda_or_triton/
│   ├── agent/
│   └── validation/
├── tests/
├── benchmarks/
├── profiles/
└── results/
```

## Final submission checklist

- Clear problem definition
- Correct reference implementation
- Reproducible setup instructions
- Correctness tests and tolerances
- Baseline and optimized implementations
- Benchmark methodology
- Hardware/software details
- Performance charts and variance
- Profiling evidence
- Limitations and future work

## Strong project title

**Validation-Gated Agentic Optimization of Scientific GPU Kernels**

Implement PyTorch reference kernels, generate Triton or CUDA candidates, validate them against the reference, benchmark them across input sizes, and use a lightweight agent to choose tile sizes and optimization strategies. This is narrow enough for a few weeks while visibly covering AI, HPC, correctness, and measurable performance.
