# PRD.md:  "The Why"; Product requirements, benchmark metadata table, high-level requirements, etc.

Product Requirements Document

"The Why"; Product requirements, benchmark metadata table, high-level requirements, etc. Defines the benchmark goal, validation objective, test name, benchmark number, category, and high-level success criteria.

## Benchmark Matrix Document Metadata (via benchmark_specification.json)

This PRD.md section is populated from benchmark_specification.json, which is the structured source of benchmark-specific product requirements.

## Workload Number
414

## Workload Name
CUDA Memcpy Bandwidth Test (H2D, D2H, D2D)

## Execution Summary (Run and Measure)
Compile src/cuda_memcpy.cu with nvcc and run H2D, D2H, D2D, and H2D_pageable at each geometric transfer size from min_bytes to max_bytes, parse bandwidth and H2D latency, and record pinned/pageable as the H2D ratio, to measure copy bandwidth. yaml direction and pinned_memory do not restrict which copies run

## Main Goal
Measure H2D/D2H/D2D transfer bandwidth

## Validation Objective
Validates positive bandwidth for every swept size. Metric #5 is a pinned/pageable ratio

## Workload Category
Memory, Bandwidth & Data Movement

## Validation Requirement

The benchmark must include an automated SQLite-integrated validation layer that verifies persisted results from `results/benchmark.db`. Validation must confirm:

1. The benchmark run completed successfully with no tool errors.
2. Required samples and aggregate metrics were persisted for every swept shape.
3. Metrics are finite and physically sensible (positive, within plausible bounds).
4. Measured values satisfy configured thresholds when the workload defines pass/fail gates.
5. The benchmark fails validation when required data is missing, invalid, or outside bounds.

## Non-Functional Requirements

| Requirement | Target |
|---|---|
| Automation | Runs to completion without manual intervention after `bash run_benchmark.sh` |
| Idempotency | Re-running `run_benchmark.sh` appends a new run; never corrupts existing rows |
| Persistence | All metrics survive script exit; `results/benchmark.db` is the durable record |
