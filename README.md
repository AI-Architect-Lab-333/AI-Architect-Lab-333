# AI Architect Lab

Verified, reproducible guides from real infrastructure work. Every command published here was run for real, in order, on real machines — and the pitfall sections document what actually broke along the way, not what theoretically might.

Each guide states its verified environment (OS, versions, date) and ends with the end-to-end test that proves the setup works. They are written to be followed by **humans and AI agents alike** — the verification steps are never optional, because several failure modes in these setups are silent.

## Guides

### AI agents

- **[ai-agent-guardrails-windows-guide](https://github.com/AI-Architect-Lab-333/ai-agent-guardrails-windows-guide)** — a global guardrail that blocks catastrophic shell commands (`rm -rf /`, `git push --force`, disk wipes…) before any AI agent runs them, on Windows. Documents two pitfalls that silently disable the naive Unix recipe.
- **[pi-hermes-setup](https://github.com/AI-Architect-Lab-333/pi-hermes-setup)** — a controller/controlled agent pair: Pi Agent in Docker on Windows driving a Hermes agent on a remote VPS over SSH + tmux, with a Mixture of Agents preset.
- **[windows-tmux-agent-orchestration](https://github.com/AI-Architect-Lab-333/windows-tmux-agent-orchestration)** — driving several AI agents in parallel tmux panes on Windows (WSL2): spawn, send input, read output, poll. A Cmux-to-tmux port; documents the `docker-desktop`-default-distro and PowerShell→wsl→bash quoting traps.
- **[dsh-blind-circuit-windows](https://github.com/AI-Architect-Lab-333/dsh-blind-circuit-windows)** — DeepSeek Harness Web UI on Windows plus llama.cpp on a Tailscale-only GPU box: the coding agent starts the UI and probes model ids, but never opens the workspace. Six pitfalls, including why `Start-Process -WindowStyle Hidden` then `ERR_CONNECTION_REFUSED`.

### Evaluation

- **[eval-bench-frontier-witness](https://github.com/AI-Architect-Lab-333/eval-bench-frontier-witness)** — measuring what a local model **gives up** against a frontier one, on your own work: deterministic checks, no model-as-judge, and a frontier pass run **first** as a witness of the bench's own validity. That witness scored **69%** on the first pass — nothing was wrong with the model, six things were wrong with the bench. Twelve pitfalls, including a reasoning model that spends its entire token budget and returns zero characters, a brevity check an empty answer passes, and why five wrongly lenient checks survived three witness passes unseen. Ships the bench, its self-test, and the audit that finds leniency.
- **[bench-saturation-refusal](https://github.com/AI-Architect-Lab-333/bench-saturation-refusal)** — the companion problem: the bench is honest and still useless, because every model passes. How to detect saturation, how to write cases whose obvious answer is wrong, and how to measure refusal as a number instead of an anecdote. The case set was rebuilt four times harder and produced **zero wrong answers in 108 model-case pairs** — these models do not answer incorrectly on this material, they stop, having burned up to 62 500 tokens deciding to.

### Self-hosted infrastructure

- **[vps-tailscale-hardening-guide](https://github.com/AI-Architect-Lab-333/vps-tailscale-hardening-guide)** — locking down a VPS behind Tailscale until no port answers on the public IP. Covers the Docker-bypasses-UFW trap and the sshd config read-order trap.
- **[vps-tailscale-backup-pull-guide](https://github.com/AI-Architect-Lab-333/vps-tailscale-backup-pull-guide)** — nightly pull-architecture backups between two VPS over Tailscale: rsync, systemd timers, integrity checksums, retention, and a restore procedure. Production can never touch its own backups.
- **[tailscale-aperture-openrouter-gateway](https://github.com/AI-Architect-Lab-333/tailscale-aperture-openrouter-gateway)** — one identity-authenticated LLM gateway on your tailnet: the OpenRouter API key stays server-side and no device ever holds it, with per-user dollar quotas. Documents the non-existent-model-ID and silent empty-reasoning-response traps.
- **[dgx-spark-headless-setup](https://github.com/AI-Architect-Lab-333/dgx-spark-headless-setup)** — an NVIDIA DGX Spark as a hardened headless home-lab server: display-free first boot through its Wi-Fi hotspot, Tailscale-only SSH, and a UPS auto-shutdown (NUT) proven by a real power-cut test. Six pitfalls, including why NUT's default killpower cuts your router's power mid-outage.
- **[dgx-spark-cross-host-inference](https://github.com/AI-Architect-Lab-333/dgx-spark-cross-host-inference)** — llama.cpp serving an OpenAI-compatible API from that same home GPU box to AI agents on separate hosts over Tailscale, as a systemd **user** service with linger, proven by a real power-off/power-on cycle (auto-recovery in under 10 minutes, zero manual commands). Eight pitfalls, including a client that silently defaults to the wrong OpenAI API shape and a `sudo` password blocking a system-wide unit.
- **[dgx-spark-vl-beside-llm](https://github.com/AI-Architect-Lab-333/dgx-spark-vl-beside-llm)** — serve Qwen3-VL-8B next to a 100 GB LLM on 128 GB unified memory with llama.cpp: two Tailscale-only APIs, without unloading the first model. Two-model cold boot proven (~12 min, `Restart=no`). Eight pitfalls, including why a 32B vision model does not fit once the LLM is already resident, and why an HTTP 200 on the LLM is not GPU-ready.
- **[dgx-spark-idle-llm-profiles](https://github.com/AI-Architect-Lab-333/dgx-spark-idle-llm-profiles)** — systemd **user** profiles on that same DGX Spark: **idle** (GPU free), **llm** (the verified pair), or **vl** (Qwen3-VL-32B alone on `:8001`). Linger no longer reloads ~100 GB of weights on every power button. Idle reboot proven (~2.7 Gi used / ~119 Gi available).
- **[dgx-spark-qwen3-embedding](https://github.com/AI-Architect-Lab-333/dgx-spark-qwen3-embedding)** — Qwen3-Embedding-0.6B Q8 on llama.cpp, started by hand on a free DGX Spark GPU (Tailscale `:8002`, `--pooling last`). Not a fourth boot profile: the next boot stays idle and `:8002` does not come back. A retrieval score of 6/6 still leaves the wrong guide at 0.664, and a false note outranks the true guide by 0.001.
- **[dgx-spark-qwen25-3b-qlora](https://github.com/AI-Architect-Lab-333/dgx-spark-qwen25-3b-qlora)** — QLoRA NF4 of Qwen2.5-3B-Instruct on that same free GPU: an adapter can install a format, not facts. Twelve real examples lowered the loss (4.111 → 3.397) and changed no answer; a synthetic filing set went from 0/8 to 8/8, three real questions from 0/3 to 1/3. The untrained Qwen3.8-27B filed every score correctly but wrote no date — the instruction never said where the date goes, so that 0/8 measures the rule, not the model. At 280 tokens the 27B returns empty `content`: the budget went into `reasoning_content`.
- **[windows-durable-keep-awake](https://github.com/AI-Architect-Lab-333/windows-durable-keep-awake)** — keeping a Windows machine awake for a job and surviving a sign-out, via a SYSTEM Scheduled Task: the LaunchAgent equivalent, proven by a real sign-out test. Six pitfalls, including a SYSTEM task that is invisible — not merely unreadable — from an unelevated account.
- **[uptime-kuma-tailscale-cross-host-monitor](https://github.com/AI-Architect-Lab-333/uptime-kuma-tailscale-cross-host-monitor)** — a second Uptime Kuma on another host over Tailscale so alerts still fire when production dies. Documents the `https`-to-plain-HTTP trap, Kuma 302 status codes, a misleading Push history line, and a reboot too short to turn monitors red.

### Robotics

- **[dgx-spark-mujoco-headless-panda](https://github.com/AI-Architect-Lab-333/dgx-spark-mujoco-headless-panda)** — headless MuJoCo on an NVIDIA DGX Spark (GB10): EGL renders, Franka Panda, a collision-aware 6-D pinch (`GRASP_OK`), and batched MJX/Warp (GPU beats one CPU Panda at 2048 / 1024 envs). Pitfalls include teleport IK, a tossed-cube `GRASP_OK`, and quoting a single MJX env as a training rate. Needs the idle profile first.
- **[dgx-spark-isaac-lab-headless](https://github.com/AI-Architect-Lab-333/dgx-spark-isaac-lab-headless)** — Isaac Sim 6.0.1 built from source and Isaac Lab on that same GB10, verified with a headless Cartpole smoke (`CARTPOLE_OK`, 16 envs, `cuda:0`). No GUI. Documents gcc 11 vs 13, a GNU sed that ate a trailing `r`, and a PyTorch sm_121 warning that did not stop the 20 steps. No H1 training in this guide.

### GPU / machine learning

- **[blackwell-sdxl-setup-guide](https://github.com/AI-Architect-Lab-333/blackwell-sdxl-setup-guide)** — RTX 50xx (Blackwell / `sm_120`) GPUs with PyTorch nightly and reForge on WSL2, up to image generation through the API. Six pitfalls, each with its exact symptom and fix.
- **[sdxl-batch-generation-guide](https://github.com/AI-Architect-Lab-333/sdxl-batch-generation-guide)** — file-driven batch image generation with reproducible manifests: prompt files anyone can edit, real seeds read back, byte-identical reproduction verified. Companion to the Blackwell setup guide.

## Method

1. Build the thing for real, on real machines.
2. Write down every command that ran, in the order it ran — including the dead ends worth warning about.
3. Anonymize, then verify the whole chain end to end one last time.
4. Publish. If a guide is here, it worked.

<!-- This repository only exists so that this README is displayed on the account profile page. -->
