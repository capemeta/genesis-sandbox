# Genesis Sandbox

English | [简体中文](./README.zh-CN.md)

Genesis Sandbox is a general-purpose Go-based sandbox runtime service for AI agents, MCP tools, Skills, workflow platforms, low-code Code nodes, file processing, data analysis, browser automation, visual cloud desktop operations, and enterprise automation tasks.

It provides secure, isolated, and policy-controlled execution environments for running commands, scripts, tools, MCP tools, Skills, task-specific code, browser sessions, and future visual desktop operations. It can be used as the secure execution layer of Genesis Agent, but it is designed as a standalone and reusable Agent Sandbox Runtime for different agent platforms and automation systems.

Genesis Sandbox is not a general-purpose online IDE or a full AI coding product. Its goal is to safely execute the code, commands, tools, browser actions, desktop operations, and scripts required by agents and enterprise workflows to complete real-world tasks.

## Positioning

```text id="7khezk"
Genesis Sandbox = General-purpose Agent Sandbox Runtime
Genesis Agent   = One upper-layer platform that can use Genesis Sandbox
```

Genesis Sandbox focuses on solving the following problems:

```text id="5k99to"
How to safely execute untrusted code
How to isolate AI-generated scripts
How to run MCP tools under policy control
How to execute Skills scripts with risk-based isolation
How to process files without polluting the main platform
How to support browser automation and visual cloud desktop operations
How to provide a governed execution environment for long-running agents
How to control resources, networking, logs, and audits in multi-tenant enterprise environments
```


## Highlights

