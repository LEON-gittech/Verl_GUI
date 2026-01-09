<p align="center">
<h1 align="center">
  <img src="https://leon-gittech.github.io/Verl_GUI/icon.png" alt="BEPA" height="40"> BEPA
  <br>
  <span style="font-size:22px;">From Off-Policy to On-Policy: Enhancing GUI Agents via Bi-level Expert-to-Policy Assimilation</span>
</h1>
</p>

<p align="center">
  <b>Zezhou Wang<sup>1</sup></b>, <b>Ziyun Zhang<sup>2</sup></b>, <b>Xiaoyi Zhang<sup>3</sup></b>, <b>Zhuzhong Qian<sup>1</sup></b>, <b>Yan Lu<sup>3</sup></b>
  <br>
  <sup>1</sup>Nanjing University, <sup>2</sup>Peking University, <sup>3</sup>Microsoft Research Asia
</p>

<p align="center">
&nbsp;&nbsp;🌐 <a href="">Website (coming soon)</a>&nbsp;&nbsp; | &nbsp;&nbsp;📑 <a href="">arXiv (coming soon)</a>&nbsp;&nbsp; | &nbsp;&nbsp;🤖 <a href="https://huggingface.co/LEONW24/BEPA-7B-S2">Model</a>&nbsp;&nbsp; | &nbsp;&nbsp;🤗 <a href="">Dataset (coming soon)</a>&nbsp;&nbsp;
</p>

<p align="center">
  <img src="https://leon-gittech.github.io/Verl_GUI/stats/overview.png" alt="BEPA Overview" width="90%">
</p>

## 📢 Updates
- 2026-01: We release the webpage and model [BEPA-7B-S2](https://huggingface.co/LEONW24/BEPA-7B-S2). Check it out!

## 📖 TL;DR

We propose **BEPA** (Bi-Level Expert-to-Policy Assimilation), a framework that turns static expert traces into dynamic, policy-aligned guidance for GUI agents. BEPA improves UITARS1.5-7B from **22.87%** to **32.13%** on OSWorld-Verified (+9.26 points).

## 🔍 Introduction

Vision-language models are increasingly deployed as **computer-use agents (CUAs)** that operate desktops and browsers. Top-performing CUAs are framework-based systems that decompose planning and execution, while end-to-end screenshot-to-action policies are easier to deploy but lag behind on benchmarks such as OSWorld-Verified.

We ask: *How can reinforcement learning from verifiable rewards (RLVR) best exploit a small pool of expert trajectories to train end-to-end policies?*

Naively mixing these off-policy traces into on-policy RLVR is brittle due to:
- **Structural Mismatch:** Framework traces interleave multiple roles (planning, execution, grounding) that end-to-end policies cannot directly imitate.
- **Distribution Gap:** Even after format conversion, trajectories remain far from the base-policy manifold.

<p align="center">
  <img src="https://leon-gittech.github.io/Verl_GUI/stats/distribution_bias.png" alt="Distribution Bias" width="90%">
</p>

## 🚀 BEPA: Bi-Level Expert-to-Policy Assimilation

BEPA operates in two complementary stages:

### LEVEL-1: Self-Rolled Execution
Transforms alien expert traces into policy-compatible trajectories. We abstract the expert trajectory into a compact natural-language plan, then let the base policy act in the environment with plan conditioning. This produces trajectories that lie much closer to the policy's manifold.

### LEVEL-2: Self-Aligned Assimilation
Dynamically maintains a per-task cache, injecting guided trajectories into GRPO updates only upon *total on-policy failure*. The cache is continuously refreshed with the policy's own successful executions, ensuring the off-policy signal evolves alongside the agent.

## 📊 Main Results

BEPA achieves **32.13%** success on OSWorld-Verified, improving over UITARS1.5-7B (22.87%) by **+9.26 points (+40.5% relative)** and over GRPO (23.60%) by **+8.53 points (+36.1% relative)**.

| Method | D<sub>expert_only</sub> | D<sub>train</sub> | D<sub>held_out</sub> | Overall (%) |
|--------|-------------------------|-------------------|----------------------|-------------|
| UITARS1.5-7B | 18.52 | 55.12 | 5.74 | 22.87 |
| GRPO | 11.11 | 58.02 | 5.32 | 23.60 |
| Trace Replacement | 18.52 | 66.50 | 1.29 | 23.91 |
| LUFFY | 19.01 | 65.44 | 2.16 | 24.11 |
| LEVEL-1 | 25.93 | 69.20 | 5.05 | 27.30 |
| LEVEL-2 | 29.18 | 71.65 | 7.48 | 29.74 |
| **BEPA (ours)** | **35.19** | **73.23** | **10.30** | **32.13** |

## 🛠️ Verl-GUI: Training Framework

As part of this work, we release **Verl-GUI**, a highly scalable distributed training framework for long-horizon, multi-turn vision-language GUI agent training built upon veRL.

<p align="center">
  <img src="https://leon-gittech.github.io/Verl_GUI/stats/verl_gui_architecture.png" alt="Verl-GUI Architecture" width="90%">
</p>

### Key Features

- **Heterogeneous Cluster Architecture:** Completely separates trainer and rollout into independent Ray clusters, enabling deployment across heterogeneous compute resources (IB/NVLink nodes for training, PCIe nodes for rollout).

- **Multiple Storage Backends:** Supports Azure Blob Storage, NAS, and local filesystems through a unified abstraction layer.

- **Async Task Queue:** Dynamically maintains a task queue for the rollout cluster to consume, enabling decoupled and non-blocking task processing.

- **K-round Rollout Processing:** Intelligently splits batches across multiple rounds when trainer's global batch size exceeds rollout cluster capacity.

- **Scalable Parallel Environments:** Number of concurrent environments scales with rollout cluster compute capacity, with Ray-based orchestration and automatic Docker cleanup.

- **Service-oriented Orchestration:** Modular components including CheckpointManager, EnvWorkerPool, RolloutService, and ValidationAdapter.

## 📝 Citation

```bibtex
@article{wang2026bepa,
  title = {From Off-Policy to On-Policy: Enhancing GUI Agents via Bi-level Expert-to-Policy Assimilation},
  author = {Wang, Zezhou and Zhang, Ziyun and Zhang, Xiaoyi and Qian, Zhuzhong and Lu, Yan},
  journal = {arXiv preprint},
  year = {2026}
}
```

## 📄 License

This project is released under the [MIT License](LICENSE).

## 🙏 Acknowledgements

We thank the following open-source projects for making this work possible:

- [verl](https://github.com/volcengine/verl) for the excellent RL framework.
- [vLLM](https://github.com/vllm-project/vllm) for the fast inference engine.
- [OSWorld](https://github.com/xlang-ai/OSWorld) for the GUI agent benchmark.
