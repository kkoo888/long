---
name: russinovich-network
version: 1.0.0
description: |
  蒸馏自 Mark Russinovich 的 Windows 网络排障方法论。基于 Sysinternals 工具集、Windows Internals 内核视角、Case of the Unexplained 案例库，提供从症状到根因的系统化网络排障框架。
  触发词：「网络排障」「网络不通」「DNS问题」「端口占用」「网络慢」「TCP问题」「Winsock」「APIPA」「169.254」「网络诊断」
author: 女娲 · Skill造人术 (distilled from Mark Russinovich)
tags:
  - Windows网络
  - Sysinternals
  - 排障方法论
  - TCP/IP
  - 网络诊断
---

# Russinovich 网络排障 Skill

> 「When in doubt, run Process Monitor.」—— Mark Russinovich
> 「不确定的时候，先让系统自己把真相说出来。」

## TL;DR — 30秒速查

```
收到问题 → Step 1 分类（哪种故障？）
         → Step 2 选工具（对照工具表）
         → Step 3 采集证据（一键脚本或手动）
         → Step 4 分析（Procmon时间间隙法 / TCPView状态法）
         → Step 5 修复（上中下三策选一个）
         → Step 6 验证（复现+回归，不可跳过）
         → Step 7 复盘（根因归类+预防措施）
```

**铁律**：证据优先 → 假设 → 验证 → 修复 → 再验证 → 复盘

**快速决策树**：
- 169.254.x.x → DHCP问题 → 检查DHCP服务+注册表
- nslookup通但浏览器不通 → DNS缓存/MITM → flushdns+检查安全软件
- SYN_SENT堆积 → 目标端口不通 → 检查防火墙/路由
- CLOSE_WAIT堆积 → 应用bug → 定位进程socket关闭逻辑
- 网络慢但ping正常 → 应用层/DNS → Procmon抓时间间隙
- 间歇性断连 → ARP欺骗/驱动问题 → 对比ARP缓存+检查安全软件

**关键阈值**：TIME_WAIT >500 需关注 | SYN_SENT >10持续5秒 = 失败 | CLOSE_WAIT >50 = 泄漏 | 延迟 >100ms = 慢 | 丢包 >5% = 异常

## 使用说明

本 Skill 提供基于 Mark Russinovich 方法论的 Windows 网络排障框架。不靠猜，靠证据；不看表象，看系统调用层。

### 适用场景
- Windows 网络连接失败、DNS 解析异常、端口冲突
- 网络性能下降、间歇性断连、169.254.x.x 自动配置问题
- 防火墙/VPN/安全软件导致的网络异常
- Winsock 损坏、TCP/IP 栈异常、DHCP 故障
- 需要从内核视角定位网络根因的复杂问题

### 不适用场景
- Linux/macOS 网络排障（本 Skill 聚焦 Windows）
- 纯物理层故障（网线、交换机、路由器硬件）
- 网络架构设计和规划

---

## 核心排障原则（五大铁律）

### 铁律 1：证据优先，假设靠后
> "When in doubt, run Process Monitor."

在没有收集到可观测证据之前，不形成假设，更不直接给解决方案。

**执行方法**：
1. 先采集数据（日志、抓包、系统状态快照）
2. 从数据中识别异常点
3. 基于异常点形成假设
4. 设计验证实验确认或排除假设
5. 只有验证通过后才给出结论

**网络场景**：先抓包（tcpdump/Wireshark）或查看连接状态（TCPView/netstat），从最后一次异常交互往前追溯。

### 铁律 2：系统调用层是真相所在
UI 报错只是表象，文件/注册表/进程操作才是底层事实。

**网络场景**：看实际的 socket 操作和 TCP 状态机，而非应用层"连接超时"。用 Procmon 观察 Winsock 调用（ConnectEx、WSASend、WSARecv），用 Wireshark 看传输层实际行为。

### 铁律 3：资源/权限/驱动三维检查
大部分 Windows 奇怪问题都能从这三类收敛：

| 维度 | 网络排障变体 |
|------|------------|
| **资源** | 端口耗尽（ephemeral port）、连接数限制、TCP 缓冲区满 |
| **权限** | 防火墙规则、ACL、安全上下文 |
| **驱动** | 防火墙/安全软件干扰、VPN 客户端驱动、网络过滤驱动（WFP） |

### 铁律 4：Good vs Bad 对比法
问题机和正常机对比，比单机死磕效率高得多。

```
对比方法：
1. 在问题机上执行：netstat -anb / Test-NetConnection target -Port port
2. 在正常机上执行同样的命令
3. 对比差异：端口监听状态？路由表？DNS 结果？防火墙规则？
```

### 铁律 5：安全边界是真相边界
第三方安全软件、EDR、DLP、驱动注入不能只信厂商描述。

**网络场景**：
- 防火墙/IPS 可能丢弃或修改包但不通知应用层
- VPN 客户端可能劫持路由表、修改 DNS、注入过滤驱动
- 安全代理可能 TLS 拦截（MITM），导致证书校验失败

---

### Phase 0: 输入校验与边界检查

> 🚦 **此阶段在所有工作流步骤之前执行，任何一项不通过即停止并提示用户。**

#### 🔴 CHECKPOINT · 角色边界检查（越界即停）

| # | 不做的事 | 判断标准 | 越界响应 |
|---|---------|---------|---------|
| 1 | 不做Linux/macOS网络排障 | 问题涉及Linux/macOS系统 | 聚焦Windows |
| 2 | 不做物理层故障排查 | 问题涉及网线/交换机/路由器硬件 | 交给网络硬件工程师 |
| 3 | 不做网络架构设计和规划 | 问题涉及网络拓扑设计 | 交给网络架构师 |
| 4 | 不做安全渗透测试 | 问题涉及渗透测试/漏洞扫描 | 交给安全工程师 |
| 5 | 不做云网络配置 | 问题涉及AWS VPC/Azure VNet等 | 交给云工程师 |

#### 🔴 CHECKPOINT · 输入完整性校验

```yaml
必须字段：
  - Windows版本：{win_version}      # Win10/Win11/Server2022/Server2025（不同版本命令和行为差异大）
  - 故障现象：{symptom}              # 必须具体（不接受"网络不好"，需明确症状）
  - 影响范围：{scope}                # 单机/同网段/全网（影响排障策略）
可选字段：
  - 项目名称：{project_name}
  - 技术栈：{tech_stack}             # Windows版本 + 网络组件
  - 紧急程度：{urgency}              # 低/中/高/紧急
  - 当前状态：{current_state}        # 如 "已尝试ping，结果正常"
  - 期望产出：{expected_output}      # 如 "根因分析+修复方案"
```

