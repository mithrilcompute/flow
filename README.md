# Mithril Flow Compute

**Python → Petaflops in 15 seconds.**
Flow procures GPUs through Mithril, spins InfiniBand-connected instances, and runs your workloads—zero friction, no hassle.

## Background

> **There's a paradox in GPU infrastructure today:**
> Massive GPU capacity sits idle, even as AI teams wait in queues—starved for compute.
> Mithril, the AI-compute **omnicloud**, dynamically allocates GPU resources from a global pool (spanning Mithril's first-party resources and 3rd-party partner cloud capacity) using efficient two-sided auctions, maximizing surplus and reducing costs. Mithril seamlessly supports both reserved-in-advance and just-in-time workloads—maximizing utilization, ensuring availability, and significantly reducing costs.

```bash
flow run "python train.py" # -i 8xh100
⠋ Bidding for best‑price GPU node (8×H100) with $12.29/h100-hr limit_price…
✓ Launching on NVIDIA H100-80GB for $1/h100-hr
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
| **Iteration Velocity and Ease**                     | Fresh containers in **seconds**; from idea to training or serving instantly. | `flow dev` for DevBox or `flow run` to programmatically launch tasks                 |
| **Best price-performance via market-based pricing** | Preemptible secure jobs for **\$1/h100-hr**                                  | Blind two-sided second-price auction; client-side bid capping                        |
| **Availability and Elasticity**                     | GPUs always available, self-serve; no haggling, no calls.                    | Uncapped spot + overflow capacity from partner clouds                                |
| **Abstraction and Simplification**                  | InfiniBand VMs, CUDA drivers, auto-managed healing buffer—all pre-arranged.  | Mithril virtualization and base images preconfigured + Mithril capacity management.  |

> *"The tremendous demand for AI compute and the large fraction of idle time makes sharing a perfect solution, and Mithril's innovative market is the right approach."* — **Paul Milgrom**, Nobel Laureate (Auction Theory and Mechanism Design)

---

## Pricing & Auctions

**How Flow leverages Mithril's Second-Price Auction:**

You express your limit price (or leverage flow defaults); GPUs provision instantly at the fair market clearing rate.

| Your Bid's Limit Price | Current Spot Price | You Pay                                                      |
| ----------- | -------------- | ------------------------------------------------------------ |
| \$3.00      | \$1.00         | \$1.00                                                       |
| \$3.00      | \$3.50 (spike) | No [allocation](https://docs.mithril.ai/compute-and-storage/spot-bids#spot-auction-mechanics) |


* Your billing price = highest losing bid.
* Limit price protects from surprises.
* Resell unused reservations into the auction to recoup costs.


[Full Auction Mechanics →](https://docs.mithril.ai/compute-and-storage/spot-bids#spot-auction-mechanics)

---

## Quick Start

Get an API key → [app.mithril.ai](https://app.mithril.ai/account/apikeys)

```bash
# Optional: install uv
curl -LsSf https://astral.sh/uv/install.sh | sh

# Install via uv or pipx (or see installer scripts in scripts/)
uv tool install flow-compute
# or: pipx install flow-compute

flow init  # Sets up your authentication and configuration
flow dev   # Interactive box. sub-5-second dev loop after initial VM config
``` 
---

## Key Concepts to Get Started

### Auctions & Limit Prices

Flow uses Mithril spot instances via second-price auctions. [See auction mechanics](https://docs.mithril.ai/compute-and-storage/spot-bids#spot-auction-mechanics).

### Core Workflows

* `flow dev` → interactive loops in seconds.
* `flow run` → reproducible batch jobs.
* `flow grab` → instant GPU cluster (e.g., `flow grab 256`)
* Python API → easy pipelines and orchestration.

### Examples

```bash
# Grab a micro-cluster instantly  
flow grab 256  # optionally name it: -n micro-cluster

# Launch a batch job on discounted H100s
flow run "python train.py" -i 8xh100

# Frictionlessly leverage an existing SLURM script
flow run job.slurm

# Serverless‑style decorator
@flow.function(gpu="a100")
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

### 1) Install uv (once) — optional but recommended

- macOS/Linux:
  ```bash
  curl -LsSf https://astral.sh/uv/install.sh | sh
  ```
- Windows (PowerShell):
  ```powershell
  powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
  ```
- More options: see the uv installation guide: [docs.astral.sh/uv/getting-started/installation](https://docs.astral.sh/uv/getting-started/installation/)

Note: Ensure the directory uv adds (often `~/.local/bin`) is on your PATH.

### 2) Install Flow

- Global CLI (uv):
  ```bash
  uv tool install flow-compute
  flow init
  ```

- Global CLI (pipx):
  ```bash
  pipx install flow-compute
  flow init
  ```

- One-liner (macOS/Linux) without uv/pipx:
  ```bash
  curl -fsSL https://raw.githubusercontent.com/mithrilcompute/flow/main/scripts/install.sh | sh
  flow init
  ```

- Per‑project (uv-managed env):
  ```bash
  uv init my-project
  cd my-project
  uv add flow-compute
  uv sync
  uv run flow init
  ```

After installation:
```bash
flow --version          # Verify installation
flow example gpu-test   # Test GPU access
flow dev                # Launch interactive DevBox
```

### Updating

```bash
flow update            # Auto-updates based on installation method
flow update --check    # Check for available updates
flow update --json     # Machine-readable output (CI)
```

**Note:** If you encounter version conflicts, run `which -a flow` to check for multiple installations.

---

## Under the Hood (Advanced)

* **Bid Caps** – Protect budgets automatically.
* **Self-Healing** – Spot nodes dynamically migrate tasks.
* **Docker/Conda** – Pre-built images or dynamic install.
* **Multi-cloud Ready** – Mithril (with Oracle, Nebius integrations internal to Mithril), and more coming
* **SLURM Compatible** – Run `#SBATCH` scripts directly.

---

## Developer Deep Dive

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

### SLURM Migration

Flow seamlessly runs existing SLURM scripts:

```bash
# Your existing script works unchanged
flow run job.slurm

# SLURM → Flow mapping:
# #SBATCH --gpus=8        → instance_type="8xa100"
# #SBATCH --time=24:00:00 → max_run_time_hours=24
# squeue                  → flow status
# scancel                 → flow cancel
```

### Zero-Import Remote Execution

Run existing Python functions on GPUs without code changes:

```python
# Execute any function from any file remotely
from flow import invoke

result = invoke(
    "src/train.py",           # Your existing file
    "train_model",            # Function name  
    args=["dataset.csv"],     # Arguments
    gpu="a100",               # GPU type
    code_root="src",          # Upload only this subtree (mapped to /workspace)
    image="python:3.11-slim"  # Ensure Python exists in the image
)
```

### Persistent Volumes & Docker Caching

```python
# Create reusable Docker cache (10x faster container starts)
cache = flow.create_volume(size_gb=100, name="docker-cache")

task = flow.run(
    "python train.py",
    instance_type="a100",
    image="pytorch/pytorch:2.3.0-cuda12.1-cudnn8",
    volumes=[{
        "volume_id": cache.volume_id,
        "mount_path": "/var/lib/docker"
    }]
)
# First run: ~5 min (downloads image)
# Next runs: ~30 sec (uses cache)
```

### Dynamic Volume Mounting

Attach persistent storage to launched tasks. If the task is still starting, the attachment is queued and completes automatically. In some cases a restart may be required for the mount to take effect.

```bash
# Mount by names:
flow mount training-data gpu-job-1

# Mount by IDs:
flow mount vol_abc123 task_xyz789

# Volume will be available at /volumes/training-data
```

### Key Features Summary

* **Distributed Training** – Multi-node InfiniBand clusters auto-configured
* **Code Upload** – Automatic with `.flowignore` support  
* **Container Environments** – Custom Docker images with caching (set `image="..."`)
* **Live Debugging** – SSH into running instances (`flow ssh`)
* **Cost Protection** – Built-in `max_price_per_hour` safeguards
* **Google Colab Integration** – Connect notebooks to GPU instances
* **Private Registries** – ECR/GCR with auto-authentication

**Repository**: https://github.com/mithrilcompute/flow

## Further Reading

* [Restoring the Promise of Public Cloud for AI](https://mithril.ai/blog/restoring-the-promise-of-the-public-cloud-for-ai)
* [Introducing Mithril](https://mithril.ai/blog/introducing-foundry)
* [Spot Auction Mechanics](https://docs.mithril.ai/compute-and-storage/spot-bids#spot-auction-mechanics)
