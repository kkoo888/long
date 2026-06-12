# Sysinternals 网络排障工具体系调研

> 调研日期: 2026-06-12
> 调研范围: Mark Russinovich 创立的 Sysinternals 工具集中与网络排障相关的所有工具

---

## 一、Sysinternals 网络相关工具完整清单

以下列表基于 Microsoft Learn 官方 Sysinternals 工具索引页。分为「直接网络工具」和「间接网络相关工具」两类。

### 1.1 直接网络工具（官方分类为 Networking Utilities）

| 工具 | 功能 | 官方分类 |
|------|------|----------|
| **TCPView** | 实时显示所有 TCP/UDP 终结点、状态、关联进程 | 网络工具 |
| **PsPing** | ICMP/TCP/UDP Ping、延迟测试、带宽测试 | 网络工具 |
| **PsFile** | 显示远程打开的文件（通过 SMB 连接） | 网络工具 |
| **Whois** | 查询域名/IP 的 WHOIS 归属信息 | 网络工具 |
| **ShareEnum** | 扫描网络文件共享并查看安全设置 | 网络工具 |
| **PipeList** | 显示系统命名管道（含实例数） | 网络工具 |
| **AdExplorer** | Active Directory 高级查看/编辑器 | 网络工具 |
| **AdInsight** | LDAP 实时监控，排障 AD 客户端应用 | 网络工具 |
| **AdRestore** | 恢复已删除的 AD 对象 | 网络工具 |

**来源**: https://learn.microsoft.com/en-us/sysinternals/downloads/networking-utilities
**可信度**: ★★★★★（一手来源，Microsoft 官方文档）

### 1.2 间接网络相关工具

| 工具 | 网络相关功能 |
|------|-------------|
| **Process Monitor (Procmon)** | 通过 Winsock 钩子监控网络操作（TCP/UDP 连接、DNS 查询、send/recv） |
| **Process Explorer** | 显示进程的 TCP/IP 连接、句柄、DLL 加载、线程等待状态 |
| **ProcDump** | 抓取网络应用崩溃/挂起时的 dump 文件 |
| **PsExec** | 远程执行命令，远程网络排障 |
| **Sysmon** | 记录网络连接事件（Event ID 3）、DNS 查询（Event ID 22）到 Windows 事件日志 |
| **Handle** | 查看进程打开的句柄（含网络套接字句柄） |
| **ListDLLs** | 列出加载的 DLL（可发现网络相关 DLL 注入） |
| **PortMon** | 监控串口/并口活动（旧式网络设备调试） |
| **BgInfo** | 桌面显示 IP 地址、网络适配器等信息 |
| **RDCMan** | 管理多个远程桌面连接 |
| **DebugView** | 捕获网络驱动的 DbgPrint 调试输出 |

---

## 二、核心工具详细分析

### 2.1 TCPView — 网络连接实时监控

#### 基本功能
TCPView 是 Sysinternals 中最直接的网络连接查看工具，功能远超 Windows 内置的 `netstat`。

- **实时显示**所有 TCP 和 UDP 终结点
- 显示**本地地址、远程地址、连接状态**
- 显示**拥有连接的进程名和 PID**
- 支持自动刷新（默认 1 秒）
- 支持 IP 地址反向 DNS 解析

**来源**: https://learn.microsoft.com/en-us/sysinternals/downloads/tcpview
**可信度**: ★★★★★（一手来源）

#### 高级用法

1. **命令行版本 Tcpvcon**
   ```
   tcpvcon -a -c -s
   ```
   - `-a`: 显示所有连接（含未关联进程的）
   - `-c`: CSV 格式输出（便于脚本解析）
   - `-s`: 打印 TCP 连接状态

   批量导出示例：
   ```cmd
   tcpvcon -a -c -s | find "ESTABLISHED" > connections.log
   ```

   监控特定进程：
   ```cmd
   tcpvcon -a -n "chrome.exe" -o 30
   ```

   **来源**: CSDN 博客实战案例 + Microsoft Learn 官方文档
   **可信度**: ★★★★☆（官方文档确认 Tcpvcon 功能，实战示例为二手来源）

