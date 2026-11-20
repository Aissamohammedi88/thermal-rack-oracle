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
|   - __global__ Conduction   |         |   - 8 Architecture Specs    |
|   - nvcc FP32 / FP16 solver |         |   - Base 0x1000 - 0x1030    |
+-----------------------------+         +-----------------------------+