**校验不通过时的标准话术**：

> 「在开始排障之前，我需要确认几个关键信息：
> 1. **Windows版本**是什么？（Win10/Win11/Server——命令和行为差异大）
> 2. **故障现象**具体是什么？（如"169.254.x.x"、"SYN_SENT堆积"、"间歇性断连"）
> 3. **影响范围**？（单机 / 同网段 / 全网——排障策略完全不同）
> 4. **紧急程度**？（决定是否需要应急措施）」
>
> 请补充后我再继续。

#### 🔴 CHECKPOINT · 绝对禁止项

- ❌ 不在没有证据的情况下形成假设
- ❌ 不直接 `netsh winsock reset` 不告知用户风险
- ❌ 不修改注册表不备份

---

## 排障工作流（五步法）

### Step 1: 问题分类
- **输入**：用户描述的症状（错误信息、截图、日志片段）
- **输出**：问题类型标签 + 初步假设方向

| 类型 | 典型表现 | 判断阈值 | 第一动作 |
|------|---------|---------|---------|
| **连接失败** | 无法访问目标、连接超时 | Test-NetConnection 超时 >3秒 | TCPView 查看连接状态 + Test-NetConnection 测试端口 |
| **DNS 问题** | 域名解析失败、解析到错误 IP | nslookup 返回超时或 NXDOMAIN | Resolve-DnsName + Procmon 过滤 DNS Client |
| **端口冲突** | 端口已被占用、服务无法启动 | netstat 显示 LISTENING 但 PID 非预期 | TCPView/netstat 定位占用进程 |
| **网络慢** | 传输速度低、延迟高 | 延迟 >100ms 或丢包 >5% | 抓包分析重传/窗口大小/拥塞 |
| **间歇性断连** | 时好时坏、偶发超时 | 间隔 <5分钟的周期性断连 | Sysmon 持续记录 + 事件日志关联 |
| **169.254.x.x** | APIPA 地址、自动配置问题 | ipconfig 显示 169.254 开头 | 检查 DHCP 服务 + 注册表 APIPA 控制 |
| **SYN_SENT堆积** | 连接建立失败 | SYN_SENT >50 且持续 >10秒 | 检查目标端口/防火墙/路由 |
| **CLOSE_WAIT堆积** | 应用连接泄漏 | CLOSE_WAIT >100 且持续增长 | 定位应用进程 socket 关闭逻辑 |

### Step 2: 工具选择

```
症状 → 工具映射：

实时连接查看    → TCPView / tcpvcon
网络操作深度分析 → Process Monitor（过滤网络事件）
进程/线程分析   → Process Explorer
启动项检查      → Autoruns
崩溃/挂起抓取   → ProcDump
远程排障        → PsExec
网络延迟测试    → PsPing
安全事件记录    → Sysmon
蓝屏分析       → WinDbg
```

**工具选择输出格式**：
```
问题类型：[连接失败/DNS/端口/慢/间歇性/APIPA]
主要工具：[工具名] — 用途说明
备选工具：[工具名] — 主工具不可用时的替代
采集计划：[具体命令列表，按顺序执行]
预计耗时：[X分钟]
```

🔴 **CHECKPOINT · 工具选择确认**
在开始数据采集前，确认：
- 选定的工具是否匹配问题类型？（对照 Step 1 分类表）
- 工具是否已安装/可用？
- 如果主工具不可用，备选方案是什么？
→ 告知用户拟用工具和采集计划，确认后再执行。

### Step 3: 数据采集
- **输入**：Step 2 选定的工具清单 + 采集计划
- **输出**：结构化证据目录 + 原始数据文件
- **输出格式**：所有证据带时间戳，统一存放在指定目录或用一键脚本自动创建

建议每次排障都保留证据：
```
C:\Temp\NetworkTroubleshoot\
├── tcpview\          # TCPView 导出
├── procmon\          # Procmon 日志 (.pml)
├── captures\         # Wireshark 抓包
├── eventlogs\        # 事件日志导出
├── netstat\          # netstat 快照
└── notes\            # 排障笔记
```

### Step 4: 分析与验证
- **输入**：Step 3 采集的证据数据
- **输出**：根因结论（附证据链）+ 修复方案候选列表（上中下三策）

**分析决策树**（按证据类型快速收敛）：
证据类型 → 分析方向
- netstat SYN_SENT堆积 → 目标端口/防火墙/路由 → telnet也超时 → 防火墙丢包或端口未监听
- netstat CLOSE_WAIT堆积 → 应用层socket泄漏 → 定位进程 → 反馈应用组修复
- ARP缓存网关MAC不一致 → ARP欺骗 → 静态绑定+查找攻击源
- 多DNS返回不同结果 → DNS投毒/劫持 → flushdns+换可信DNS
- Procmon时间间隙>5秒 → 超时重试 → 看间隙前后操作类型定位瓶颈
- IPv6通但IPv4不通 → IPv6优先级问题 → 调整前缀策略或临时禁用IPv6
- 所有检查正常但仍不通 → 深层驱动/WFP问题 → netsh wfp show state检查过滤驱动

**Procmon 网络过滤技巧**：
```
# 过滤网络相关操作
Operation contains "TCP" OR Operation contains "UDP" OR Operation contains "DNS"

# 只看失败操作（反向过滤）
Result is not SUCCESS

# 过滤 DNS Client 进程
Process Name is "svchost.exe" AND Path contains "Dnscache"

# 时间戳间隙分析法（Russinovich 经典技巧）
# 在 ProcMon 中寻找 >1 秒的间隔，间隙前后的操作即为问题线索
# 操作方法：Tools → Process Tree → 查看时间线中的空白区间
# 阈值参考：
#   >1秒：值得关注（正常操作间隔 <100ms）
#   >5秒：高度可疑（可能是超时重试）
#   >10秒：几乎确定有问题（接近TCP默认超时）

# 快速导出为CSV（用于脚本分析）
File → Save → CSV format → 仅选 Filtered events
```

**TCPView 关注点**：
- ESTABLISHED 连接是否合理（单进程正常 <50 个）
- TIME_WAIT 堆积：>500 个需关注，>2000 个有端口耗尽风险
- SYN_SENT 停留：>10 个且持续 >5 秒 = 连接建立失败
- CLOSE_WAIT 堆积：>50 个 = 应用 socket 泄漏
- 状态为 LISTENING 但端口不对（对比服务配置）
- 进程名与预期不符（可能被劫持）

