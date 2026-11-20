# Results: 414 CUDA Memcpy Bandwidth Test (H2D, D2H, D2D)

**Main goal:** Measure H2D/D2H/D2D transfer bandwidth  
**Tools:** Compile cuda_memcpy.cu with nvcc; run bin/cuda_memcpy for H2D, D2H, D2D, and H2D_pageable

Reference results measured by the maintainer on the system shown in each section (generator TEMPLATE_00_149 · recorded 2026-11-19). Your own runs write `results/RESULTS_ACTUAL.md` in the same format.

> Smoke runs use deliberately small sizes. They check that the workload installs and runs end to end; their numbers are not performance results. Baseline and extended are.

<!-- profile:smoke -->
## smoke

- **System:** 1× NVIDIA H100 NVL (95830 MiB) · Intel(R) Xeon(R) Platinum 8452Y · Ubuntu 26.04.1 LTS · kernel 6.8.0-138-generic · driver 595.112.07
- **Run:** 2026-11-19T21:42:15Z · status **PASS** (exit 0)
- **Time (mm:ss):** setup 01:03 · benchmark 01:31 · whole run 02:41
- **Source:** run 414_20261119_214215
- **Command:** `bash run_benchmark.sh --smoke --device-id 0 --direction all --pinned-memory true --min-bytes 1024 --max-bytes 268435456 --step-factor 2 --warmup-iters 1 --num-iterations 10`

| # | Metric | Result |
|---|---|---|
| 1 | H2D bandwidth at max transfer size, GB/s (bandwidth_h2d_gb_s) | 42.103791 |
| 2 | D2H bandwidth at max transfer size, GB/s (bandwidth_d2h_gb_s) | 56.835542 |
| 3 | D2D bandwidth at max transfer size, GB/s (bandwidth_d2d_gb_s) | 1755.232718 |
| 4 | H2D latency at min transfer size, us (latency_h2d_us) | 14.4384 |
| 5 | D2H latency at min transfer size, us (latency_d2h_us) | 12.751999 |
| 6 | D2D latency at min transfer size, us (latency_d2d_us) | 11.0304 |
| 7 | Pinned-over-pageable H2D bandwidth ratio (pinned_vs_pageable_bandwidth_ratio) | 5.56599131228309 |
<!-- /profile:smoke -->
