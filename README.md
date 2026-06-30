# Genesis Sandbox

English | [简体中文](./README.zh-CN.md)

Genesis Sandbox is a general-purpose Go-based sandbox runtime service for AI agents, MCP tools, skills, workflow platforms, file processing, data analysis, and code-assisted enterprise automation.

It provides secure, isolated, and policy-controlled execution environments for running commands, scripts, tools, and task-specific code. It can be used as the execution layer of Genesis Agent, but it is designed as a standalone and reusable sandbox runtime for different agent platforms and automation systems.

Genesis Sandbox is not a general-purpose online IDE or a full AI coding product. Its goal is to safely execute the code, commands, tools, and scripts required by agents and enterprise workflows to complete real-world tasks.

## Highlights

- General-purpose sandbox runtime for AI agents and automation platforms
- Standalone Go service with HTTP/gRPC APIs
- Docker-first execution with Linux production support for gVisor/runsc
- Runtime Driver abstraction and capability probing
- Policy-driven runtime selection with no silent downgrade for high-risk workloads
- Sandbox pool, lease, lifecycle, and cleanup management
- Runtime profiles for code, tools, skills, trusted tools, and browser automation
- Tenant-aware quotas and fair queueing
- Stateless job execution and optional stateful sessions
- Script-wrapping based shell state preservation
- SSE streaming logs with cursor-based resume
- File gateway for upload, download, artifacts, search, and batch replace
- Resource limits for CPU, memory, disk, PID, timeout, stdout/stderr, and artifacts
- Network policy model with future Tool Proxy, DNS-based egress control, and Credential Vault
- Health, readiness, and capabilities APIs
- Roadmap toward project workspaces, terminal streaming, browser sandboxing, checkpoint/rollback, and long-running autonomous agent runtimes