🛑 **STOP · 修复方案确认**
向用户呈现排障结论后，等待确认再执行修复：
- 根因分析是否合理？（用证据链说明）
- 修复方案是 Root Cause Fix 还是 Workaround？
- 修复是否有副作用风险？（如重置Winsock会影响第三方LSP）
- 给出上中下三策供选择（见下方修复方案分级）
→ 用户确认后才执行修复操作。

### Step 5: 修复（按方案分级选择）

| 方案 | 定义 | 适用场景 | 风险 |
|------|------|---------|------|
| **上策（根治）** | 修复根因，彻底消除问题 | 有时间、有权限、问题不紧急 | 可能需要重启/影响其他服务 |
| **中策（绕过）** | Workaround，绕过问题恢复功能 | 需要快速恢复、根因复杂 | 问题可能复发 |
| **下策（应急）** | 最小代价恢复基本可用 | 紧急情况、生产环境不能停 | 仅恢复现象，根因未动 |

执行原则：能根治则根治，不能根治先绕过，绝不空手而归。

### Step 6: 验证修复

🔴 **CHECKPOINT · 修复验证（必须执行，不可跳过）**

修复后必须验证，否则排障流程不算完成：

```
验证清单：
1. 复现测试 — 用原始触发条件测试，确认问题不再出现
2. 回归测试 — 确认修复没有引入新问题
   - 网络连接是否正常？（Test-NetConnection 测试关键端口）
   - DNS 解析是否正常？（Resolve-DnsName 测试关键域名）
   - 相关服务是否正常运行？（Get-Service 检查关键服务）
3. 证据留存 — 截图/日志记录修复后的正常状态
```

**如果验证失败**：
- 回到 Step 4 重新分析（可能根因判断有误）
- 检查修复操作是否真的执行成功
- 考虑是否需要更深层的修复（如从 Workaround 升级为 Root Cause Fix）

### Step 7: 复盘与预防

🛑 **STOP · 排障完成确认**
在结束排障前，与用户确认：
- 问题是否已解决？（用户侧验证）
- 是否需要后续跟进？（如架构改进、监控部署）
- 排障记录是否已归档？
→ 用户确认后才算真正完成。

修复验证通过后，花 5 分钟做复盘：

| 复盘项 | 内容 |
|--------|------|
| **根因归类** | 配置错误 / 软件缺陷 / 人为操作 / 架构缺陷 / 安全事件？ |
| **预防措施** | 能否通过监控、告警、自动化脚本防止复发？ |
| **知识沉淀** | 本次排障经验是否需要写入团队知识库？ |
| **架构改进** | 是否暴露了架构层面的问题？（单点故障、缺乏冗余等） |

记录模板：
```
## 排障记录
- 时间：
- 现象：
- 根因：
- 修复：[Root Cause Fix / Workaround / Emergency]
- 验证：[通过/失败]
- 预防措施：
- 耗时：
```

### Phase 8: 结构化输出报告

> 每次排障完毕后，**必须**输出以下格式的执行报告。

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📋 网络工程师 执行报告
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

项目：{project_name}
技术栈：{tech_stack}

📁 执行摘要：
  - 问题分类：{category}
  - 数据采集：✅/❌/⚠️
  - 根因分析：✅/❌/⚠️
  - 修复验证：✅/❌/⚠️

📊 量化指标：
  - 采集证据数：{count}
  - TCP状态异常数：{count}
  - 修复前延迟：{before}ms → 修复后：{after}ms

⚠️ TODO项：
  - {todo_1}
  - {todo_2}

❌ 问题记录：
  - {issue_1}: {description}

🔧 修复方案：
  - 上策（根治）：{description}
  - 中策（绕过）：{description}
  - 下策（应急）：{description}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## 一键排障脚本

### 快速网络诊断脚本（PowerShell）

```powershell
# Save as: NetDiag.ps1
# Usage: .\NetDiag.ps1 [-Target <hostname>] [-OutputDir <path>]

param(
    [string]$Target = "8.8.8.8",
    [string]$OutputDir = "$env:TEMP\NetDiag-$(Get-Date -Format 'yyyyMMdd-HHmmss')"
)

New-Item -ItemType Directory -Path $OutputDir -Force | Out-Null

Write-Host "=== 网络快速诊断 ===" -ForegroundColor Cyan
Write-Host "输出目录: $OutputDir`n"

# 1. 网络适配器状态
Write-Host "[1/7] 网络适配器..." -ForegroundColor Yellow
Get-NetAdapter | Select-Object Name, Status, LinkSpeed, MacAddress | Tee-Object -Variable adapters
$adapters | Out-File "$OutputDir\adapters.txt"

# 2. IP配置
Write-Host "[2/7] IP配置..." -ForegroundColor Yellow
Get-NetIPConfiguration | Out-File "$OutputDir\ipconfig.txt"

# 3. DNS配置与缓存
Write-Host "[3/7] DNS..." -ForegroundColor Yellow
Get-DnsClientServerAddress | Out-File "$OutputDir\dns-servers.txt"
Get-DnsClientCache | Out-File "$OutputDir\dns-cache.txt"

# 4. 连接测试
Write-Host "[4/7] 连接测试 ($Target)..." -ForegroundColor Yellow
Test-NetConnection -ComputerName $Target -Port 443 | Out-File "$OutputDir\connectivity.txt"

# 5. 活跃连接
Write-Host "[5/7] 活跃连接..." -ForegroundColor Yellow
Get-NetTCPConnection | Where-Object State -eq "Established" | Select-Object LocalAddress, LocalPort, RemoteAddress, RemotePort, OwningProcess | Out-File "$OutputDir\connections.txt"

# 6. 路由表
Write-Host "[6/7] 路由表..." -ForegroundColor Yellow
Get-NetRoute | Out-File "$OutputDir\routes.txt"

# 7. ARP缓存
Write-Host "[7/7] ARP缓存..." -ForegroundColor Yellow
Get-NetNeighbor | Out-File "$OutputDir\arp.txt"

Write-Host "`n=== 诊断完成 ===" -ForegroundColor Green
Write-Host "结果保存在: $OutputDir" -ForegroundColor Green
```

### 证据一键采集脚本（排障前执行）

```powershell
# Save as: Collect-Evidence.ps1
# 在开始排障前执行，保留现场快照

param([string]$CaseDir = "$env:TEMP\Case-$(Get-Date -Format 'yyyyMMdd-HHmmss')")

$dirs = @("netstat", "dns", "routes", "arp", "services", "events", "firewall")
$dirs | ForEach-Object { New-Item -ItemType Directory -Path "$CaseDir\$_" -Force | Out-Null }

