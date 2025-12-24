# Rust-Ultra-GEMM: High-Performance CPU Computing Engine 

**Rust-Ultra-GEMM** is a cutting-edge linear algebra library implemented in Rust, engineered to push the limits of CPU throughput. By combining low-level hardware abstractions with Rust's safety, this engine achieves near-theoretical peak performance for matrix multiplication across multiple CPU architectures.

## 🚀 Technical Pillars

### 1. Advanced Memory Management & Huge Pages
To minimize the cost of virtual-to-physical address translation, the engine implements a custom `HugePageAllocator`:
- **TLB Optimization:** Uses `mmap` with `MAP_HUGETLB` to allocate 2MB or 1GB pages, drastically reducing TLB misses.
- **Cache-Line Alignment:** Enforces strict 64-byte alignment (`posix_memalign`) to ensure optimal SIMD load/store throughput and prevent false sharing.



### 2. Multi-Level Cache Pipelining (Tiling)
The implementation features a sophisticated 3-level blocking strategy tailored to the modern CPU cache hierarchy:
- **L1/L2/L3 Optimization:** Dynamically tiles matrices to fit within specific cache sizes ($L1_{48KB}, L2_{1.25MB}, L3_{60MB}$), minimizing main memory stalls.
- **NUMA-Aware Scheduling:** Integrated with `hwloc` and `rayon` to ensure thread affinity and data locality across multiple CPU sockets.



### 3. Architecture-Specific Micro-Kernels
The engine features a high-performance `MicroKernel` trait with specialized implementations for:
- **x86_64 (AVX-512):** Hand-rolled Assembly using 512-bit ZMM registers and `vfmadd231ps` instructions.
- **AArch64 (NEON):** Vectorized kernels using 128-bit NEON intrinsics.
- **Apple Silicon (AMX):** Specialized orchestration for Apple's proprietary Matrix Coprocessor via `vDSP`.



## 🛠 Project Structure

- **`HugePageAllocator`**: Low-level systems programming for memory mapping.
- **`Architecture`**: Runtime hardware feature detection.
- **`GEMMExecutor`**: The main orchestrator managing the 6-loop nesting and parallel tiling.
- **`AlignedBuffer`**: A safety wrapper around raw pointers for aligned memory.

## 🔧 Installation & Usage

### Requirements
- **Rust Nightly:** Required for `asm_const`, `stdsimd`, and `portable_simd`.
- **Operating System:** Linux (for Huge Pages support) or macOS (for Apple AMX).

### Building the Project
```bash
cargo +nightly build --release