2. **GUI 高级技巧**
   - **Options → Resolve Addresses**: 启用 DNS 反向解析（帮助识别远程服务器）
   - **View → Update Speed**: 调整刷新频率（排查时建议 1 秒）
   - **右键菜单**: 可直接 Ping 远程主机、关闭连接、查看进程属性
   - **高亮显示**: 新连接以绿色高亮，断开的连接以红色高亮（帮助发现异常新连接）
   - **进程属性集成**: 右键选择 "Process Properties" 可直接跳转到 Process Explorer 查看详情

   **来源**: Microsoft Learn 官方 + CSDN 实战分析
   **可信度**: ★★★★☆

3. **与 PsExec 结合远程扫描**
   ```cmd
   psexec @computers.txt -u domain\admin -p password -c tcpview64.exe /savelog \\server\logs\%COMPUTERNAME%.log
   ```

   **来源**: CSDN 博客实战
   **可信度**: ★★★☆☆（二手来源，企业实践方法）

4. **网络排障场景**
   - **恶意软件检测**: 发现异常外连（挖矿病毒、C2 通信）
   - **端口冲突排查**: 快速定位哪个进程占用了特定端口
   - **连接泄漏检测**: 观察 ESTABLISHED 连接是否持续增长
   - **防火墙规则验证**: 确认连接是否被正确阻断

---

### 2.2 Process Monitor (Procmon) — 网络操作深度监控

#### 网络监控原理
Procmon 通过挂钩 Winsock 内核驱动（如 `AFD.SYS`）及 RPC 运行时组件来捕获网络操作。它**不直接抓取网络数据包**（那是 Wireshark 的领域），但可以记录高层语义行为。

**关键网络相关操作**:
- `ConnectEx`、`WSAConnect`: TCP 连接建立
- `sendto`、`WSASend`: 数据发送
- `recvfrom`、`WSARecv`: 数据接收
- DNS 查询过程（通过监控 DNS Client 服务进程）
- 套接字创建和关闭

**来源**: Microsoft Learn 官方 + CSDN 技术分析
**可信度**: ★★★★★（官方确认文件系统/注册表/进程/线程/DLL 监控能力）；★★★★☆（网络操作细节来自技术分析）

#### 网络相关过滤器配置

1. **按操作类型过滤**
   - Operation 包含 `TCP` / `UDP` / `DNS`
   - Operation 包含 `Connect` / `Send` / `Receive`
   - Path 包含 `\Device\Afd` （AFD 是 Windows 的 Ancillary Function Driver，处理 Winsock 请求）

2. **按进程过滤**
   - Process Name is `dns.exe` — DNS 服务器活动
   - Process Name is `svchost.exe` AND Path contains `Dnscache` — DNS 客户端缓存
   - Process Name is `lsass.exe` — 认证相关网络活动

3. **典型过滤器组合**
   ```
   Process Name is "chrome.exe" AND Operation begins with "TCP"
   Process Name is "svchost.exe" AND Path contains "DNS"
   Result is "NAME NOT FOUND" AND Path contains "\\Device\\Afd"
   ```

4. **Boot Logging**
   - Procmon 支持启动阶段日志记录，可捕获从系统引导开始的所有网络操作
   - 适合排查开机后网络连接建立延迟的问题

**来源**: Microsoft Learn 官方 + CSDN 实战指南
**可信度**: ★★★★☆

#### 与 Wireshark 的互补关系
| 维度 | Process Monitor | Wireshark |
|------|----------------|-----------|
| 抓包层 | 应用层/Winsock API | 网络层/数据链路层 |
| 看到什么 | 哪个进程发起了什么网络操作 | 实际的网络数据包内容 |
| 关联性 | 可关联进程、线程、文件/注册表操作 | 专注网络协议分析 |
| 适用场景 | "谁在连接？为什么？" | "连接中传输了什么？" |