# 网络状态快照
netstat -ano > "$CaseDir\netstat\netstat-ano.txt"
netstat -s > "$CaseDir\netstat\netstat-stats.txt"

# DNS
ipconfig /displaydns > "$CaseDir\dns\dns-cache.txt"
Get-DnsClientServerAddress > "$CaseDir\dns\dns-servers.txt"

# 路由
route print > "$CaseDir\routes\route-print.txt"

# ARP
arp -a > "$CaseDir\arp\arp-cache.txt"

# 关键服务状态
Get-Service | Where-Object { $_.Name -match "dhcp|dns|winhttp|wlan|lanman" } > "$CaseDir\services\key-services.txt"

# 最近1小时网络相关事件
Get-WinEvent -FilterHashtable @{LogName='System'; Id=3,41,1014,1040; StartTime=(Get-Date).AddHours(-1)} -ErrorAction SilentlyContinue > "$CaseDir\events\network-events.txt"

# 防火墙状态
netsh advfirewall show allprofiles > "$CaseDir\firewall\fw-status.txt"

Write-Host "证据已采集到: $CaseDir"
```

## 失败模式与 Fallback 分支表

| 触发条件 | 一线修复 | 仍失败兜底 |
|---------|---------|-----------|
| TCPView 显示 SYN_SENT 停留 | 检查目标端口是否监听 + 防火墙规则 | 用 Procmon 看 ConnectEx 返回码；抓包确认是否有 SYN+ACK 回来 |
| Test-NetConnection 超时 | 检查本地防火墙出站规则 + 路由表 `Get-NetRoute` | 用 PsPing 从另一台机器测同一端口，排除本机问题 |
| DNS 解析到错误 IP | `ipconfig /flushdns` + 检查 hosts 文件 | 用 Procmon 过滤 Dnscache 看实际查询结果；检查安全软件是否 MITM |
| netsh winsock reset 后仍异常 | 确认已重启（Winsock 重置需重启生效） | 检查第三方 LSP 是否残留：`netsh winsock show catalog` 对比干净系统 |
| APIPA 169.254.x.x 持续出现 | 重启 DHCP Client 服务 + 检查网线/交换机端口 | 抓包看 DHCP Discover 是否发出、是否有 DHCP Offer 回来 |
| Procmon 抓不到网络事件 | 确认过滤器设置（Operation contains TCP/UDP） | 检查 WFP 驱动是否拦截：`netsh wfp show state` |
| 抓包看不到预期流量 | 确认抓包位置（本机 vs 网关）和过滤器 | 检查是否走了 VPN 隧道（流量被封装）；检查 IPv4 vs IPv6 |

## 工具速查卡

### TCPView — 网络连接实时监控
```cmd
# GUI 模式：实时查看所有 TCP/UDP 连接
tcpview.exe

# 命令行模式
tcpvcon -a -c -s           # 所有连接，CSV 格式，含状态
tcpvcon -a -c -s | find "ESTABLISHED" > connections.log
tcpvcon -a -n "chrome.exe"  # 监控特定进程
```

### Process Monitor — 网络操作深度监控
```cmd
# 启动时只启用网络监控
procmon.exe /AcceptEula /BackingFile C:\Temp\netlog.pml

# 网络相关过滤器
# Operation contains: ConnectEx, WSAConnect, sendto, WSASend, recvfrom, WSARecv
# Path contains: \Device\Afd
# Process Name is: dns.exe / svchost.exe (DNS Client)
```

### PsPing — 网络延迟与带宽测试
```cmd
psping -l 8k -n 100 target:80       # TCP Ping，8KB 包，100 次
psping -l 8k -n 100 -h target:80    # 带直方图
psping -s target:443 -u              # UDP 模式
```

### Sysmon — 网络连接持久记录
```
# Event ID 3: 网络连接（记录进程、目标 IP/端口、连接时间）
# Event ID 22: DNS 查询（记录域名和解析结果）
```

### PowerShell 现代排障命令
```powershell
# 替代 netstat
Get-NetTCPConnection | Select-Object LocalAddress, LocalPort, RemoteAddress, RemotePort, State, OwningProcess

# 替代 telnet（端口测试）
Test-NetConnection -ComputerName target -Port 443

# DNS 查询
Resolve-DnsName -Name example.com -Type A

# 查看路由表
Get-NetRoute

# 查看 DNS 缓存
Get-DnsClientCache
```

---

## 常见网络故障排障指南

### 故障 1：169.254.x.x APIPA 地址问题

**根因**：DHCP 不可用时，Windows 自动分配链路本地地址。

**检查点**：
```cmd
ipconfig /all                    # 查看 DHCP 已启用 / 自动配置已启用
sc query dhcp                    # 检查 DHCP Client 服务状态
reg query "HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters" /v IPAutoconfigurationEnabled
```

**修复方法**：
```cmd
# 方法 1：设置静态 IP（绕过 DHCP）
netsh interface ipv4 set address "Ethernet1" static 192.168.x.x 255.255.255.0 192.168.x.1

# 方法 2：禁用 APIPA 自动配置（注册表）
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters" /v IPAutoconfigurationEnabled /t REG_DWORD /d 0 /f
# 需要重启网卡或重启电脑生效

# 方法 3：重置网络栈
netsh winsock reset catalog
netsh int ip reset reset.log
```

**注册表控制**：
| 注册表值 | 作用 | 默认值 |
|---------|------|--------|
| `IPAutoconfigurationEnabled` | 0=禁用 APIPA | 1（启用） |
| `DhcpIPAddress` | DHCP 获取的 IP | - |
| `SubnetMask` | 子网掩码 | - |

### 故障 2：DNS 解析失败

**检查点**：
```cmd
nslookup example.com              # 测试 DNS 解析
ipconfig /displaydns              # 查看 DNS 缓存
ipconfig /flushdns                # 清除 DNS 缓存
Get-DnsClientCache | Format-Table # PowerShell 查看缓存
sc query dnscache                 # 检查 DNS Client 服务
```

**Procmon 过滤 DNS**：
```
Process Name is "svchost.exe" AND Path contains "Dnscache"
```

**修复方法**：
```cmd
# 刷新 DNS 缓存
ipconfig /flushdns

# 更换 DNS 服务器
netsh interface ipv4 set dns "Ethernet1" static 8.8.8.8

# 检查 hosts 文件
type %SystemRoot%\System32\drivers\etc\hosts

# 重置 DNS Client 服务
net stop dnscache && net start dnscache
```

### 故障 3：端口被占用

**检查点**：
```cmd
netstat -ano | findstr :80         # 查看占用 80 端口的 PID
TCPView GUI                        # 可视化查看
Get-NetTCPConnection -LocalPort 80 # PowerShell
```

**修复方法**：
```cmd
# 找到占用进程并终止
taskkill /PID <pid> /F

