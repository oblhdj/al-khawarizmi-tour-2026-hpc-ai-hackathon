# HPC Reading Guide for the Al-Khawarizmi Tour 2026 HPC & AI Hackathon

This guide is a practical path through core HPC topics that are shared across all seven project ideas in this repository. It is intentionally organized as a progression, not a flat link list.

## How to use this guide

### Learning goals

By the end of this guide, you should be able to answer these five questions for any project idea:

1. What is the parallel unit (thread, process, block, task)?
2. Where does data move, and what movement is avoidable?
3. What is the likely bottleneck (compute, memory, communication, synchronization)?
4. What is the correctness oracle and numerical tolerance?
5. What benchmark method will prove improvement fairly?

### Suggested pace

- **First meeting prep (2–3 hours):** follow [Read this first](#read-this-first-path-for-the-first-hackathon-meeting).
- **Week 1:** focus on correctness + baseline benchmarking sections.
- **Week 2:** focus on profiling + kernel/scheduling optimization sections.
- **Week 3:** focus on agentic workflow, candidate tracking, and regression detection.

---

## Core HPC mental model

Before tools and frameworks, align on these concepts:

- **Parallelism:** doing useful work concurrently.
- **Memory hierarchy:** registers/cache/shared memory are faster than main/global memory.
- **Data movement:** often more expensive than arithmetic.
- **Compute-bound vs memory-bound:** performance limits differ by workload.
- **Roofline model:** links arithmetic intensity with achievable performance.

### Start here

1. **HPC Carpentry lessons** — https://www.hpc-carpentry.org/lessons/
   - **Read first:** Intro/HPC foundations and performance-focused lessons.
   - **Why it matters:** practical foundations for terminology and measurement discipline.

2. **LLNL Introduction to Parallel Computing** — https://hpc.llnl.gov/documentation/tutorials/introduction-parallel-computing-tutorial
   - **Read first:** conceptual sections on decomposition, dependencies, and scaling.
   - **Why it matters:** helps frame each project as a decomposition + bottleneck problem.

3. **Berkeley Lab Roofline material** — https://crd.lbl.gov/divisions/amcr/computer-science-amcr/par/research/roofline/
   - **Read first:** overview material introducing operational intensity and roofline ceilings.
   - **Why it matters:** gives a common model for explaining why optimization did or did not work.

---

## Parallel programming: OpenMP, MPI, scheduling, and load balance

Use this section for CPU shared-memory parallelism and distributed runs.

- **OpenMP:** within one shared-memory node.
- **MPI:** process-based communication across processes/nodes.
- **Scheduling:** static vs dynamic work assignment.
- **Synchronization:** barriers/locks/collectives have real costs.

### Core resources

1. **OpenMP reference guides** — https://www.openmp.org/resources/refguides/
   - **Read first:** syntax/reference cards for `parallel`, `for`, scheduling, reduction, synchronization.
   - **Why it matters:** fast lookup while implementing CPU baseline and OpenMP variants.

2. **MPI Forum documentation** — https://www.mpi-forum.org/docs/
   - **Read first:** standard documentation landing page and current standard materials.
   - **Why it matters:** authoritative behavior for collectives and point-to-point communication.

3. **mpi4py documentation** — https://mpi4py.github.io/mpi4py/stable/
   - **Read first:** tutorials/API basics around communicators (`COMM_WORLD`), collectives, and sends/receives.
   - **Why it matters:** fastest path to distributed parameter sweep prototypes in Python.

---

## GPU fundamentals

For CUDA/Triton/PyTorch kernel work, understand execution and memory first:

- Thread/block/grid execution model
- Warp behavior, occupancy, divergence
- Coalescing and memory locality
- Host↔device transfer cost
- Profiling-guided optimization

### Core resources

1. **NVIDIA CUDA C++ Programming Guide** — https://docs.nvidia.com/cuda/cuda-c-programming-guide/
   - **Read first:** execution model + memory hierarchy chapters.
   - **Why it matters:** conceptual baseline for writing correct GPU kernels.

2. **NVIDIA CUDA C++ Best Practices Guide** — https://docs.nvidia.com/cuda/cuda-c-best-practices-guide/
   - **Read first:** memory access patterns, occupancy, and performance strategy sections.
   - **Why it matters:** directly informs practical kernel optimization decisions.

---

## GPU kernel programming: Triton, tiling, reductions, fusion, attention

This section connects GPU fundamentals to modern kernel authoring.

### Core resources

1. **Triton programming guide/tutorials** — https://triton-lang.org/main/programming-guide/
   - **Read first:** introduction and first tutorials.
   - **Why it matters:** productive path for custom GPU kernels and kernel autotuning.

2. **FlashAttention paper** — https://arxiv.org/abs/2205.14135
   - **Read first:** abstract, introduction, and sections discussing IO-awareness and tiling motivation.
   - **Why it matters:** exemplar of memory-traffic reduction as the key to speedup.

---

## Benchmarking and profiling

Optimization claims must be reproducible and profiler-supported.

- Warm up before timing
- Synchronize asynchronous GPU work
- Repeat measurements; report central tendency and spread
- Use traces/counters to explain speedups

### Core resources

1. **PyTorch Profiler docs/tutorial**
   - Docs: https://docs.pytorch.org/docs/stable/profiler.html
   - Tutorial: https://docs.pytorch.org/tutorials/recipes/recipes/profiler_recipe.html
   - **Read first:** profiler recipe/tutorial workflow.
   - **Why it matters:** identifies expensive ops and memory hotspots in PyTorch baselines.

2. **NVIDIA Nsight Systems documentation** — https://docs.nvidia.com/nsight-systems/
   - **Read first:** getting started and timeline interpretation.
   - **Why it matters:** reveals end-to-end CPU/GPU overlap, launch overhead, and transfer stalls.

3. **NVIDIA Nsight Compute documentation** — https://docs.nvidia.com/nsight-compute/
   - **Read first:** quickstart and kernel analysis workflow.
   - **Why it matters:** kernel-level metrics (occupancy, memory throughput, stall reasons).

---

## Numerical correctness and reproducibility

Performance only counts if output is scientifically acceptable.

Focus on:

- Absolute/relative tolerances
- Floating-point non-associativity
- Deterministic validation harnesses
- Edge-case sizes and dtypes

Use the repository norm: **reference implementation first, then optimize, always validate.**

---

## Scaling and performance analysis

Use standard scaling language in reports:

- **Speedup:** baseline time / optimized time
- **Efficiency:** speedup / number of workers
- **Strong scaling:** fixed problem size, more workers
- **Weak scaling:** increased problem size with more workers
- **Amdahl/Gustafson:** limits and opportunities for parallel gain

Link this analysis with Roofline and profiling evidence.

---

## Agentic HPC/scientific computing

Agentic components should be constrained and auditable:

- Sandbox all candidate generation
- Independent validation gates (agent cannot self-certify)
- Candidate tracking: accepted/rejected + reason
- Regression detection against stored baseline metrics

Relevant existing references in this repository:

- HPCAgent-Bench: https://ited.edu.kg/spcl/HPCAgent-Bench
- Atrex-Bench: https://github.com/alibaba/atrex-bench
- ORNL matsim-agents: https://github.com/ORNL/matsim-agents
- RIKEN HPC-Agentic SDK: https://github.com/RIKEN-RCCS/HPC-Agentic-SDK

---

## Resource map: what to read first and why

| Resource | Read first | Why it matters to this hackathon |
|---|---|---|
| HPC Carpentry lessons | Intro/HPC foundations + performance lessons | Shared vocabulary, cluster/HPC workflow, baseline rigor |
| LLNL Intro to Parallel Computing | decomposition + scaling concepts | Cross-project framework for parallel design decisions |
| Berkeley Lab Roofline material | roofline overview | Distinguish compute limits from memory limits |
| CUDA C++ Programming Guide | execution model + memory hierarchy | Correct mental model for CUDA/Triton kernels |
| CUDA C++ Best Practices | memory access + occupancy guidance | Practical GPU optimization rules |
| FlashAttention paper | intro + IO-aware idea | Concrete case where reduced data movement drives speedup |
| MPI Forum docs | standard docs landing page | Authoritative MPI semantics for communication correctness |
| mpi4py docs | communicator basics + collectives | Fast Python path for distributed parameter sweep prototypes |
| OpenMP reference guides | pragma reference cards | Reliable CPU parallel baseline and scheduling variants |
| PyTorch Profiler docs/tutorial | profiler recipe/workflow | Find bottlenecks before optimizing |
| Nsight Systems docs | getting-started timeline workflow | End-to-end host/device performance visibility |
| Nsight Compute docs | quickstart + kernel analysis | Per-kernel bottleneck diagnosis |
| Triton programming guide | intro + first tutorials | Productive custom kernel implementation path |
| PyTorch benchmark repo | browse benchmark patterns | Ideas for benchmark organization and reproducibility |
| Triton repo | examples/tutorial links | Real-world Triton kernels and patterns |
| NVIDIA CUDA Samples | selected sample kernels | Trusted CUDA reference patterns |
| mpi4py repo | examples directory | Practical mpi4py usage examples |
| LLNL RAJA | README/docs overview | Portability-layer perspective for backend comparisons |
| LLNL HPC benchmark data | dataset README and metadata | Example of benchmark data organization |
| ParBench | README + benchmark list | Parallel benchmark structure ideas |
| HPCAgent-Bench | README/tasks | Agentic-HPC benchmark framing |
| Atrex-Bench | README/workflow | Agent-assisted optimization benchmark framing |
| ORNL matsim-agents | README/architecture notes | Agentic scientific workflow inspiration |
| RIKEN HPC-Agentic SDK | README and examples | Agentic HPC orchestration patterns |

---

## Mapping resources to the seven repository project suggestions

| Project suggestion | Highest-priority topics | Best first resources |
|---|---|---|
| 1. Agentic GPU Kernel Optimizer | GPU execution, kernel optimization, validation gates, profiling | CUDA Programming Guide, CUDA Best Practices, Triton Guide, Nsight Compute, PyTorch Profiler, HPCAgent-Bench/Atrex-Bench |
| 2. FlashAttention-inspired benchmark | Memory hierarchy, tiling, IO-awareness, fair benchmarking | FlashAttention paper, Roofline material, CUDA Best Practices, Nsight Systems/Compute |
| 3. MPI parameter sweep + AI scheduling | Distributed communication, load balancing, scheduling overhead | MPI Forum docs, mpi4py docs, LLNL Intro, HPC Carpentry |
| 4. Adaptive CPU/GPU stencil solver | Crossover analysis, transfer cost, scaling, benchmark rigor | Roofline material, CUDA Best Practices, OpenMP references, PyTorch Profiler |
| 5. Multi-backend portability study | Correctness parity, backend tradeoffs, scaling and reproducibility | LLNL Intro, RAJA repo, ParBench, CUDA/OpenMP/MPI references |
| 6. Agentic scientific simulation workflow | Deterministic validation, candidate governance, experiment tracking | ORNL matsim-agents, RIKEN SDK, HPCAgent-Bench, LLNL Intro |
| 7. AI-assisted regression detection | Repeatable benchmarks, metadata discipline, profiler-based diagnosis | PyTorch benchmark, PyTorch Profiler, Nsight Systems, benchmark data references |

---

## Read this first path for the first hackathon meeting

If you have limited prep time, use this order:

1. **HPC Carpentry lessons** (foundations/performance overview)
2. **LLNL Intro to Parallel Computing** (parallel decomposition and scaling mindset)
3. **Roofline material** (compute-vs-memory diagnosis)
4. **CUDA Best Practices** (memory access + occupancy fundamentals)
5. **FlashAttention paper intro** (IO-aware optimization intuition)
6. **mpi4py basics** (`COMM_WORLD`, collectives) for distributed project option
7. **PyTorch Profiler recipe** (how to locate bottlenecks)

Output for meeting discussion:

- one candidate project,
- one correctness oracle,
- one benchmark table format,
- one risk-mitigation fallback (minimum viable demo).

---

## Compact glossary

- **Arithmetic intensity:** operations per byte moved from memory.
- **Amdahl’s Law:** speedup limit from serial fraction.
- **Coalescing:** combining neighboring memory accesses efficiently on GPU.
- **Divergence:** warp threads taking different branches, reducing efficiency.
- **Efficiency (parallel):** speedup divided by number of workers.
- **Load balancing:** distributing work so workers finish at similar times.
- **Occupancy:** active warps relative to hardware capacity.
- **Roofline model:** performance bound model using bandwidth and compute ceilings.
- **Strong scaling:** fixed problem size, increase workers.
- **Synchronization overhead:** waiting cost at barriers/locks/collectives.
- **Weak scaling:** increase problem size proportionally with workers.

---

## Project evaluation checklist (use before implementation)

- [ ] **Parallel unit defined:** thread/block/process/task ownership is explicit.
- [ ] **Data movement mapped:** host-device, cache/shared/global, MPI communication paths identified.
- [ ] **Expected bottleneck stated:** compute, memory, communication, or synchronization.
- [ ] **Correctness oracle defined:** trusted reference + tolerance policy.
- [ ] **Benchmark methodology fixed:** warm-up, repetitions, synchronization, input-size sweep, summary statistics.
- [ ] **Hardware/software metadata captured:** CPU/GPU model, drivers, compiler/interpreter, library versions, dtype/shape.
- [ ] **Minimum viable demo scoped:** small but complete baseline + validated improvement.

This checklist is intentionally short so teams can apply it to any of the seven suggested projects.
