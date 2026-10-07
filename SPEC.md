# SPEC.md: "The Human How"; Exact technical requirements, environment setup, implementation details, etc.

## Execution Description

Builds src/cuda_memcpy.cu and sweeps transfer sizes from min_bytes to max_bytes by step_factor. Each size always runs H2D, D2H, D2D, and H2D_pageable. device_id: GPU index. warmup_iters and num_iterations time cudaMemcpy with CUDA events. Sweep dimensions: device_id, direction, pinned_memory, min_bytes, max_bytes, step_factor, warmup_iters, num_iterations.

## Parameters

| Parameter | CLI Flag | Tested Values | Default | Description |
| --- | --- | --- | --- | --- |
| device_id | `--device-id` | smoke=0, baseline=0, extended=0 | 0 | From Parameter list; see Execution Description With Parameters. |
| direction | `--direction` | smoke=all, baseline=all, extended=all | all | From Parameter list; see Execution Description With Parameters. |
| pinned_memory | `--pinned-memory` | smoke=true, baseline=true, extended=true | true | From Parameter list; see Execution Description With Parameters. |
| min_bytes | `--min-bytes` | smoke=1024, baseline=1024, extended=1024 | 1024 | From Parameter list; see Execution Description With Parameters. |
| max_bytes | `--max-bytes` | smoke=4096, baseline=1073741824, extended=1073741824 | 1073741824 | From Parameter list; see Execution Description With Parameters. |
| step_factor | `--step-factor` | smoke=2, baseline=2, extended=2 | 2 | From Parameter list; see Execution Description With Parameters. |
| warmup_iters | `--warmup-iters` | smoke=1, baseline=5, extended=10 | 5 | From Parameter list; see Execution Description With Parameters. |
| num_iterations | `--num-iterations` | smoke=3, baseline=750, extended=2200 | 750 | From Parameter list; see Execution Description With Parameters. |

## Invocation

```bash
Compile cuda_memcpy.cu with nvcc; run bin/cuda_memcpy for H2D, D2H, D2D, and H2D_pageable
```

## Raw Output Format

CSV with H2D, D2H, and D2D bandwidth and latency on each transfer-size row

sample_index,status,transfer_size_bytes,bandwidth_h2d_gbps,bandwidth_d2h_gbps,bandwidth_d2d_gbps,latency_h2d_us,latency_d2h_us,latency_d2d_us,pinned_vs_pageable_host_memory_bandwidth_gb_s,pinned_vs_pageable_bandwidth_ratio,error_message
0,ok,268435456,25,24,700,8,9,2,20,1.4,

## Metrics

- **#1: H2D bandwidth at max transfer size, GB/s** — stored as `bandwidth_h2d_gbps`.
- **#2: D2H bandwidth at max transfer size, GB/s** — stored as `bandwidth_d2h_gbps`.
- **#3: D2D bandwidth at max transfer size, GB/s** — stored as `bandwidth_d2d_gbps`.
- **#4: H2D latency at min transfer size, us** — stored as `latency_h2d_us`.
- **#5: D2H latency at min transfer size, us** — stored as `latency_d2h_us`.
- **#6: D2D latency at min transfer size, us** — stored as `latency_d2d_us`.
- **#7: Pageable ratio** — stored as `pinned_vs_pageable_bandwidth_ratio`.

## Framework

Builds src/cuda_memcpy.cu and sweeps transfer sizes from min_bytes to max_bytes by step_factor. Each size always runs H2D, D2H, D2D, and H2D_pageable. device_id: GPU index.

## Installation and Execution Summary

Compile src/cuda_memcpy.cu with nvcc and run H2D, D2H, D2D, and H2D_pageable at each geometric transfer size from min_bytes to max_bytes, parse bandwidth and H2D latency, and record pinned/pageable as the H2D ratio, to measure copy bandwidth. yaml direction and pinned_memory do not restrict which copies run

## Platform Portability

- **AMD (primary):** ```bash
Compile cuda_memcpy.cu with nvcc; run bin/cuda_memcpy for H2D, D2H, D2D, and H2D_pageable
```
- **NVIDIA:** Native NVIDIA CUDA workload. Execute on the stated Ubuntu release with the host NVIDIA driver and CUDA userspace. ROCm porting notes do not apply.

## Model Context Protocols

- **Active:** None

## Execution-Loop Validation Contract

EXECUTION CHAIN: `run_benchmark.sh` ➔ raw output ➔ `scripts/parse_results.py` ➔ `results/benchmark.db` ➔ `scripts/validate_results.py`

This benchmark uses a lightweight, SQLite-integrated execution loop for result validation. All validation is performed by `scripts/validate_results.py`.

### Validation script usage

```bash
export BENCHMARK_PYTHON=/usr/bin/python3.13  # optional; select the installed interpreter

# After a live run:
".venv/bin/python" scripts/validate_results.py --db results/benchmark.db

# CI / no-GPU path (seeds fixture and validates it):
".venv/bin/python" scripts/validate_results.py --seed-fixture --quiet

# Override DB path via environment variable:
BENCHMARK_DB=tests/fixtures/benchmark.db \
  ".venv/bin/python" scripts/validate_results.py
```

### Run artifact contract

CSV with H2D, D2H, and D2D bandwidth and latency on each transfer-size row

sample_index,status,transfer_size_bytes,bandwidth_h2d_gbps,bandwidth_d2h_gbps,bandwidth_d2d_gbps,latency_h2d_us,latency_d2h_us,latency_d2d_us,pinned_vs_pageable_host_memory_bandwidth_gb_s,pinned_vs_pageable_bandwidth_ratio,error_message
0,ok,268435456,25,24,700,8,9,2,20,1.4,

```bash
bash run_benchmark.sh --help
bash run_benchmark.sh --profile smoke --validate
bash run_benchmark.sh --profile baseline --validate
bash run_benchmark.sh --profile extended --validate
```
`run_benchmark.sh --help` prints usage and exits. The harness calls `scripts/ensure_setup.sh` when `.setup_state` is absent.

### Required integrity checks (built into `validate_results.py`)

1. Latest run exists and `runs.status = 'ok'`.
2. `run.error_message` is NULL.
3. `started_at` and `finished_at` are valid ISO-8601 UTC strings.
4. All required aggregate metrics in `runs` are non-NULL and finite.
5. All required aggregate metrics are physically sensible (positive values). Builds src/cuda_memcpy.cu and sweeps transfer sizes from min_bytes to max_bytes by step_factor. Each size always runs H2D, D2H, D2D, and H2D_pageable. device_id: GPU index.
6. At least 2 sample rows exist for the latest `run_id` (sweep coverage).
7. No sample has `status = 'error'`.
8. Builds src/cuda_memcpy.cu and sweeps transfer sizes from min_bytes to max_bytes by step_factor. Each size always runs H2D, D2H, D2D, and H2D_pageable. device_id: GPU index.

### Baseline / Threshold configuration (`config/benchmark_config.yaml`)

Expected ranges and gates live in `config/benchmark_config.yaml` under `baselines:` or `thresholds:`. To update them, edit that file — never edit validation code directly.

Threshold key suffixes encode comparison direction when `thresholds:` is present: `_min` → observed value must be ≥ threshold. `_max` → observed value must be ≤ threshold. Informational `baselines:` ranges are not pass/fail gates.
