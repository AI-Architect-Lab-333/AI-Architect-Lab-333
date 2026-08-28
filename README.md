# AI Architect Lab

Verified, reproducible guides from real infrastructure work. Every command published here was run for real, in order, on real machines — and the pitfall sections document what actually broke along the way, not what theoretically might.

Each guide states its verified environment (OS, versions, date) and ends with the end-to-end test that proves the setup works. They are written to be followed by **humans and AI agents alike** — the verification steps are never optional, because several failure modes in these setups are silent.

## Guides

### AI agents

- **[ai-agent-guardrails-windows-guide](https://github.com/AI-Architect-Lab-333/ai-agent-guardrails-windows-guide)** — a global guardrail that blocks catastrophic shell commands (`rm -rf /`, `git push --force`, disk wipes…) before any AI agent runs them, on Windows. Documents two pitfalls that silently disable the naive Unix recipe.
- **[pi-hermes-setup](https://github.com/AI-Architect-Lab-333/pi-hermes-setup)** — a controller/controlled agent pair: Pi Agent in Docker on Windows driving a Hermes agent on a remote VPS over SSH + tmux, with a Mixture of Agents preset.
- **[windows-tmux-agent-orchestration](https://github.com/AI-Architect-Lab-333/windows-tmux-agent-orchestration)** — driving several AI agents in parallel tmux panes on Windows (WSL2): spawn, send input, read output, poll. A Cmux-to-tmux port; documents the `docker-desktop`-default-distro and PowerShell→wsl→bash quoting traps.
- **[dsh-blind-circuit-windows](https://github.com/AI-Architect-Lab-333/dsh-blind-circuit-windows)** — DeepSeek Harness Web UI on Windows plus llama.cpp on a Tailscale-only GPU box: the coding agent starts the UI and probes model ids, but never opens the workspace. Six pitfalls, including why `Start-Process -WindowStyle Hidden` then `ERR_CONNECTION_REFUSED`.

### Self-hosted infrastructure

- **[vps-tailscale-hardening-guide](https://github.com/AI-Architect-Lab-333/vps-tailscale-hardening-guide)** — locking down a VPS behind Tailscale until no port answers on the public IP. Covers the Docker-bypasses-UFW trap and the sshd config read-order trap.
- **[vps-tailscale-backup-pull-guide](https://github.com/AI-Architect-Lab-333/vps-tailscale-backup-pull-guide)** — nightly pull-architecture backups between two VPS over Tailscale: rsync, systemd timers, integrity checksums, retention, and a restore procedure. Production can never touch its own backups.
- **[tailscale-aperture-openrouter-gateway](https://github.com/AI-Architect-Lab-333/tailscale-aperture-openrouter-gateway)** — one identity-authenticated LLM gateway on your tailnet: the OpenRouter API key stays server-side and no device ever holds it, with per-user dollar quotas. Documents the non-existent-model-ID and silent empty-reasoning-response traps.
- **[dgx-spark-headless-setup](https://github.com/AI-Architect-Lab-333/dgx-spark-headless-setup)** — an NVIDIA DGX Spark as a hardened headless home-lab server: display-free first boot through its Wi-Fi hotspot, Tailscale-only SSH, and a UPS auto-shutdown (NUT) proven by a real power-cut test. Six pitfalls, including why NUT's default killpower cuts your router's power mid-outage.
- **[dgx-spark-cross-host-inference](https://github.com/AI-Architect-Lab-333/dgx-spark-cross-host-inference)** — llama.cpp serving an OpenAI-compatible API from that same home GPU box to AI agents on separate hosts over Tailscale, as a systemd **user** service with linger, proven by a real power-off/power-on cycle (auto-recovery in under 10 minutes, zero manual commands). Eight pitfalls, including a client that silently defaults to the wrong OpenAI API shape and a `sudo` password blocking a system-wide unit.
- **[dgx-spark-vl-beside-llm](https://github.com/AI-Architect-Lab-333/dgx-spark-vl-beside-llm)** — serve Qwen3-VL-8B next to a 100 GB LLM on 128 GB unified memory with llama.cpp: two Tailscale-only APIs, without unloading the first model. Two-model cold boot proven (~12 min, `Restart=no`). Eight pitfalls, including why a 32B vision model does not fit once the LLM is already resident, and why an HTTP 200 on the LLM is not GPU-ready.
- **[windows-durable-keep-awake](https://github.com/AI-Architect-Lab-333/windows-durable-keep-awake)** — keeping a Windows machine awake for a job and surviving a sign-out, via a SYSTEM Scheduled Task: the LaunchAgent equivalent, proven by a real sign-out test. Six pitfalls, including a SYSTEM task that is invisible — not merely unreadable — from an unelevated account.
- **[uptime-kuma-tailscale-cross-host-monitor](https://github.com/AI-Architect-Lab-333/uptime-kuma-tailscale-cross-host-monitor)** — a second Uptime Kuma on another host over Tailscale so alerts still fire when production dies. Documents the `https`-to-plain-HTTP trap, Kuma 302 status codes, a misleading Push history line, and a reboot too short to turn monitors red.

### GPU / machine learning

- **[blackwell-sdxl-setup-guide](https://github.com/AI-Architect-Lab-333/blackwell-sdxl-setup-guide)** — RTX 50xx (Blackwell / `sm_120`) GPUs with PyTorch nightly and reForge on WSL2, up to image generation through the API. Six pitfalls, each with its exact symptom and fix.
- **[sdxl-batch-generation-guide](https://github.com/AI-Architect-Lab-333/sdxl-batch-generation-guide)** — file-driven batch image generation with reproducible manifests: prompt files anyone can edit, real seeds read back, byte-identical reproduction verified. Companion to the Blackwell setup guide.

## Method

1. Build the thing for real, on real machines.
2. Write down every command that ran, in the order it ran — including the dead ends worth warning about.
3. Anonymize, then verify the whole chain end to end one last time.
4. Publish. If a guide is here, it worked.

<!-- This repository only exists so that this README is displayed on the account profile page. -->
