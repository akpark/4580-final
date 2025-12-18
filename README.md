# ORIE 4580/5580 Course Project: LLM Serving System Simulation

## Overview

This project implements a stochastic discrete-event simulation to study the performance of Large Language Model (LLM) serving systems. The simulation models GPU-based query processing with two distinct phases (prefill and decode), batching strategies, and different scheduling policies.

## Team Members

- Amanda Park
- Hannah Canales
- Alexander Chan
- Sukrit Sharma

## File Structure

```
submission/
├── README.md                 # This file
├── main_report.ipynb         # Main analysis and findings
├── simulation.py             # Core simulation code
└── data/
    ├── baseline_results.csv      # Baseline scheduler comparison
    ├── arrival_sensitivity.csv   # Arrival rate experiments
    ├── prompt_sensitivity.csv    # Prompt length experiments
    └── output_sensitivity.csv    # Output budget experiments
```

## Requirements

- Python 3.9+
- Required packages:
  ```
  numpy >= 1.24.0
  matplotlib >= 3.7.0
  scipy >= 1.10.0
  ```

## Installation

```bash
pip install numpy matplotlib scipy
```

## How to Run

1. **Clone or extract** the submission folder to your local machine.

2. **Open the notebook**:
   ```bash
   jupyter notebook main_report.ipynb
   ```

3. **Run all cells** in order (Cell → Run All).

The notebook imports functions from `simulation.py`. Precomputed results are available in `data/` for reference.

**Note:** Full execution takes approximately 15-20 minutes due to long simulation runs (12,000 seconds simulated time per configuration, 5 replications each).

## Key Parameters

| Parameter | Default Value | Description |
|-----------|---------------|-------------|
| `ARRIVAL_RATE` | 1.0 q/s | Query arrival rate (λ) |
| `PROMPT_LENGTH` | 32 tokens | Tokens per prompt (L) |
| `OUTPUT_BUDGET` | 8 tokens | Output tokens per query (B) |
| `MAX_BATCH_SIZE` | 64 tokens | Maximum batch size (K) |
| `C_SETUP` | 30 ms | Fixed setup cost per batch (c) |
| `A_TOKEN` | 0.25 ms/token | Marginal cost per token (a) |
| `B0_THRESHOLD` | 16 tokens | Batch threshold (b₀) |
| `SIM_DURATION` | 12,000,000 ms | Total simulation time |
| `WARMUP_TIME` | 2,000,000 ms | Warmup period (discarded) |

## Simulation Components

### Scheduling Policies

1. **Decode-First (Baseline)**: Processes each query to completion before admitting the next. Simple FCFS approach with no batching across queries.

2. **Prefill-First**: Prioritizes new query prefills while batching decode tokens from multiple active queries. Improves throughput through better GPU utilization.

### Metrics Collected

- **TTFT (Time To First Token)**: Delay from query arrival to first output token
- **TBT (Time Between Tokens)**: Time between successive output tokens
- **Throughput**: Completed queries per second
- **P95 Latency**: 95th percentile latency values

## Validation

The simulation is validated against M/M/1 queue theory by setting:
- `C_SETUP = 0` (no setup cost)
- `B0_THRESHOLD = 0` (linear service time)
- `PROMPT_LENGTH = 1` (single token)
- `OUTPUT_BUDGET = 0` (no decode phase)

Under these conditions, simulated mean sojourn times match theoretical predictions.

## Random Seeds

For reproducibility, the following seeds are used:
- Primary runs: `42`
- Replications: `42, 123, 456, 789, 1011`

## References

- Agarwal et al.
- Li et al.
- Bari et al.

See course project description for full citations.
