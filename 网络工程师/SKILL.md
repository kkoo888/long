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

## 排障工作流（五步法）

### Step 1: 问题分类

| 类型 | 典型表现 | 第一动作 |
|------|---------|---------|
| **连接失败** | 无法访问目标、连接超时 | TCPView 查看连接状态 + Test-NetConnection 测试端口 |
| **DNS 问题** | 域名解析失败、解析到错误 IP | Resolve-DnsName + Procmon 过滤 DNS Client |
| **端口冲突** | 端口已被占用、服务无法启动 | TCPView/netstat 定位占用进程 |
| **网络慢** | 传输速度低、延迟高 | 抓包分析重传/窗口大小/拥塞 |
| **间歇性断连** | 时好时坏、偶发超时 | Sysmon 持续记录 + 事件日志关联 |
| **169.254.x.x** | APIPA 地址、自动配置问题 | 检查 DHCP 服务 + 注册表 APIPA 控制 |

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

### Step 3: 数据采集

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
```

**TCPView 关注点**：
- ESTABLISHED 连接是否合理
- TIME_WAIT 堆积（可能端口耗尽）
- SYN_SENT 停留（连接建立失败）
- 状态为 LISTENING 但端口不对

### Step 5: 修复与总结
- 修复根因，不只是恢复现象
- 记录：问题现象 → 排障路径 → 根因 → 修复方法
- 区分 Workaround（绕过）和 Root Cause Fix（根因修复）

---

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

## 网络栈内部机制速查

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