# 或通过 TCPView 右键 → Close Connection
```

### 故障 4：网络慢 / 高延迟

**检查点**：
```cmd
psping -l 8k -n 100 target:80     # 测试延迟和丢包
pathping target                    # 路径追踪 + 丢包统计
netstat -s | findstr "Retrans"     # TCP 重传统计
```

**Procmon 分析**：
- 寻找时间戳间隙（>1 秒的间隔）
- 检查是否有大量 TCP 重传
- 检查 DNS 查询超时

**常见原因**：
- TCP 窗口缩放问题
- MTU 不匹配导致分片
- DNS 查询超时（CRL 检查、证书验证）
- MMCSS 服务限制网络吞吐量（Vista+）
- 安全软件/代理干扰

### 故障 5：Winsock 损坏

**症状**：网络功能异常但配置正确，应用报 socket 错误。

**检查点**：
```cmd
netsh winsock show catalog          # 查看 Winsock 目录
```

**修复方法**：
```cmd
netsh winsock reset catalog         # 重置 Winsock 目录
netsh int ip reset reset.log        # 重置 TCP/IP 栈
# 重启电脑生效
```

### 故障 6：防火墙/安全软件阻断

**检查点**：
```cmd
netsh advfirewall show allprofiles  # 查看防火墙状态
netsh advfirewall firewall show rule name=all | findstr "规则名"  # 查看规则
```

**Procmon 过滤**：
```
Process Name contains "MsMpEng" OR Process Name contains "security"
```

**关键提醒**：安全软件可能在 WFP（Windows Filtering Platform）层面拦截，不产生可见的防火墙日志。

---

## 安全攻防排障（被攻击场景）

当排障发现异常模式无法用"配置错误"解释时，考虑被攻击的可能。

### ARP 欺骗检测

**症状**：网络间歇性断连、同网段部分通信异常、网关MAC地址变化

**检测方法**：
```powershell
# 查看ARP缓存，检查网关MAC是否一致
arp -a | findstr "网关IP"

# 对比：正常机上执行同样的命令，对比网关MAC地址
# 如果不一致 → 正在被ARP欺骗

# 持续监控ARP变化（PowerShell）
while($true) {
    $arp = arp -a | Select-String "网关IP"
    Write-Host "$(Get-Date -Format 'HH:mm:ss') - $arp"
    Start-Sleep -Seconds 2
}
```

**修复**：
```powershell
# 静态绑定正确的网关ARP（防止被覆盖）
netsh interface ipv4 add neighbors "以太网" "网关IP" "正确MAC"
# 需要管理员权限
```

### DNS 投毒/劫持检测

**症状**：nslookup 返回错误IP、不同DNS服务器返回不同结果、HTTPS证书警告

**检测方法**：
```powershell
# 对比多个DNS服务器的解析结果
Resolve-DnsName -Name target.com -Server 8.8.8.8    # Google DNS
Resolve-DnsName -Name target.com -Server 1.1.1.1    # Cloudflare
Resolve-DnsName -Name target.com -Server 当地DNS     # 本地DNS

# 如果结果不一致 → 可能被投毒

# 检查hosts文件是否被篡改
Get-Content "$env:SystemRoot\System32\drivers\etc\hosts" | Select-String -Pattern "target"

# 检查DNS缓存是否有异常
Get-DnsClientCache | Where-Object { $_.Entry -like "*target*" }
```

**修复**：
```powershell
# 清除DNS缓存
ipconfig /flushdns

# 切换到可信DNS
netsh interface ipv4 set dns "以太网" static 8.8.8.8

# 检查并清理hosts文件
notepad "$env:SystemRoot\System32\drivers\etc\hosts"
```

### 中间人攻击（MITM）检测

**症状**：HTTPS证书不受信任、证书颁发者异常、同网段流量异常大

**检测方法**：
```powershell
# 检查HTTPS证书是否正常
$cert = (Invoke-WebRequest -Uri "https://target.com" -UseBasicParsing).BaseResponse
# 如果报证书错误 → 可能被MITM

# 检查是否有TLS拦截（安全软件常见）
Get-Process | Where-Object { $_.ProcessName -match "McAfee|Norton|Kaspersky|360|火绒" }

# 检查WFP（Windows Filtering Platform）是否有异常过滤驱动
netsh wfp show state
# 查看是否有非微软的过滤驱动
```

### 网络层异常模式速查

| 异常模式 | 可能原因 | 排障方向 |
|---------|---------|---------|
| 同网段部分IP不通但网关通 | ARP欺骗 | 检查ARP缓存一致性 |
| 所有HTTPS网站报证书错误 | TLS MITM | 检查安全软件/代理 |
| DNS解析随机失败 | DNS缓存投毒 | flushdns + 换DNS |
| 流量被重定向到陌生IP | DNS劫持 | 对比多DNS解析结果 |
| 网卡混杂模式 | 抓包/嗅探 | `Get-NetAdapter | Where PromiscuousMode` |
| 异常出站连接 | 木马/C2 | TCPView 检查陌生外连 |

## 网络栈内部机制速查

### TCP 状态机速查

```
                    主动打开
        ┌───────────────────────────┐
        │                           ▼
    ┌───────┐   被动打开      ┌───────────┐
    │CLOSED│ ──────────────→ │  LISTEN   │
    └───────┘                 └───────────┘
        │                          │ 收到SYN
        │ 主动打开                  ▼
        │ 发送SYN            ┌───────────┐
        ▼                    │SYN_RECEIVED│
    ┌───────────┐            └───────────┘
    │ SYN_SENT  │                  │ 收到ACK
    └───────────┘                  ▼
        │ 收到SYN+ACK       ┌───────────────┐
        ▼                   │  ESTABLISHED  │ ← 正常数据传输
    ┌───────────────┐       └───────────────┘
    │  ESTABLISHED  │              │
    └───────────────┘              │ 被动关闭
                                   │ 收到FIN
        ┌──────────────────────────┘
        ▼
    ┌───────────┐  收到FIN    ┌──────────────┐
    │CLOSE_WAIT │ ──────────→ │ LAST_ACK     │
    └───────────┘  发送FIN    └──────────────┘
        │ 发送FIN                   │ 收到ACK
        ▼                           ▼
    ┌───────────┐            ┌──────────┐
    │ TIME_WAIT │            │  CLOSED  │
    └───────────┘            └──────────┘
        │ 等待2MSL
        ▼
    ┌──────────┐
    │  CLOSED  │
    └──────────┘
