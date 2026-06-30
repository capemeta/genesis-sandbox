# Genesis Sandbox

[English](./README.md) | 简体中文

Genesis Sandbox 是一个基于 Go 的通用沙箱运行时服务，面向 AI Agent、MCP 工具、Skills、工作流平台、低代码 Code 节点、文件处理、数据分析、浏览器自动化、云桌面可视化操作和企业级自动化任务。

它提供安全、隔离、可治理的执行环境，用于运行命令、脚本、工具、MCP 工具、Skills、任务专用代码、浏览器会话，以及未来的可视化桌面操作。Genesis Sandbox 可以作为 Genesis Agent 的安全执行层，但它本身被设计为一个独立、通用、可复用的 Agent Sandbox Runtime，可被不同的 Agent 平台和自动化系统集成使用。

Genesis Sandbox 不是通用在线 IDE，也不是完整的 AI 编程产品。它的目标是安全地执行 Agent 和企业工作流完成真实任务所需的代码、命令、工具、浏览器动作、桌面操作和脚本。

## 定位

```text
Genesis Sandbox = 通用 Agent 沙箱运行时
Genesis Agent   = 可以使用 Genesis Sandbox 的一个上层平台
```

Genesis Sandbox 重点解决以下问题：

```text
如何安全执行不可信代码
如何隔离运行 AI 生成的脚本
如何在策略控制下运行 MCP 工具
如何按风险等级隔离执行 Skills 脚本
如何在不污染主平台的情况下处理文件
如何支持浏览器自动化和云桌面可视化操作
如何为长期运行的 Agent 提供可治理的执行环境
如何在企业多租户环境下控制资源、网络、日志和审计
```


## 核心亮点