**来源**: 综合分析
**可信度**: ★★★★☆

---

### 2.3 Process Explorer — 进程级网络关联分析

#### 网络相关功能

1. **TCP/IP 连接查看**
   - 选中任意进程，底部面板可查看其所有 TCP/UDP 连接
   - 显示本地/远程地址、端口、连接状态
   - 功能类似 TCPView 但与进程树深度整合

2. **句柄（Handles）分析**
   - 右侧面板显示进程打开的所有内核对象句柄
   - 包含套接字句柄（`\Device\Afd\Endpoint`）、命名管道句柄
   - 可发现网络资源泄漏（未关闭的套接字句柄持续增长）

3. **DLL 加载分析**
   - 查看进程加载的所有 DLL
   - 可发现异常的网络相关 DLL（如 `wininet.dll`、`ws2_32.dll`、`winhttp.dll`）
   - 可检测 DLL 注入攻击（恶意 DLL 通过网络下载并注入）

4. **线程等待分析**
   - 查看每个线程的等待状态和调用栈
   - 可发现线程卡在网络 I/O 等待上（如 `ntdll!NtDeviceIoControlFile` → `tcpip.sys`）
   - 排查应用"假死"（UI 线程阻塞在网络调用上）

5. **Find Handle or DLL (Ctrl+F)**
   - 全局搜索，可搜索网络相关的句柄名或 DLL 名
   - 例如搜索 `ws2_32` 找到所有加载了 Winsock DLL 的进程

**来源**: Microsoft Learn 官方 + CSDN 技术分析
**可信度**: ★★★★☆

#### 网络排障场景
- **应用连接问题**: 查看应用进程的 TCP 连接状态，是否处于 SYN_SENT（连接超时）
- **服务依赖分析**: 通过句柄分析找到 svchost 进程实际使用的网络服务
- **安全审计**: 检查进程是否有异常的 DLL 注入和网络连接

---

### 2.4 ProcDump — 网络应用崩溃/挂起捕获

#### 网络排障相关用法

1. **捕获挂起的网络应用**
   ```cmd
   procdump -ma -h hang.exe
   ```
   - `-h`: 当进程的窗口未响应（hang）时抓取 dump
   - `-ma`: 完整内存 dump（含所有线程栈，可看到网络等待的线程）
   - 适用场景：Web 应用卡死、数据库连接超时、RPC 调用无响应

2. **捕获网络相关崩溃**
   ```cmd
   procdump -ma -e 1 w3wp.exe
   ```
   - `-e 1`: 在第一次异常时抓取 dump（含 first-chance exception）
   - 适用场景：Web 服务进程（IIS w3wp.exe）崩溃

3. **等待特定进程启动**
   ```cmd
   procdump -ma -w MyApp.exe
   ```
   - `-w`: 等待指定进程启动后附加
   - 适用场景：排障间歇性启动失败的网络服务

4. **基于性能计数器触发**
   ```cmd
   procdump outlook -s 10 -p "\Processor(_Total)\% Processor Time" 20
   ```
   - 当系统总 CPU 超过 20% 持续 10 秒时抓取 dump
   - 可用于网络负载高导致 CPU 飙升的场景

5. **注册为事后调试器（AeDebug）**
   ```cmd
   procdump -ma -i C:\dumps
   ```
   - 安装后，任何进程崩溃都会自动抓取 dump
   - 适合生产环境长期监控网络服务稳定性

**来源**: https://learn.microsoft.com/en-us/sysinternals/downloads/procdump + CSDN 实战
**可信度**: ★★★★★（参数和用法来自官方文档）；★★★★☆（场景应用来自实践经验）

