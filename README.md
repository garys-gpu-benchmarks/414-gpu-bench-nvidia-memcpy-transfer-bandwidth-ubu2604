# CUDA Memcpy Bandwidth Test (H2D, D2H, D2D) Benchmark

[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](LICENSE)
[![CI](https://github.com/garys-gpu-benchmarks/414-gpu-bench-nvidia-memcpy-transfer-bandwidth-ubu2604/actions/workflows/ci.yml/badge.svg)](https://github.com/garys-gpu-benchmarks/414-gpu-bench-nvidia-memcpy-transfer-bandwidth-ubu2604/actions/workflows/ci.yml)

Target: Ubuntu 26.04 · NVIDIA · see Hardware Requirements. This is a host benchmark, not a laptop `pip install` project.

## Quick Start

```bash
git clone https://github.com/garys-gpu-benchmarks/414-gpu-bench-nvidia-memcpy-transfer-bandwidth-ubu2604.git
cd 414-gpu-bench-nvidia-memcpy-transfer-bandwidth-ubu2604
sudo bash setup.sh --assume-yes
bash run_benchmark.sh --profile smoke --validate
```
Results are written to `results/benchmark.db` and `results/summary.json`.

This workload is executed on the validation host after the repository is copied there. `setup.sh` and `run_benchmark.sh` do not open an outbound SSH session.

Prerequisites: Ubuntu 26.04; NVIDIA; Python 3.14.4; root or sudo for `setup.sh`. Framework: Bash, SQLite, Python, PyYAML, CUDA Runtime, NVCC. This is a host benchmark, not a laptop `pip install` project.

```mermaid
flowchart LR
  setup.sh --> run_benchmark.sh --> parse_results.py --> results/benchmark.db
```

## 1. Overview

Builds src/cuda_memcpy.cu and sweeps transfer sizes from min_bytes to max_bytes by step_factor. Each size always runs H2D, D2H, D2D, and H2D_pageable. device_id: GPU index. warmup_iters and num_iterations time cudaMemcpy with CUDA events. Sweep dimensions: device_id, direction, pinned_memory, min_bytes, max_bytes, step_factor, warmup_iters, num_iterations.

## 2. What It Validates

- Validates positive bandwidth for every swept size. Metric #5 is a pinned/pageable ratio
- #1: H2D bandwidth at max transfer size, GB/s (bandwidth_h2d_gbps); is present and physically sensible.
- #2: D2H bandwidth at max transfer size, GB/s (bandwidth_d2h_gbps); is present and physically sensible.
- #3: D2D bandwidth at max transfer size, GB/s (bandwidth_d2d_gbps); is present and physically sensible.
- #4: H2D latency at min transfer size, us (latency_h2d_us); is present and physically sensible.
- #5: D2H latency at min transfer size, us (latency_d2h_us); is present and physically sensible.
- #6: D2D latency at min transfer size, us (latency_d2d_us); is present and physically sensible.
- #7: Pageable ratio (pinned_vs_pageable_bandwidth_ratio) is present and physically sensible.

## 3. Metrics Captured

- **#1: H2D bandwidth at max transfer size, GB/s** — stored as `bandwidth_h2d_gbps`.
- **#2: D2H bandwidth at max transfer size, GB/s** — stored as `bandwidth_d2h_gbps`.
- **#3: D2D bandwidth at max transfer size, GB/s** — stored as `bandwidth_d2d_gbps`.
- **#4: H2D latency at min transfer size, us** — stored as `latency_h2d_us`.
- **#5: D2H latency at min transfer size, us** — stored as `latency_d2h_us`.
- **#6: D2D latency at min transfer size, us** — stored as `latency_d2d_us`.
- **#7: Pageable ratio** — stored as `pinned_vs_pageable_bandwidth_ratio`.

## 4. Hardware Requirements

### Supported environment

- OS: Ubuntu 26.04
- GPU vendor: NVIDIA
- Framework family: Bash, SQLite, Python, PyYAML, CUDA Runtime, NVCC
- Python: Python 3.14.4

### Reference validation environment

The tables below describe the machine used to generate the reference results. They are not a requirement that every user buy that exact cloud instance.

### System

Builds src/cuda_memcpy.cu and sweeps transfer sizes from min_bytes to max_bytes by step_factor. Each size always runs H2D, D2H, D2D, and H2D_pageable. device_id: GPU index.

### GPU

Ubuntu 26.04 / NVIDIA / Bash, SQLite, Python, PyYAML, CUDA Runtime, NVCC

## 5. Software Requirements

| Component | Version |
|---|---|
| OS | Ubuntu 26.04 |
| Kernel | kernel 7.0.0 |
| Python | Python 3.14.4 |
| ROCm | CUDA 13.3 |
| rocBLAS | N/A - rocBLAS not used |

Builds src/cuda_memcpy.cu and sweeps transfer sizes from min_bytes to max_bytes by step_factor. Each size always runs H2D, D2H, D2D, and H2D_pageable. device_id: GPU index.

## 6. Installation

```bash
Compile cuda_memcpy.cu with nvcc; run bin/cuda_memcpy for H2D, D2H, D2D, and H2D_pageable
```

## 7. Running the Benchmark

```bash
Compile cuda_memcpy.cu with nvcc; run bin/cuda_memcpy for H2D, D2H, D2D, and H2D_pageable
```

**Validating results separately:**

```bash
export BENCHMARK_PYTHON=/usr/bin/python3.13  # optional
python3 -m venv .venv
source ".venv/bin/activate"
".venv/bin/python" scripts/validate_results.py
```

## 8. Output

### `results/benchmark.db` (SQLite)

CSV with H2D, D2H, and D2D bandwidth and latency on each transfer-size row

sample_index,status,transfer_size_bytes,bandwidth_h2d_gbps,bandwidth_d2h_gbps,bandwidth_d2d_gbps,latency_h2d_us,latency_d2h_us,latency_d2d_us,pinned_vs_pageable_host_memory_bandwidth_gb_s,pinned_vs_pageable_bandwidth_ratio,error_message
0,ok,268435456,25,24,700,8,9,2,20,1.4,

```bash
Compile cuda_memcpy.cu with nvcc; run bin/cuda_memcpy for H2D, D2H, D2D, and H2D_pageable
```

### `results/summary.json`

Consolidated metrics from the most recent run — suitable for CI artifact upload or dashboard ingestion.

### `results/raw/<timestamp>.txt`

CSV with H2D, D2H, and D2D bandwidth and latency on each transfer-size row

sample_index,status,transfer_size_bytes,bandwidth_h2d_gbps,bandwidth_d2h_gbps,bandwidth_d2d_gbps,latency_h2d_us,latency_d2h_us,latency_d2d_us,pinned_vs_pageable_host_memory_bandwidth_gb_s,pinned_vs_pageable_bandwidth_ratio,error_message
0,ok,268435456,25,24,700,8,9,2,20,1.4,

## 9. Baselines / Thresholds

Expected ranges and gates live in `config/benchmark_config.yaml` under `baselines:` or `thresholds:`. To update them, edit that file — never edit validation code directly.

## 10. Troubleshooting

**`setup.sh` missing collector**
Create cannot finish without `scripts/collect_workload.py`.

**`self_check` overlay rewritten**
Do not overwrite files listed in `results/overlay_lock.json`.

**Remote SSH drop during setup**
Reconnect and resume `bash setup.sh --assume-yes`. Do not wipe `.venv` or `.cache`.

## 11. NVIDIA H100 Coding Differences

Native NVIDIA CUDA workload. Execute on the stated Ubuntu release with the host NVIDIA driver and CUDA userspace. ROCm porting notes do not apply.

## Repository layout

```text
.
├── setup.sh
├── run_benchmark.sh
├── benchmark_specification.json
├── .github/workflows/      # thin CI callers (see Continuous Integration)
├── config/
├── scripts/
├── src/
├── tests/
├── docs/
├── results/
└── LICENSE
```

## Continuous Integration

| Workflow | Runs on | When | What it does |
|---|---|---|---|
| [CI](.github/workflows/ci.yml) | GitHub-hosted runner | every pull request, and every push to `main` | shellcheck, ruff, `bash -n`, `compileall`, `run_benchmark.sh --help`, specification schema, the results validator on a seeded fixture, required files, and actionlint. No GPU and no benchmark run. |
| [GPU Smoke Benchmark](.github/workflows/gpu-smoke.yml) | self-hosted runner labeled `gpu`, `nvidia`, `ubu2604` | only when started by hand: **Actions → GPU Smoke Benchmark → Run workflow** (choose `smoke`, `baseline` or `extended`) | Verifies the pre-provisioned GPU stack, records `results/environment.json` (driver, runtime, kernel, GPU), runs the profile with `--validate`, shows headline metrics on the run page, and uploads the results. |

Both files are short callers. The steps themselves live once, for every workload in the suite, in [`garys-gpu-benchmarks/shared-workflows`](https://github.com/garys-gpu-benchmarks/shared-workflows), pinned at `@v1`. The GPU workflow is never triggered by pull requests, so code from a fork cannot run on the GPU host.

### Running it as part of the NVIDIA Ubuntu 26.04 bundle

This repository is one of the 32 workloads in [`bundle-nvidia-ubuntu-2604`](https://github.com/garys-gpu-benchmarks/bundle-nvidia-ubuntu-2604), which holds them as git submodules. To put the whole bundle on a GPU host and run this workload from it:

```bash
git clone --recurse-submodules https://github.com/garys-gpu-benchmarks/bundle-nvidia-ubuntu-2604 /opt/benchmarks
cd /opt/benchmarks/414-gpu-bench-nvidia-memcpy-transfer-bandwidth-ubu2604
bash run_benchmark.sh --profile smoke --validate
```