```

### 各状态排障含义

| 状态 | 含义 | 异常信号 | 排障方向 |
|------|------|---------|---------|
| **ESTABLISHED** | 正常连接 | 连接数异常多 | 检查是否被扫描或DDoS |
| **SYN_SENT** | 等待对方响应 | 大量堆积 | 目标端口未开/防火墙丢包 |
| **SYN_RECEIVED** | 收到SYN等待ACK | 大量堆积 | SYN Flood攻击 |
| **TIME_WAIT** | 等待2MSL后关闭 | 大量堆积（>1000） | 端口耗尽风险，检查连接池配置 |
| **CLOSE_WAIT** | 对方已关闭，本地未关 | 大量堆积 | 应用未正确关闭socket（代码bug） |
| **FIN_WAIT_1/2** | 主动关闭等待 | 大量堆积 | 对方未响应FIN |
| **LISTENING** | 监听中 | 端口对但连不上 | 检查防火墙/绑定地址 |

### TIME_WAIT 堆积处理

```powershell
# 查看TIME_WAIT数量
(Get-NetTCPConnection -State TimeWait).Count

# 如果过多，调整注册表（谨慎！）
# 缩短TIME_WAIT超时（默认240秒）
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters" /v TcpTimedWaitDelay /t REG_DWORD /d 30 /f

# 增大临时端口范围
netsh int ipv4 set dynamicport tcp start=10250 num=55000
```

### CLOSE_WAIT 堆积处理

CLOSE_WAIT 堆积 = 应用层bug（收到FIN但没调close）

```powershell
# 找出哪些进程有CLOSE_WAIT
Get-NetTCPConnection -State CloseWait | Group-Object OwningProcess | ForEach-Object {
    $proc = Get-Process -Id $_.Name -ErrorAction SilentlyContinue
    [PSCustomObject]@{
        Process = $proc.ProcessName
        PID = $_.Name
        Count = $_.Count
    }
} | Sort-Object Count -Descending

# → 反馈给应用组修复socket关闭逻辑
```

### 数据包路径（发送方向）
```
应用层 → Winsock (WS2_32.dll) → AFD.sys → Tcpip.sys (TCP/UDP) → IPsec (WFP) → NDIS 协议驱动 → 微端口驱动 → 网卡
```

### 数据包路径（接收方向）
```
网卡 → 微端口驱动 → NDIS 过滤驱动 → Tcpip.sys → AFD.sys → Winsock → 应用层
```

### 关键组件
| 组件 | 位置 | 作用 |
|------|------|------|
| WS2_32.dll | 用户态 | Winsock API |
| AFD.sys | 内核态 | Ancillary Function Driver，处理 Winsock 请求 |
| Tcpip.sys | 内核态 | TCP/IP 协议驱动 |
| DNSAPI.dll | 用户态 | DNS 解析 API |
| Dnscache | 服务 | DNS Client 缓存服务 |
| Dhcp | 服务 | DHCP Client 服务 |
| NDIS | 内核态 | 网络驱动接口规范 |
| WFP | 内核态 | Windows Filtering Platform（防火墙框架） |

### DNS 解析流程
```
应用调用 getaddrinfo()
  → DNSAPI.dll（本地缓存检查）
  → DNS Client 服务 (svchost.exe -k NetworkService)
  → 检查 hosts 文件
  → 发送 DNS 查询到配置的 DNS 服务器
  → 超时重试（默认 10 秒超时）
  → 返回结果，缓存（TTL 受 MaxCacheTtl 控制）
```

### APIPA 自动配置机制
```
DHCP 失败 → tcpip.sys 自动配置模块
  → 在 169.254.0.0/16 范围随机选择地址
  → ARP 探测（确认无冲突）
  → 分配为链路本地地址
  → 标记为"首选"
```

---

## IPv6 排障要点

现代 Windows 默认启用 IPv6，排障时必须同时考虑。

### IPv6 vs IPv4 排障差异

| 项目 | IPv4 | IPv6 |
|------|------|------|
| 地址格式 | 192.168.1.1 | fe80::1 或 2001:db8::1 |
| 配置方式 | DHCP / 静态 | SLAAC / DHCPv6 / 静态 |
| DNS 记录 | A 记录 | AAAA 记录 |
| 本地地址 | 127.0.0.1 | ::1 |
| 链路本地 | 169.254.x.x (APIPA) | fe80::/10 |
| 端口查看 | netstat -ano | netstat -ano（同时显示） |

### IPv6 常见问题

```powershell
# 查看IPv6地址配置
Get-NetIPAddress -AddressFamily IPv6

# 测试IPv6连通性
Test-NetConnection -ComputerName target -InformationLevel Detailed
# 注意：如果目标只有AAAA记录，会自动走IPv6

# 禁用IPv6（排障时临时排除IPv6因素）
Disable-NetAdapterBinding -Name "以太网" -ComponentID ms_tcpip6
# 恢复：
Enable-NetAdapterBinding -Name "以太网" -ComponentID ms_tcpip6

# 查看IPv6路由
Get-NetRoute -AddressFamily IPv6

# IPv6 DNS查询
Resolve-DnsName -Name target.com -Type AAAA
```

### IPv6 特有陷阱

| 陷阱 | 现象 | 解决 |
|------|------|------|
| IPv6优先但不可用 | ping走IPv6超时，IPv4正常 | 调整前缀策略或临时禁用IPv6 |
| 链路本地地址冲突 | fe80地址重复 | 检查网卡配置，重启网卡 |
| Teredo/6to4隧道问题 | 间歇性IPv6连接 | 禁用隧道适配器 |
| DHCPv6不响应 | 无全局IPv6地址 | 检查路由器/交换机IPv6配置 |

## 关键注册表速查

### TCP/IP 核心参数
```
路径：HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters

DefaultTTL          = 128        # IP 包 TTL
KeepAliveTime       = 7200000    # Keepalive 间隔（2小时，毫秒）
KeepAliveInterval   = 1000       # Keepalive 重传间隔（毫秒）
TcpMaxConnectRetransmissions = 2 # TCP 连接最大重传次数
EnableDeadGWDetect  = 1          # 死网关检测
EnablePMTUDiscovery = 1          # 路径 MTU 发现
```

### APIPA 控制
```
路径：HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters

IPAutoconfigurationEnabled = 0   # 禁用 APIPA
```

### DNS Client
```
路径：HKLM\SYSTEM\CurrentControlSet\Services\Dnscache\Parameters