#### 与 WinDbg 配合分析
ProcDump 生成的 dump 文件可在 WinDbg 中分析：
```
!analyze -v          # 自动分析崩溃原因
~*k                  # 查看所有线程栈（可发现卡在网络调用上的线程）
!locks               # 检查死锁（网络操作可能持有锁）
!tcpip               # 扩展命令查看 TCP/IP 状态
```

---

### 2.5 PsExec — 远程网络排障

#### 核心网络排障用法

1. **远程执行网络诊断命令**
   ```cmd
   psexec \\192.168.1.100 -u admin -p password cmd.exe /c "ipconfig /all && netstat -ano && nslookup"
   ```

2. **远程部署 TCPView/PsPing**
   ```cmd
   psexec \\remote-server -c psping.exe -t target:443
   ```
   - `-c`: 将本地程序复制到远程系统后执行

3. **远程运行 ProcDump**
   ```cmd
   psexec \\remote-server -s procdump -ma -h w3wp.exe C:\dumps\w3wp_hang.dmp
   ```
   - `-s`: 以 SYSTEM 账户运行（有权限抓取任何进程）

4. **远程检查网络配置**
   ```cmd
   psexec \\remote-server cmd.exe /c "route print && arp -a && nbtstat -n"
   ```

5. **远程服务重启**
   ```cmd
   psexec \\remote-server cmd.exe /c "net stop Dnscache && net start Dnscache"
   ```

**前提条件**:
- 目标机器需开放 ADMIN$ 共享（445 端口）
- 防火墙允许 SMB 通信
- 域环境可能需要 `-h` 参数提升权限

**来源**: https://learn.microsoft.com/en-us/sysinternals/downloads/psexec + CSDN 实战
**可信度**: ★★★★★（核心用法来自官方）；★★★★☆（实战技巧来自社区）

---

### 2.6 PsPing — 网络性能测试

#### 四种测试模式

| 模式 | 触发参数 | 功能 |
|------|---------|------|
| ICMP Ping | 目标为 IP 或主机名（无端口） | 测试主机可达性与基本延迟 |
| TCP Ping | 目标为 `主机:端口` | 测试 TCP 端口连通性与延迟 |
| TCP 延迟测试 | 目标为 `主机:端口` + `-l` | 精确 TCP 延迟统计（含直方图） |
| 带宽测试 | 目标为 `主机:端口` + `-b` | TCP/UDP 带宽测量 |

#### 常用命令示例

```cmd
:: ICMP Ping
psping -n 10 www.contoso.com

:: TCP Ping（测试 443 端口连通性）
psping -n 10 www.contoso.com:443

:: TCP 延迟测试（带直方图）
psping -l 64k -n 20 target:3389

:: 带宽测试（10 秒）
psping -b -l 512k -n 20 target:5001

:: 作为服务端监听
psping -s 0.0.0.0:5001
```

**来源**: https://learn.microsoft.com/en-us/sysinternals/downloads/psping + CSDN 实战
**可信度**: ★★★★★（官方文档）；★★★★☆（实战技巧）

---

### 2.7 Sysmon — 网络事件日志

#### 网络相关事件 ID

| Event ID | 名称 | 网络相关性 |
|----------|------|-----------|
| **3** | NetworkConnect | 记录所有 TCP/UDP 连接（源/目标 IP、端口、进程） |
| **22** | DNSEvent | 记录 DNS 查询（域名、查询类型、结果） |
| **1** | ProcessCreate | 进程创建（关联分析：哪个进程发起了网络连接） |
| **7** | ImageLoaded | DLL 加载（发现网络相关 DLL） |
| **8** | CreateRemoteThread | 远程线程注入（可能与网络攻击相关） |

#### 配置示例（网络连接监控）
```xml
<RuleGroup name="" groupRelation="or">
  <NetworkConnect onmatch="include">
    <DestinationPort condition="is">445</DestinationPort>
    <DestinationPort condition="is">3389</DestinationPort>
    <DestinationPort condition="is">22</DestinationPort>
  </NetworkConnect>
</RuleGroup>
```

