## Welcome

This repository is used **for tracking issues and feedback** related to the Flow project.  
No source code is stored here.

If you’ve encountered a bug, have a feature request, or want to share feedback, please open a new issue using the **Issues** tab or reach out at support@mithril.ai. Our team would love to hear from you.

# Flow CLI & SDK

![PyPI - Version](https://img.shields.io/pypi/v/flow-compute) ![Static Badge](https://img.shields.io/badge/Docs-blue?link=https%3A%2F%2Fdocs.mithril.ai%2Fcli-and-sdk%2Fquickstart)


**Python → Petaflops in 15 seconds.**
Flow procures GPUs through Mithril, spins InfiniBand-connected instances, and runs your workloads—zero friction, no hassle.

## Background

> **There's a paradox in GPU infrastructure today:**
> Massive GPU capacity sits idle, even as AI teams wait in queues—starved for compute.
> Mithril, the AI-compute **omnicloud**, dynamically allocates GPU resources from a global pool (spanning Mithril's first-party resources and 3rd-party partner cloud capacity) using efficient two-sided auctions, maximizing surplus and reducing costs. Mithril seamlessly supports both reserved-in-advance and just-in-time workloads—maximizing utilization, ensuring availability, and significantly reducing costs.

### Infrastructure mode
```bash
flow instance create -i 8xh100 -N 20
╭─ Instance Configuration ────────────────────────────────╮
│                                                         │
│  Name           multinode-run                           │
│  Command        sleep infinity                          │
│  Image          nvidia/cuda:12.1.0-runtime-ubuntu22.04  │
│  Working Dir    /workspace                              │
│  Instance Type  8xh100                                  │
│  Num Instances  20                                      │
│  Max price      $12.29/hr                               │
│                                                         │
╰─────────────────────────────────────────────────────────╯

flow instance list
╭─────────────────────────────── ❊ Flow ────────────────────────────────╮
│                                                                       │
│     #     Status     Instance               GPU       Owner     Age   │
│     1   ● running    multinode-run        8×H100·80G  alex       0m   │
│     2   ● running    interactive-77c31e   A100·80G    noam       5h   │
│     3   ○ cancelled  dev-a100-test        A100·80G    alex       1d   │
│                                                                       │
╰───────────────────────────────────────────────────────────────────────╯
```

### Research mode (in early preview)
```bash
flow submit "python train.py" # -i 8xh100
⠋ Bidding for best‑price GPU node (8×H100) with $12.29/h100-hr limit_price…
✓ Launching on NVIDIA H100-80GB for $1/h100-hr
```

---


## Quick Start

```bash
# Optional: install uv
curl -LsSf https://astral.sh/uv/install.sh | sh

# Install via uv or pipx (or see installer scripts in scripts/)
uv tool install flow-compute
# or: pipx install flow-compute

flow setup  # Sets up your authentication and configuration
flow dev   # Interactive box. sub-5-second dev loop after initial VM config
```

---

## Why choose Flow

Status quo GPU provisioning involves quotas, complex setups, and queue delays, even as GPUs sit idle elsewhere or in recovery processes. Flow addresses this:

**Dynamic Market Allocation** – Efficient two-sided auctions ensure you pay the lowest market-driven prices rather than inflated rates.

**Simplified Batch Execution** – An intuitive interface designed for cost-effective, high-performance batch workloads without complex infrastructure management.

Provision from 1 to thousands of GPUs for long-term reservations, short-term "micro-reservations" (minutes to weeks), or spot/on-demand needs—all interconnected via InfiniBand. High-performance persistent storage and built-in Docker support further streamline workloads, ensuring rapid data access and reproducibility.

---

## Why Flow + Mithril?

| Pillar                                              | Outcome                                                                      | How                                                                                  |
| --------------------------------------------------- | ---------------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| **Iteration Velocity and Ease**                     | Fresh containers in **seconds**; from idea to training or serving instantly. | `flow dev` for DevBox or `flow submit` to programmatically launch tasks                 |
| **Best price-performance via market-based pricing** | Preemptible secure jobs for **\$1/h100-hr**                                  | Blind two-sided second-price auction; client-side bid capping                        |
| **Availability and Elasticity**                     | GPUs always available, self-serve; no haggling, no calls.                    | Uncapped spot + overflow capacity from partner clouds                                |
| **Abstraction and Simplification**                  | InfiniBand VMs, CUDA drivers, auto-managed healing buffer—all pre-arranged.  | Mithril virtualization and base images preconfigured + Mithril capacity management.  |

> *"The tremendous demand for AI compute and the large fraction of idle time makes sharing a perfect solution, and Mithril's innovative market is the right approach."* — **Paul Milgrom**, Nobel Laureate (Auction Theory and Mechanism Design)

---

## Key Concepts to Get Started

### Core Workflows
Infrastructure mode
* `flow instance create -i 8xh100 -N 20` → spin up a 20-node GPU cluster in seconds
* `flow volume create -s 10000 -i file` → provision 10 TB of persistent, high-speed storage
* `flow ssh instance -- nvidia-smi` → run across all nodes in parallel

## In research preview

### Research mode
* `flow dev` → interactive loops in seconds.
* `flow submit` → reproducible batch jobs.
* Python API → easy pipelines and orchestration.

### Examples
```bash
# Launch a batch job on discounted H100s
flow submit "python train.py" -i 8xh100

# Frictionlessly leverage an existing SLURM script
flow submit job.slurm

# Serverless‑style decorator
@app.function(gpu="a100")
```

---

## Ideal Use Cases

* **Rapid Experimentation** – Quick iterations for research sprints.
* **Instant Elasticity** – Scale rapidly from one to thousands of GPUs.
* **Collaborative Research** – Shared dev environments with per-task cost controls.

Flow is not yet ideal for: always‑on ≤100 ms inference, strictly on‑prem regulated data, or models that fit on laptop or consumer-grade GPUs.

---

## Architecture (30‑s view)

```
Your intent ⟶ Flow Execution Layer ⟶ Global GPU Fabric
```

*Flow SDK abstracts complex GPU auctions, InfiniBand clusters, and multi-cloud management into a single seamless and unified developer interface.*

---

## Installation

### Requirements

- Python 3.10 or later
- Recommended: use `uv` to auto-manage a compatible Python when installing the CLI

### 1) Install uv — optional but recommended
Installation guide: [docs.astral.sh/uv/getting-started/installation](https://docs.astral.sh/uv/getting-started/installation/)

- macOS/Linux:
  ```bash
  curl -LsSf https://astral.sh/uv/install.sh | sh
  ```
- Windows (PowerShell):
  ```powershell
  powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
  ```

### 2) Install Flow

- Global CLI (uv):
  ```bash
  uv tool install flow-compute
  flow setup
  ```

- Global CLI (pipx):
  ```bash
  pipx install flow-compute
  flow setup
  ```

---

## Under the Hood (Advanced)

* **Bid Caps** – Protect budgets automatically.
* **Self-Healing** – Spot nodes dynamically migrate tasks.
* **Docker/Conda** – Pre-built images or dynamic install.
* **Multi-cloud Ready** – Mithril (with Oracle, Nebius integrations internal to Mithril), and more coming
* **SLURM Compatible** – Run `#SBATCH` scripts directly.

---

## Python SDK (Research Preview)

### Advanced Task Configuration

```python
# Distributed training example (32 GPUs, Mithril groups for InfiniBand connectivity by default)
task = flow.run(
    command="torchrun --nproc_per_node=8 train.py",
    instance_type="8xa100",
    num_instances=4,  # Total of 32 GPUs (4 nodes × 8 GPUs each)
    env={"NCCL_DEBUG": "INFO"}
)

# Mount S3 data + persistent volumes
task = flow.run(
    "python analyze.py",
    gpu="a100",
    mounts={
        "/datasets": "s3://ml-bucket/imagenet",  # S3 via s3fs
        "/models": "volume://pretrained-models"   # Persistent storage
    }
)
```

### Key Features Summary

* **Distributed Training** – Multi-node InfiniBand clusters auto-configured
* **Code Upload** – Automatic with `.flowignore` (or `.gitignore` fallback)  
* **Live Debugging** – SSH into running instances (`flow ssh`)
* **Cost Protection** – Built-in `max_price_per_hour` safeguards
* **Jupyter Integration** – Connect notebooks to GPU instances

**Documentation**: https://docs.mithril.ai/cli-and-sdk/quickstart

## Further Reading

* [Restoring the Promise of Public Cloud for AI](https://mithril.ai/blog/restoring-the-promise-of-the-public-cloud-for-ai)
* [Introducing Mithril](https://mithril.ai/blog/introducing-foundry)
* [Spot Auction Mechanics](https://docs.mithril.ai/compute-and-storage/spot-bids#spot-auction-mechanics)