* **General-purpose agent sandbox runtime**: Not limited to Genesis Agent. It can be integrated by AI agent platforms, low-code platforms, workflow engines, MCP tool ecosystems, and enterprise automation systems.
* **Standalone Go sandbox service**: The sandbox is not an execution function embedded inside the main backend. It runs as an independent `sandbox-service` and communicates with upper-layer platforms through HTTP/gRPC.
* **Docker-first runtime strategy**: The first stage focuses on Docker-based execution. Windows/macOS development environments can use Docker, while Linux production environments prefer Docker + gVisor/runsc.
* **Runtime Driver abstraction**: A unified Runtime Driver interface hides low-level runtime details and keeps the system extensible for containerd, Kubernetes RuntimeClass, Kata Containers, Firecracker MicroVM, WASM/WASI, and other runtimes.
* **Runtime capability probing**: Capability Probe detects whether the underlying Docker daemon or runtime environment supports runc, runsc, Kata, checkpointing, and other capabilities before execution.
* **Policy-driven runtime selection**: Runtime selection is based on task risk level, tenant policy, Runtime Profile, and capability probing results. High-risk workloads must not be silently downgraded.
* **Sandbox pool and lease management**: Supports pre-warmed sandbox pools, lease allocation, release, renewal, job cancellation, draining, destruction, and heartbeat, preventing agents or tools from directly holding raw containers.
* **Complete sandbox lifecycle**: Covers states such as created, starting, ready, leased, busy, idle, draining, destroyed, and failed, making scheduling, cleanup, auditing, and failure handling easier.
* **Runtime Profile system**: Supports task-oriented runtime profiles such as `code-python-basic`, `code-python-data`, `tool-basic`, `skill-python-basic`, `browser-playwright`, and `desktop-cloud`.
* **Layered execution for Code / Tool / Skill / Browser / Desktop**: Separates code sandboxes, tool sandboxes, trusted runtimes, browser automation sandboxes, and visual cloud desktop sandboxes, applying different isolation policies based on risk level.
* **MCP and Skills execution support**: Can serve as the secure execution layer for MCP tools, third-party tools, and Skills scripts, dynamically mapping them to different sandbox profiles based on source, permissions, and risk level.
* **Browser automation**: Provides a `browser-playwright` profile for Playwright, Chromium, web automation, web data extraction, screenshots, form filling, operation replay, and browser-agent scenarios.
* **Visual cloud desktop operations**: Reserves profiles such as `desktop-cloud`, `desktop-vnc`, and `desktop-browser` for remote desktop environments that can be operated by agents, observed by users, and taken over by humans when needed.
* **Visual agent operation trace**: Future support for visualizing browser operations, desktop operations, mouse clicks, keyboard input, screenshots, execution logs, and task status, making agent behavior observable, auditable, replayable, and interruptible.
* **Human-in-the-loop and manual takeover**: Designed for complex enterprise tasks where users may need to observe the agent’s current browser or desktop state and take over the session when necessary.
* **Tenant-aware quotas and fair queueing**: Supports tenant-level resource quotas and fair scheduling to prevent a single tenant or workspace from exhausting sandbox capacity.
* **Standardized API design**: Provides APIs for capability probing, sandbox leasing, release, destruction, querying, metadata updates, job execution, log reading, and file management.
* **Optimistic concurrency control**: Introduces `resourceVersion` / `ETag` / `If-Match`-style concurrency control for multi-agent or concurrent workflow scenarios that modify sandbox metadata.
* **StatelessExec and StatefulSession separation**: Uses safer stateless execution by default while reserving stateful sessions for interactive tasks, Code Interpreter, browser automation, cloud desktop operations, and long-running agent workspaces.
* **Script-wrapping based state preservation**: Preserves and restores cwd/env state through script wrapping, simulating shell-like sessions while avoiding the instability of long-lived PTY processes.
* **SSE streaming logs**: Supports Server-Sent Events for stdout, stderr, status, and other execution events.
* **Cursor-based log resume**: Logs can be tailed from files with offset/cursor support, allowing clients to resume log reading after network interruptions.
* **File gateway capabilities**: Supports file upload, download, artifact management, Range-based downloads, file search, and batch replacement, reducing frequent download-modify-upload cycles.
* **Artifact management**: Manages output files, transformation results, analysis results, screenshots, browser recordings, desktop operation recordings, and logs as execution artifacts.
* **Resource limits**: Supports limits for CPU, memory, disk, PID, execution timeout, stdout/stderr size, and artifact size to prevent runaway jobs from overwhelming the host.
* **Network policy model**: Uses restricted networking by default and supports policy models such as `none`, `allowlist`, `internal-api`, and `tool-proxy`.
* **Future secure egress governance**: Reserves extension points for Tool Proxy, DNS-based dynamic egress allowlists, nftables-based dynamic firewalling, and Credential Vault.
* **Metadata and state storage design**: Can combine Docker Labels, filesystem JSON, SQLite/PostgreSQL to manage sandbox state, user metadata, job records, snapshots, and audit logs.
* **Orphan container recovery**: Can scan and clean up exited, expired, or orphaned sandbox containers after service restart to avoid resource leaks.
* **Health, readiness, and capabilities APIs**: Provides Health, Readiness, and Capabilities APIs for production deployment, monitoring, and operations.
* **Long-running agent runtime roadmap**: Evolves from short-task sandboxes to session-level sandboxes, project workspace sandboxes, terminal streaming, browser automation, visual cloud desktop operations, file diff/patch, checkpoint/rollback, and long-running autonomous agent execution.

## Browser Automation and Visual Cloud Desktop

Genesis Sandbox is not limited to code, command, and tool execution. It is also designed to support browser automation and visual cloud desktop operations for future agent scenarios.

Browser automation is intended for web access, form filling, screenshots, web data extraction, enterprise web system operations, workflow automation, and browser-agent scenarios. This capability can be isolated through Runtime Profiles such as `browser-playwright`, combined with resource limits, network policies, logs, audit records, screenshots, recordings, and artifact management.

Visual cloud desktop operations are designed for more complex human-agent collaboration and enterprise task execution scenarios. Agents can operate browsers, desktop software, terminals, or enterprise system interfaces inside a controlled remote desktop environment. Users can observe the agent’s actions in real time and manually take over the session when necessary.

This capability provides a foundation for future OpenClaw-like, Manus-like, and Claude Computer Use-like enterprise agent products. It enables agents not only to call APIs and run scripts, but also to complete complex software operations, web operations, and cross-system tasks inside a visual and controllable environment.
