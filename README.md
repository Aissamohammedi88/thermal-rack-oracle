# thermal-rack-oracle
 **Thermal-Rack-Oracle** bridges low-level hardware telemetry with real-time operational physics: 1. **CUDA 2D/3D Transient Heat Diffusion Kernel** solving Fourier's law with dynamic thermal conductivity (\(k\)) across copper, aluminium, graphene and paste. 2. **Deterministic TCU (Thermal Control Unit) HAL**: Register-level mapping, MMIO access
# ⚡ Thermal-Rack-Oracle // Datacenter HPC & GPU Telemetry Twin

[![CUDA 12.x Ready](https://img.shields.io/badge/CUDA-12.x-76B900?style=for-the-badge&logo=nvidia&logoColor=white)](https://developer.nvidia.com/cuda-zone)
[![Rust](https://img.shields.io/badge/Rust-2024_Edition-DEA584?style=for-the-badge&logo=rust&logoColor=white)](https://www.rust-lang.org)
[![Zig](https://img.shields.io/badge/Zig-0.13-F7A41D?style=for-the-badge&logo=zig&logoColor=white)](https://ziglang.org)
[![Thermal Modeling](https://img.shields.io/badge/Physics-Fourier_Transient_Diffusion-00C7B7?style=for-the-badge)](#physics-engine)
[![NVIDIA Target](https://img.shields.io/badge/Hardware-NVL72_%7C_GB200_%7C_H100_SXM5-black?style=for-the-badge&logo=nvidia)](https://www.nvidia.com)

> **Real-time 3D Digital Twin, Microsecond TCU Telemetry & GPU Heat Dissipation Simulator designed for Megawatt-Scale AI Clusters (up to 130 kW/rack).**

---

## 🎯 Executive Overview

Modern AI clusters powered by dense GPU architectures (such as **NVIDIA GB200 NVL72** and **HGX H100/H200**) face unprecedented heat flux challenges:
- Thermal power densities exceeding **100 kW per rack**.
- Sub-second hot-spot emergence upon sustained GEMM / FP8 matrix workload spikes.
- Crucial dependency on direct liquid-to-chip (DLC), Cold Plates and CDU/CRAC failover resilience.

**Thermal-Rack-Oracle** bridges low-level hardware telemetry with real-time operational physics:
1. **CUDA 2D/3D Transient Heat Diffusion Kernel** solving Fourier's law with dynamic thermal conductivity (\(k\)) across copper, aluminium, graphene and paste.
2. **Deterministic TCU (Thermal Control Unit) HAL**: Register-level mapping, MMIO access via **Zig**, safety-critical daemon in **Rust**, and raw **C11 FFI**.
3. **Full Datacenter Digital Twin (48 racks, 42U each)**: Real-time gradient calculation, CRC32 hardware frame validation, and emergency thermal cut-off simulation.

---

## 🏛️ System Architecture

```text
       +-------------------------------------------------------+
       |   NOC / Operations Console (TanStack Start + WebGL)   |
       +-------------------------------------------------------+
                                  ▲
                    JSON-RPC / Zero-Copy RingBuffer
                                  ▼
       +-------------------------------------------------------+
       |          Rust Telemetry Daemon (TcuDriver)            |
       |       - Real-time CRC32 Frame Engine                  |
       |       - Hot-aisle dynamic thermal mitigation          |
       +-------------------------------------------------------+
               │                                       │
        C11 FFI Bindings                        Zig MMIO Driver
               │                                       │
               ▼                                       ▼
+-----------------------------+         +-----------------------------+
|    CUDA Accelerated PDE     |         |    TCU Hardware Registers   |
|   - __global__ Conduction   |         |   - 8 Architecture Specs    |🔬 Core Physics & Governing Equations



The solver computes thermal transient conduction across multi-layer server interfaces:




∂T∂t=α∇2T+q˙ρcp
∂t
∂T
	​

=α∇
2
T+
ρc
p
	​

q
˙
	​

	​





With one-dimensional steady-state heat flux across server chassis and TIM:




ΔT=q˙⋅Lk
ΔT=
k
q
˙
	​

⋅L
	​






q˙
q
˙
	​

: Heat flux density ($W/m^2$)


$k$: Thermal conductivity (
W/m⋅K
W/m⋅K) — evaluated dynamically:



Copper: 
401 W/m⋅K
401 W/m⋅K


Aluminium: 
205 W/m⋅K
205 W/m⋅K


Graphene heat spreader: 
2000 W/m⋅K
2000 W/m⋅K


High-performance TIM: 
8.5 W/m⋅K
8.5 W/m⋅K










⚙️ Hardware TCU Integration Matrix


Family ID	Target Architecture	Max Thermal TDP	Protocol	Register Range	Clock Freq
TCU-NV-900	Liquid-Cooled Blackwell / GB200	1200 W / compute tray	MMIO / PCIe Gen5	0x1000 - 0x1030	200 MHz
TCU-H100-X	Hopper SXM5 Baseboard	700 W / GPU	SMBus / I3C	0x2000 - 0x2028	100 MHz
TCU-GH200-S	Grace Hopper Superchip	900 W unified	Direct MMIO	0x3000 - 0x3040	250 MHz
TCU-RACK-42	Cold Plate Distribution Manifold	130 kW / rack	Industrial Modbus/IP	0x4000 - 0x4050	50 MHz




🚀 Quick Start (Local & HPC Workstation)


1. Prerequisites




Node.js 20+ or Bun 1.1+


(Optional for native HPC builds): nvcc (CUDA Toolkit 12.2+), cargo (Rust 1.78+), zig 0.13.0, gcc 11+




2. Frontend & Digital Twin Dashboard


# Clone the repository
git clone https://github.com/Aissamohammedi88/thermal-rack-oracle.git
cd thermal-rack-oracle

# Install dependencies and run dev server
bun install
bun dev




Navigate to http://localhost:8080.



3. Native Pipeline Compilation (Linux Mint / RHEL / Ubuntu)



A standalone build script is included in the project under the Intégration tab:



chmod +x scripts/build_and_launch.sh
./scripts/build_and_launch.sh




Compiles:





kernel.cu via nvcc -O3 -arch=sm_90


driver.rs via cargo build --release


tcu_mmio.zig via zig build-lib -dynamic


tcu_ffi.c via gcc -O3 -shared






🧪 Testing & Validation


# Run unit and physics integration test suites
bun test
# or with vitest
bunx vitest run




Validates:





Thermal delta conduction formulas across material boundaries.


CRC32 polynomial framing for TCU command validation.


Rejection of deliberately corrupted sensor payloads.




|   - nvcc FP32 / FP16 solver |         |   - Base 0x1000 - 0x1030    |
+-----------------------------+         +-----------------------------+