安装和配置：
```cmd
:: 安装
Sysmon64.exe -accepteula -i sysmonconfig.xml

:: 更新配置
Sysmon64.exe -c sysmonconfig-export.xml

:: 查询网络连接事件
Get-WinEvent -FilterHashtable @{LogName='Microsoft-Windows-Sysmon/Operational'; Id=3}
```

**来源**: Microsoft Learn Sysmon 文档 + CSDN 安全分析
**可信度**: ★★★★★（官方文档）；★★★★☆（配置示例来自安全社区实践）

---

### 2.8 其他网络相关工具

#### PsFile — 查看远程打开的文件
```cmd
psfile \\remote-server
```
显示通过 SMB 连接打开的远程文件，排查文件锁定和共享冲突。

#### Whois — 域名/IP 归属查询
```cmd
whois example.com
```
查看域名注册信息，排查可疑域名。

#### ShareEnum — 网络共享扫描
扫描网络中的文件共享并检查安全设置，发现过度开放的共享。

#### PipeList — 命名管道查看
```cmd
pipelist
```
显示系统上的命名管道（含实例数），排查 IPC 通信问题。命名管道常用于进程间通信，也可跨网络使用。

#### AdInsight — LDAP 实时监控
实时监控 LDAP API 调用，排查 Active Directory 认证和查询问题。

**来源**: Microsoft Learn 官方文档
**可信度**: ★★★★★

---

## 三、Mark Russinovich 本人的使用建议

### 3.1 来自《Windows Sysinternals Administrator's Reference》

Mark Russinovich 和 Aaron Margosis 在官方指南书中提供了以下网络排障相关建议：

1. **TCPView 是 netstat 的增强替代**：适合快速查看系统网络活动全貌，比 netstat 提供更丰富的信息和更好的可视化
2. **Process Monitor 是万能排障工具**：通过过滤器可以聚焦到任何特定行为，包括网络操作
3. **ProcDump 应该是生产环境标配**：用于事后分析网络服务崩溃和挂起
4. **工具组合使用**：Sysinternals 工具的最大价值在于组合使用——Procmon 发现问题 → Process Explorer 定位进程 → ProcDump 抓取现场 → WinDbg 分析

**来源**: https://learn.microsoft.com/en-us/sysinternals/resources/troubleshooting-book
**可信度**: ★★★★★（一手来源，Russinovich 本人合著）

### 3.2 来自 Sysinternals 官方博客和技术演讲

Russinovich 在多次技术演讲中演示了 Sysinternals 工具的实际使用：

1. **排障方法论**：从宏观到微观——先用 TCPView/Process Explorer 概览，再用 Process Monitor 深入细节
2. **Procmon 的 Boot Logging**：推荐用于排查开机后网络初始化延迟
3. **安全调查流程**：TCPView → Process Explorer → Autoruns → Procmon 的排查链
4. **ProcDump 在 Azure 中的使用**：Russinovich 作为 Azure CTO，经常在 Azure 排障中使用 ProcDump 抓取网络服务问题

**来源**: https://learn.microsoft.com/en-us/sysinternals/（官方博客和技术文档）
**可信度**: ★★★★★（一手来源）

### 3.3 来自 Mark Russinovich 的 VirusTotal 分析方法

Russinovich 在安全分析演示中展示了：
- 使用 TCPView 快速发现恶意外连
- 用 Process Explorer 检查可疑进程的 DLL 加载（网络相关 DLL）
- 用 Procmon 追踪恶意软件的完整网络行为链

**来源**: 技术演讲和演示
**可信度**: ★★★★☆

---

## 四、网络问题工具选择决策树