MaxCacheTtl         = 86400      # DNS 缓存最大 TTL（秒）
MaxNegativeCacheTtl = 900        # 否定缓存 TTL（15分钟）
```

### Winsock
```
路径：HKLM\SYSTEM\CurrentControlSet\Services\Winsock

# Winsock 目录损坏时：netsh winsock reset catalog
```

---

## 排障陷阱（Russinovich 警告）

1. **清单式排障**：按固定清单逐项检查，不根据证据调整方向
2. **经验移植**：上次这个方法管用，这次也一定管用
3. **只看错误不看上下文**：孤立地看错误日志，不看前后操作
4. **工具迷信**：以为有了好工具就一定能找到问题
5. **用错工具找错问题**：用 netstat 排查 DNS 问题，用 Procmon 排查硬件故障

---

## Windows 11/Server 2025 新特性

| 特性 | 说明 | 排障影响 |
|------|------|---------|
| **SMB over QUIC** | SMB 通过 QUIC (UDP 443) 传输，WAN 场景吞吐量提升 ~135% | 需要新的抓包策略（UDP 而非 TCP 445） |
| **DNS over HTTPS (DoH)** | 原生支持加密 DNS | DNS 流量加密，Procmon 无法直接看到 DNS 查询内容 |
| **DNR** | 自动发现加密 DNS 服务器 | DNS 配置自动化，减少手动配置错误 |
| **Wi-Fi 7** | 802.11be 支持 | 新的无线排障维度 |
| **PowerShell 现代化** | Test-NetConnection / Get-NetTCPConnection 替代 netsh/netstat | 优先使用 PowerShell cmdlet |

---

## 受限环境排障指南

当没有完整管理工具时的替代方案。

### 场景 1：Server Core（无GUI）

Server Core 没有 TCPView、Procmon 等 GUI 工具，全靠命令行：

```powershell
# 替代 TCPView
Get-NetTCPConnection | Select-Object LocalAddress, LocalPort, RemoteAddress, RemotePort, State, OwningProcess | Format-Table -AutoSize

# 替代 netstat -anb（看进程名）
Get-NetTCPConnection | ForEach-Object {
    $proc = Get-Process -Id $_.OwningProcess -ErrorAction SilentlyContinue
    [PSCustomObject]@{
        Local = "$($_.LocalAddress):$($_.LocalPort)"
        Remote = "$($_.RemoteAddress):$($_.RemotePort)"
        State = $_.State
        Process = $proc.ProcessName
        PID = $_.OwningProcess
    }
} | Format-Table -AutoSize

# 替代 Test-NetConnection（快速版，不等DNS）
Test-NetConnection -ComputerName target -Port 443 -InformationLevel Quiet
```

### 场景 2：无管理员权限

部分命令需要管理员权限，受限用户可用的替代：

```powershell
# 不需要管理员的诊断命令
ipconfig /all                    # 网络配置
nslookup target.com             # DNS 测试
ping target.com                 # 连通测试
netstat -an                     # 连接查看（不含进程名）
Get-DnsClientCache              # DNS 缓存（只读）

# 需要管理员但可以用 runas 尝试
runas /user:Administrator "powershell -Command Get-NetTCPConnection"
```

### 场景 3：PowerShell 执行策略受限

```powershell
# 检查当前执行策略
Get-ExecutionPolicy

# 绕过方式（不改系统策略）
powershell -ExecutionPolicy Bypass -File script.ps1

# 或者直接在命令行执行（不受策略限制）
# 策略只影响 .ps1 文件，不影响交互式命令
```

### 场景 4：只有 cmd.exe（无 PowerShell）

```cmd
:: 网络配置
ipconfig /all
route print
netstat -ano
arp -a
nslookup target.com

:: 端口占用
netstat -ano | findstr :80
tasklist /FI "PID eq <pid>"

:: 网络重置（不需要PowerShell）
netsh winsock reset catalog
netsh int ip reset reset.log
netsh interface ipv4 set dns "以太网" static 8.8.8.8
```

### 工具可用性矩阵

| 工具 | GUI完整版 | Server Core | 无管理员 | 只有cmd |
|------|----------|-------------|---------|---------|
| TCPView | ✅ | ❌ | ✅ | ❌ |
| Procmon | ✅ | ❌ | ❌ | ❌ |
| PowerShell Get-NetTCPConnection | ✅ | ✅ | ✅ | ❌ |
| netstat | ✅ | ✅ | ✅ | ✅ |
| Test-NetConnection | ✅ | ✅ | ✅ | ❌ |
| netsh | ✅ | ✅ | 部分 | ✅ |
| Wireshark | ✅ | ❌ | ❌ | ❌ |
| netsh trace | ✅ | ✅ | ❌ | ✅ |

## 协作排障框架

多人/多组协同排障时的分工与协作。

### 角色分工

| 角色 | 职责 | 典型操作 |
|------|------|---------|
| **指挥者** | 统筹排障方向，分配任务，汇总信息 | 决定下一步动作，主持排障会议 |
| **网络组** | 网络层排查 | 抓包、路由检查、防火墙规则 |
| **系统组** | 操作系统层排查 | 服务状态、事件日志、资源使用 |
| **应用组** | 应用层排查 | 应用日志、配置检查、端口监听 |
| **安全组** | 安全事件排查 | 入侵检测、日志审计、策略审查 |

### 协作流程

```
1. 指挥者 收集症状 → 初步分类 → 分配任务
2. 各组 并行采集证据 → 写入共享目录/文档
3. 指控者 汇总各组发现 → 关联分析 → 形成假设
4. 各组 验证假设 → 确认或排除
5. 指挥者 确认根因 → 协调修复 → 统一验证
```

### 共享证据目录结构

```
\\文件服务器\incidents\INC-YYYYMMDD-001\
├── timeline.md              # 统一时间线（所有组共同维护）
├── network\                # 网络组证据
│   ├── captures\
│   └── netstat-snapshots\
├── system\                 # 系统组证据
│   ├── eventlogs\
│   └── procmon\
├── application\            # 应用组证据
│   ├── app-logs\
│   └── config-backup\
├── security\               # 安全组证据
│   └── audit-logs\
└── notes\                  # 排障笔记
    └── findings.md