* **通用 Agent 沙箱运行时**：不仅服务于 Genesis Agent，也可以被其他 AI Agent 平台、低代码平台、工作流系统、MCP 工具生态和企业自动化系统集成使用。
* **独立 Go 沙箱服务**：沙箱不是主后端里的一个执行函数，而是独立运行的 `sandbox-service`，通过 HTTP/gRPC 与上层平台解耦。
* **Docker 优先的执行路线**：第一阶段以 Docker 为核心运行时，开发环境支持 Windows/macOS + Docker，Linux 生产环境优先支持 Docker + gVisor/runsc。
* **Runtime Driver 抽象**：通过统一 Runtime Driver 接口屏蔽底层实现，未来可扩展 containerd、Kubernetes RuntimeClass、Kata Containers、Firecracker MicroVM、WASM/WASI 等运行时。
* **Runtime 能力探测机制**：通过 Capability Probe 探测底层 Docker daemon 或运行时环境是否支持 runc、runsc、Kata、checkpoint 等能力，避免错误假设执行环境。
* **策略驱动的 Runtime 选择**：根据任务风险等级、租户策略、Runtime Profile 和能力探测结果选择合适运行时，高风险任务不允许静默降级。
* **沙箱池与租约管理**：支持沙箱预热池、租约分配、释放、续约、取消任务、Drain、Destroy 和 Heartbeat，避免 Agent 或工具直接持有底层容器。
* **完整沙箱生命周期**：覆盖 created、starting、ready、leased、busy、idle、draining、destroyed、failed 等状态，便于调度、回收、审计和故障处理。
* **Runtime Profile 体系**：支持面向不同任务的运行时配置，例如 `code-python-basic`、`code-python-data`、`tool-basic`、`skill-python-basic`、`browser-playwright`、`desktop-cloud` 等。
* **Code / Tool / Skill / Browser / Desktop 分层执行**：区分代码执行沙箱、工具执行沙箱、可信运行时、浏览器自动化沙箱和云桌面可视化操作沙箱，针对不同风险等级采用不同隔离策略。
* **MCP 与 Skills 执行支持**：可作为 MCP 工具、第三方工具和 Skills 脚本的安全执行层，支持按来源、权限和风险等级动态映射到不同沙箱 Profile。
* **浏览器自动化能力**：预留 `browser-playwright` Profile，用于 Playwright、Chromium、网页自动化、网页数据采集、页面截图、表单填写、网页操作回放和浏览器 Agent 场景。
* **云桌面操作可视化能力**：预留 `desktop-cloud` / `desktop-vnc` / `desktop-browser` 等 Profile，用于构建可被 Agent 操作、可被用户观看和接管的远程桌面环境。
* **可视化 Agent 操作链路**：未来支持将 Agent 的浏览器操作、桌面操作、鼠标点击、键盘输入、截图、执行日志和任务状态以可视化方式展示给用户，便于观察、审计、回放和人工接管。
* **人在回路与人工接管**：面向复杂企业任务，支持未来在人机协同场景下让用户随时查看 Agent 当前操作界面，并在必要时接管浏览器或云桌面继续操作。
* **多租户配额与公平队列**：支持租户级资源配额和公平调度，避免单个租户或工作区占满沙箱资源。
* **标准化 API 设计**：提供能力探测、沙箱租赁、释放、销毁、查询、元数据更新、任务执行、日志读取和文件管理等 API。
* **乐观并发控制**：针对多 Agent 或并发工作流修改沙箱元数据的场景，引入类似 `resourceVersion` / `ETag` / `If-Match` 的并发控制机制。
* **StatelessExec 与 StatefulSession 分离**：默认采用更安全的无状态任务执行，同时为交互式任务、Code Interpreter、浏览器自动化、云桌面操作和长期 Agent 工作区预留有状态会话能力。
* **脚本包装式状态保留机制**：通过 Script Wrapping 方式保存和恢复 cwd/env 状态，模拟 Shell 会话能力，同时避免长期 PTY 进程带来的稳定性问题。
* **SSE 流式日志**：支持 stdout、stderr、status 等事件类型的 Server-Sent Events 日志推送。
* **日志 Cursor 续读**：底层日志可基于文件 tail 和 offset/cursor 机制读取，网络中断后客户端可以从指定位置继续拉取日志。
* **文件网关能力**：支持文件上传、下载、产物管理、Range 断点下载、文件搜索、批量替换等能力，减少 Agent 频繁 download-modify-upload 的开销。
* **执行产物管理**：支持将任务输出文件、转换结果、分析结果、截图、浏览器录屏、桌面操作录屏、日志等作为 artifacts 统一管理。
* **资源限制**：支持 CPU、内存、磁盘、PID、执行超时、stdout/stderr 大小、artifact 大小等限制，避免失控任务拖垮宿主机。
* **网络策略模型**：默认限制网络访问，支持 `none`、`allowlist`、`internal-api`、`tool-proxy` 等策略模型。
* **未来安全出网治理**：预留 Tool Proxy、DNS-based Dynamic Egress Allowlist、nftables 动态防火墙、Credential Vault 等高级安全出网能力。
* **元数据与状态存储设计**：可结合 Docker Label、文件系统 JSON、SQLite/PostgreSQL 分别承载沙箱状态、用户元数据、Job 记录、Snapshot 和审计日志。
* **孤儿容器恢复机制**：服务重启后可扫描并清理异常退出、过期或无主沙箱容器，避免资源泄漏。
* **健康检查与就绪检查**：提供 Health、Readiness 和 Capabilities API，便于生产部署、监控和运维。
* **长期 Agent Runtime 演进路线**：从短任务沙箱逐步演进到会话级沙箱、项目级 Workspace 沙箱、Terminal Streaming、浏览器自动化、云桌面可视化操作、文件 Diff/Patch、Checkpoint/Rollback 和长期自主 Agent 执行环境。

## 浏览器自动化与云桌面可视化

Genesis Sandbox 不仅支持代码、命令和工具执行，也会面向未来 Agent 场景提供浏览器自动化和云桌面可视化操作能力。

浏览器自动化能力主要用于网页访问、表单填写、页面截图、网页数据采集、后台系统操作、网页流程自动化和浏览器 Agent 场景。该能力可通过 `browser-playwright` 等 Runtime Profile 进行隔离执行，并结合资源限制、网络策略、日志审计和执行产物管理，确保浏览器自动化任务可控、可追踪、可回收。

云桌面可视化能力主要用于更复杂的人机协同和企业任务执行场景。Agent 可以在受控的远程桌面环境中操作浏览器、桌面软件、命令行工具或企业系统界面，用户可以实时查看 Agent 的操作过程，并在必要时进行人工接管。

这一能力将为未来 OpenClaw-like、Manus-like、Claude Computer Use-like 的企业级自主 Agent 产品提供基础支撑，使 Agent 不仅能够调用 API 和执行脚本，也能够在可视化环境中完成复杂的软件操作、网页操作和跨系统任务。