```
网络问题？
│
├── 需要查看当前网络连接状态？
│   ├── 快速概览 → TCPView
│   ├── 需要进程级关联 → Process Explorer (TCP/IP 标签)
│   └── 需要长期日志记录 → Sysmon (Event ID 3)
│
├── 需要测试网络连通性和性能？
│   ├── ICMP 可达性 → PsPing (ICMP 模式)
│   ├── TCP 端口连通性 → PsPing (TCP 模式)
│   ├── TCP 延迟精确测量 → PsPing (-l 模式)
│   └── 带宽测试 → PsPing (-b 模式)
│
├── 需要深入分析网络操作细节？
│   ├── 哪个进程在做什么网络操作 → Process Monitor
│   ├── DNS 解析问题 → Process Monitor (过滤 dns.exe/svchost.exe)
│   ├── 连接建立失败 → Process Monitor (过滤 TCP Connect)
│   └── 网络数据包内容 → Wireshark（非 Sysinternals）
│
├── 需要抓取网络应用崩溃/挂起现场？
│   ├── 应用挂起/假死 → ProcDump -h (窗口未响应)
│   ├── 应用崩溃 → ProcDump -e (异常触发)
│   ├── CPU 飙升 → ProcDump -p (性能计数器触发)
│   └── 生产环境长期监控 → ProcDump -i (AeDebug 注册)
│
├── 需要远程排障？
│   ├── 远程执行诊断命令 → PsExec
│   ├── 远程部署诊断工具 → PsExec -c
│   ├── 远程抓 dump → PsExec + ProcDump
│   └── 远程查看共享文件 → PsFile
│
├── 需要安全/恶意软件分析？
│   ├── 实时连接监控 → TCPView
│   ├── 进程行为追踪 → Process Monitor
│   ├── 异常 DLL/句柄 → Process Explorer
│   ├── 启动项检查 → Autoruns（含网络自启动）
│   ├── 持久化日志 → Sysmon (Event ID 1+3+22)
│   └── 网络共享安全 → ShareEnum
│
└── 需要 Active Directory 排障？
    ├── LDAP 查询监控 → AdInsight
    ├── AD 对象浏览 → AdExplorer
    └── AD 对象恢复 → AdRestore
```

---

## 五、Sysinternals 工具在网络排障中的典型工作流

### 5.1 Web 应用响应缓慢

```
1. TCPView → 查看 IIS/w3wp 的连接数和状态
2. Process Explorer → 查看 w3wp 的线程等待状态
3. Process Monitor → 过滤 w3wp 的网络操作，查看延迟来源
4. ProcDump -h w3wp.exe → 如果挂起，抓取 dump 分析线程栈
```

### 5.2 DNS 解析问题

```
1. Process Monitor → 过滤 dns.exe 和 svchost.exe (Dnscache)
2. Sysmon Event ID 22 → 查看 DNS 查询日志
3. PsPing → 测试 DNS 服务器连通性
4. TCPView → 查看 DNS 端口（53）的连接状态
```

### 5.3 恶意软件外连检测

```
1. TCPView → 快速发现异常外连
2. Process Explorer → 检查可疑进程的 DLL 和句柄
3. Process Monitor → 追踪恶意进程的完整网络行为
4. Sysmon Event ID 3 → 长期记录所有网络连接
5. Autoruns → 检查网络相关的自启动项
```

### 5.4 远程服务器网络问题

```
1. PsPing → 测试远程端口连通性和延迟
2. PsExec → 远程执行 ipconfig/netstat/nslookup
3. PsExec + ProcDump → 远程抓取服务 dump
4. PsExec + TCPView → 远程查看连接状态
```

---

## 六、信息源汇总

### 一手来源（Russinovich 本人/官方）