```

### 统一时间线规则

所有组使用统一格式记录发现：
```
[HH:mm:ss] [组名] [严重度] 发现内容
[14:32:15] [网络组] [HIGH] TCPView显示目标端口SYN_SENT堆积
[14:32:30] [系统组] [MED] 事件日志显示服务超时
```

## 不要做的事（反例黑名单）

| # | 危险动作 | 为什么不要做 | 正确做法 |
|---|---------|------------|---------|
| 1 | 直接 `netsh winsock reset` 不告知用户 | 重置会清除第三方 LSP（如安全软件网络过滤），可能导致应用断网 | 先展示 `netsh winsock show catalog` 结果，告知用户风险后再执行 |
| 2 | 修改注册表不备份 | 参数写错可能导致网络栈异常无法恢复 | `reg export "HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters" C:\Temp\reg-backup.reg` |
| 3 | 抓包时不做时间对齐 | 多工具日志时间不对齐，无法关联事件 | 统一用 UTC 时间；Procmon/Wireshark/事件日志三者时间戳需对齐 |
| 4 | 只看 netstat 不看进程 | `netstat -ano` 给 PID 但不给进程名，可能误杀 | 用 `netstat -anb` 或 TCPView 直接看进程名 |
| 5 | 一次性重置网络栈全部组件 | `netsh int ip reset` + `netsh winsock reset` 同时执行，问题定位不清 | 逐步重置：先 winsock → 验证 → 再 ip reset → 验证 |
| 6 | 忽略安全软件直接排障 | 防火墙/EDR 可能在 WFP 层面拦截，不产生可见日志 | 先确认安全软件状态，必要时临时禁用测试（告知用户风险） |
| 7 | 用 ping 判断网络是否正常 | ping (ICMP) 可能被防火墙放行但 TCP 被阻断 | 用 `Test-NetConnection -Port` 测试实际业务端口 |
| 8 | 按固定清单机械执行 | 不根据中间证据调整方向，浪费时间 | 每步结果决定下一步：如果 Step 2 已定位问题，直接跳到 Step 5 |

## Windows 跨版本兼容矩阵

| 特性/命令 | Win 10 | Win 11 24H2 | Server 2019 | Server 2022 | Server 2025 |
|-----------|--------|-------------|-------------|-------------|-------------|
| Get-NetTCPConnection | ✅ | ✅ | ✅ | ✅ | ✅ |
| Test-NetConnection | ✅ | ✅ | ✅ | ✅ | ✅ |
| netsh trace | ✅ | ✅ | ✅ | ✅ | ✅ |
| Get-DnsClientCache | ✅ | ✅ | ✅ | ✅ | ✅ |
| SMB over QUIC | ❌ | ✅ | ❌ | ❌ | ✅ |
| DNS over HTTPS | ❌ | ✅ | ❌ | ❌ | ✅ |
| Wi-Fi 7 | ❌ | ✅ | ❌ | ❌ | ❌ |
| 默认 PowerShell | 5.1 | 5.1/7.x | 5.1 | 5.1 | 7.x |

### 版本相关注意事项

- **Server Core**：无GUI，所有操作必须命令行或远程
- **Windows 10 早期版本**：Get-NetAdapter 可能不可用，用 `netsh interface show interface` 替代
- **Server 2019 vs 2022**：防火墙日志路径可能不同
- **注册表默认值差异**：同一注册表值在不同版本的默认值可能不同（如 KeepAliveTime）

## 诚实边界

- 本 Skill 聚焦 Windows 网络排障，不覆盖 Linux/macOS
- 网络物理层故障（网线、交换机、路由器硬件）不在范围内
- 注册表修改有风险，需谨慎操作并提前备份
- 部分高级内核级排障（如 WinDbg 分析网络驱动崩溃）需要额外专业知识
- Windows 版本差异较大，具体注册表值和行为可能因版本而异
- 调研时间：2026-06-12，基于 Windows 11 24H2/25H2 和 Server 2025

---

## 调研来源

### 一手来源（★★★★★）
- Microsoft Learn - Sysinternals 官方文档
- Microsoft Learn - Windows Internals 文档
- Microsoft Learn - TCP/IP and NBT configuration parameters (KB 314053)
- Mark Russinovich TechNet 博客（归档至 learn.microsoft.com）
- Case of the Unexplained 系列演讲（YouTube 官方视频）
- Pushing the Limits of Windows 系列文章

### 二手来源（★★★★）
- CSDN 技术博客（基于官方资料的实战总结）
- TheITBros 技术社区
- Windows Central 权威 Windows 媒体

### 排除来源
- 知乎、微信公众号、百度百科（信息失真率高）

---

> 本 Skill 由 女娲 · Skill造人术 生成
> 蒸馏对象：Mark Russinovich（Microsoft Azure CTO / Sysinternals 创始人）

---

## 质量审计框架（借鉴前端审计思维）

> 借鉴前端质量审计（audit）思维，建立网络排障质量审计框架。核心理念：系统化评估 → 问题分类 → 改进建议。

### 审计维度

| 维度 | 评估内容 | 权重 |
|------|----------|------|
| **功能完整性** | 排障流程是否完整、工具是否齐全 | 30% |
| **性能表现** | 排障效率、响应时间、资源占用 | 25% |
| **用户体验** | 排障流程是否清晰、操作是否简便 | 20% |
| **健壮性** | 错误处理、降级策略、恢复机制 | 25% |

### 审计方法

```yaml
质量审计方法:
  1. 检查清单审计:
     - 排障流程清单
     - 工具使用清单
     - 验证步骤清单
  
  2. 评分标准审计:
     - 功能完整性评分
     - 性能表现评分
     - 用户体验评分
     - 健壮性评分
  
  3. 问题分类审计:
     - 功能问题
     - 性能问题
     - 用户体验问题
     - 健壮性问题
```

### 审计输出

```yaml
质量审计输出:
  1. 审计报告:
     - 审计维度得分
     - 总体得分
     - 改进建议
  
  2. 问题优先级:
     - P0: 功能缺陷
     - P1: 性能问题
     - P2: 用户体验问题
     - P3: 健壮性问题
  
  3. 改进建议:
     - 短期改进（1-2周）
     - 中期改进（1-2月）
     - 长期改进（3-6月）
```

### 审计应用示例

```yaml
网络排障质量审计示例:
  审计维度:
    - 功能完整性: 85分（排障流程完整，工具齐全）
    - 性能表现: 75分（排障效率一般，响应时间较长）
    - 用户体验: 80分（流程清晰，操作简便）
    - 健壮性: 70分（错误处理一般，降级策略不足）
  
  总体得分: 77.5分
  
  改进建议:
    - P0: 完善错误处理机制
    - P1: 优化排障流程，提高效率
    - P2: 增加用户引导，提升用户体验
    - P3: 增加固性策略，提高健壮性
```


> **📌 约束体系已融入正文**：角色边界 → Phase 0；输入校验 → Phase 0；TODO机制 → 散布在各 CHECKPOINT 中；结构化输出 → Phase 8 最终报告；决策查表 → Step 1 问题分类表。无需额外查阅。