| 来源 | URL | 可信度 |
|------|-----|--------|
| Sysinternals 官方页面 | https://www.sysinternals.com/ | ★★★★★ |
| Microsoft Learn - 工具索引 | https://learn.microsoft.com/en-us/sysinternals/downloads/ | ★★★★★ |
| Microsoft Learn - TCPView | https://learn.microsoft.com/en-us/sysinternals/downloads/tcpview | ★★★★★ |
| Microsoft Learn - Process Monitor | https://learn.microsoft.com/en-us/sysinternals/downloads/procmon | ★★★★★ |
| Microsoft Learn - Process Explorer | https://learn.microsoft.com/en-us/sysinternals/downloads/process-explorer | ★★★★★ |
| Microsoft Learn - ProcDump | https://learn.microsoft.com/en-us/sysinternals/downloads/procdump | ★★★★★ |
| Microsoft Learn - PsExec | https://learn.microsoft.com/en-us/sysinternals/downloads/psexec | ★★★★★ |
| Microsoft Learn - PsPing | https://learn.microsoft.com/en-us/sysinternals/downloads/psping | ★★★★★ |
| Microsoft Learn - Sysmon | https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon | ★★★★★ |
| Microsoft Learn - 网络工具分类 | https://learn.microsoft.com/en-us/sysinternals/downloads/networking-utilities | ★★★★★ |
| Windows Sysinternals Administrator's Reference | https://learn.microsoft.com/en-us/sysinternals/resources/troubleshooting-book | ★★★★★ |
| Windows Internals (Russinovich & Solomon) | https://learn.microsoft.com/en-us/sysinternals/resources/windows-internals | ★★★★★ |

### 二手来源（社区总结/实战经验）

| 来源 | URL | 可信度 | 备注 |
|------|-----|--------|------|
| CSDN - TCPView 排查实战 | https://blog.csdn.net/weixin_30512965/article/details/161636667 | ★★★☆☆ | 中文实战案例 |
| CSDN - Procmon 过滤器 | https://blog.csdn.net/weixin_47431459/article/details/153745599 | ★★★☆☆ | 学习笔记 |
| CSDN - PsExec 远程连接 | https://blog.csdn.net/weixin_47431459/article/details/153964982 | ★★★☆☆ | 实战排障 |
| CSDN - ProcDump 触发条件 | https://blog.csdn.net/weixin_47431459/article/details/153815699 | ★★★☆☆ | 学习笔记 |
| CSDN - PsPing TCP 测试 | https://blog.csdn.net/elastic6hunter/article/details/152868400 | ★★★☆☆ | Azure 排障 |
| CSDN - Sysmon 配置 | https://wenku.csdn.net/column/xjmn23n9n6s | ★★★☆☆ | 安全配置 |
| 博客园 - Sysinternals 工具索引 | https://www.cnblogs.com/wutou/p/18551324 | ★★★★☆ | 较全面的索引 |
| TheITBros - PsExec 指南 | https://theitbros.com/using-psexec-to-run-commands-remotely/ | ★★★★☆ | 英文实战 |

### 排除的信息源
- 知乎（黑名单）
- 微信公众号（黑名单）
- 百度百科（黑名单）

---

## 七、关键发现总结

1. **Sysinternals 工具集中有 9 个直接归类为「网络工具」的工具**：TCPView、PsPing、PsFile、Whois、ShareEnum、PipeList、AdExplorer、AdInsight、AdRestore

2. **Process Monitor 是网络排障的隐藏利器**：虽然不抓包，但通过 Winsock 钩子可以回答"谁在连接、为什么连接"，这是 Wireshark 无法直接提供的

3. **ProcDump 在网络场景中的价值**：主要用于抓取网络应用挂起/崩溃时的现场，配合 WinDbg 可分析网络等待的线程栈

4. **工具组合 > 单一工具**：Russinovich 本人强调工具的组合使用——TCPView 做概览、Procmon 做深入、ProcDump 做事后分析

5. **Sysmon 是企业级网络监控的关键**：Event ID 3（网络连接）和 Event ID 22（DNS 查询）提供了持久化的网络活动日志，适合与 SIEM 集成

6. **PsPing 替代传统 ping 的价值**：在防火墙阻断 ICMP 的环境中，PsPing 的 TCP Ping 模式是测试端口连通性的首选工具
