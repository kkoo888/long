# Windows 网络栈内部机制 — 深度调研

> 基于 Mark Russinovich《Windows Internals》及 Microsoft 官方文档的系统性研究
> 
> 调研日期：2026-06-12

---

## 一、Windows 网络架构总览

### 1.1 OSI 模型与 Windows �络架构的映射

Windows 网络架构并不严格遵循 OSI 七层模型，而是出于**简化和性能**的考虑，将多个 OSI 层合并到单个组件中。Russinovich 在《Windows Internals》中指出，NT 的网络架构设计更偏向实用主义分层：

```
┌─────────────────────────────────────────────────┐
│          应用程序 (Application)                    │
│     Internet Explorer, Chrome, Explorer 等        │
├─────────────────────────────────────────────────┤
│     网络 API 层 (Network APIs)                     │
│  Winsock (WS2_32.dll) | WinHTTP | WinINET         │
│  NetBIOS | RPC | SMB Direct                       │
├─────────────────────────────────────────────────┤
│   传输驱动接口层 (TDI / WSK)                       │
│  AFD.sys (Ancillary Function Driver)              │
│  TDI.sys → 已被 WSK 取代 (Win8+)                  │
├─────────────────────────────────────────────────┤
│   协议驱动层 (Protocol Drivers)                    │
│  tcpip.sys (IPv4/IPv6 + TCP/UDP)                  │
│  NetBt.sys (NetBIOS over TCP/IP)                  │
│  [WFP 过滤层内嵌于此]                               │
├─────────────────────────────────────────────────┤
│   NDIS 中间层 / 过滤层                              │
│  Ndis.sys (NDIS Library/Framework)                │
│  NDIS Filter Drivers / Intermediate Drivers        │
├─────────────────────────────────────────────────┤
│   NDIS 微端口驱动层 (Miniport Drivers)              │
│  特定网卡硬件驱动 (e1e6532.sys, rtl8168.sys 等)     │
├─────────────────────────────────────────────────┤
│          网卡硬件 (NIC Hardware)                    │
└─────────────────────────────────────────────────┘
```

**来源**：Microsoft Learn — Windows Network Architecture and the OSI Model  
**URL**：https://learn.microsoft.com/en-us/windows-hardware/drivers/network/windows-network-architecture-and-the-osi-model  
**可信度**：★★★★★（微软官方驱动开发文档）

### 1.2 《Windows Internals》网络章节结构

Mark Russinovich 在《Windows Internals》各版本中，网络内容分布在 Part 2 的"Networking"章节（第 12/13 章）。第 7 版覆盖 Windows 10 / Server 2016 的网络架构：

- **第 13 章 (Network I/O)** — 覆盖：
  - Windows 网络架构与 OSI 映射
  - 网络 API（Winsock、RPC、NetBIOS）
  - TDI（Transport Driver Interface）
  - NDIS 驱动模型
  - TCP/IP 协议栈（tcpip.sys）
  - SMB/CIFS 文件共享
  - 名称解析（DNS、WINS、NetBIOS）
  - 远程访问（RAS/VPN）

**来源**：《Windows Internals, 7th Edition, Part 2》— Pavel Yosifovich, Alex Ionescu, Mark E. Russinovich, David A. Solomon  
**URL**：https://learn.microsoft.com/ka-ge/sysinternals/resources/windows-internals  
**可信度**：★★★★★（Russinovich 官方著作）

---

## 二、Winsock (WS2_32.dll) 与内核交互

### 2.1 Winsock 架构

Winsock（Windows Sockets）是 Windows 平台的标准网络编程接口，基于 Berkeley Sockets API 的增强实现。

**核心组件**：
- **ws2_32.dll** — 用户态 DLL，暴露 socket()、bind()、connect()、send()、recv() 等 API
- **WSAStartup()** — 必须在任何 Socket 操作前调用，协商版本号（如 MAKEWORD(2,2)）、分配内部资源
- **Winsock LSP（Layered Service Provider）** — 分层服务提供者，允许在传输层与应用层之间插入自定义过滤逻辑（透明代理、流量监控等）
- **WSK（Winsock Kernel）** — Windows 8+ 引入的内核态网络接口，取代 TDI

**从用户态到内核态的数据流**：
```
应用程序调用 socket()/send()/recv()
    ↓
WS2_32.dll (用户态 Winsock 库)
    ↓ 生成 Socket IRP
AFD.sys (Ancillary Function Driver — 内核态辅助驱动)
    ↓ 转换为 TDI IRP / WSK 请求
Tcpip.sys (协议驱动)
    ↓ NDIS 回调函数
NDIS Miniport Driver → 网卡
```

**来源**：博客园 — Windows 内核情景分析：网络通信  
**URL**：https://www.cnblogs.com/jadeshu/p/10663601.html  
**可信度**：★★★★☆（技术细节与内核源码分析一致，社区高质量技术文章）

### 2.2 AFD.sys — 辅助功能驱动

AFD.sys（Ancillary Function Driver）是 Winsock 的内核态实现，位于 WS2_32.dll 和 Tcpip.sys 之间。

**关键职责**：
- 接收来自用户态的 Socket IRP
- 管理 socket 缓冲区（发送/接收队列）
- 将 Socket 操作转换为 TDI（或 WSK）请求
- 实现异步 I/O 模型（IOCP — I/O Completion Ports）
- 事件通知机制（select、WSAEventSelect、WSAEnumNetworkEvents）

**AFD 与 IOCP 的协同**：
AFD 是 Windows 网络高性能 I/O 的关键。当网卡中断触发 → Miniport Driver 处理 DMA 接收 → NDIS 将 NET_BUFFER_LIST 递交给 tcpip.sys → tcpip.sys 处理后通过 AFD 将完成项加入 IOCP 队列 → 应用程序的工作线程从 IOCP 获取结果。

**来源**：CSDN 博客 — 内核网络组件 AFD 与 Kernel Socket 跨平台架构分析  
**URL**：https://blog.csdn.net/zhangyihu321/article/details/157906143  
**可信度**：★★★★☆（技术分析深入，与微软文档一致）

### 2.3 Winsock LSP 的演进与安全风险

LSP（Layered Service Provider）允许第三方在 Winsock 协议栈中插入自定义层。这曾被广泛用于：
- 网络加速软件
- 广告过滤
- 家长控制
- 恶意软件（浏览器劫持、流量注入）

Windows 8+ 引入 WSK（Winsock Kernel）作为 TDI 的替代，减少了 LSP 的使用场景。LSP 已被标记为 deprecated。

**来源**：技术栈 — Windows Socket API 与 LSP 分层服务提供者  
**URL**：https://jishuzhan.net/article/2021019608774410241  
**可信度**：★★★★☆（技术细节准确）

---

## 三、TDI（Transport Driver Interface）与 WSK

### 3.1 TDI — 传统传输驱动接口

TDI 是 Windows 2000 至 Windows 7 时代，协议驱动（如 tcpip.sys）向上层暴露的内核态接口。

**关键特征**：
- 通过 IRP（I/O Request Packet）进行通信
- AFD.sys 生成 TDI IRP 发送给 tcpip.sys
- TDI 定义了连接管理、数据传输、事件通知等标准接口
- 已在 Windows 8+ 中被 WSK 取代（但保留向后兼容）

### 3.2 WSK — Winsock Kernel（Windows 8+）

WSK 是 TDI 的现代化替代，提供更高效的内核态网络编程接口。

**优势**：
- 直接集成到 tcpip.sys 内部，减少 IRP 开销
- 支持事件驱动模型
- 更好的性能和更低的延迟
- 与 Windows Filtering Platform (WFP) 更好集成

**来源**：Microsoft Learn — NDIS Driver Stack  
**URL**：https://learn.microsoft.com/en-us/windows-hardware/drivers/network/ndis-driver-stack  
**可信度**：★★★★★

---

## 四、Tcpip.sys — TCP/IP 协议驱动

### 4.1 架构概述

Tcpip.sys 是 Windows 的核心协议驱动，实现了完整的 IPv4/IPv6 协议栈。它是"下一代 TCP/IP 栈"（Next Generation TCP/IP Stack）的核心，从 Windows Vista 开始引入。

**功能分层**（tcpip.sys 内部）：
```
┌─────────────────────────────────────────┐
│    传输层 (Transport Layer)               │
│    OUTBOUND_TRANSPORT / INBOUND_TRANSPORT │
│    实现 TCP/UDP 数据包封装与还原           │
├─────────────────────────────────────────┤
│    网络层 (Network Layer)                 │
│    OUTBOUND_NETWORK / INBOUND_NETWORK     │
│    实现 IPv4/IPv6 路由、分片、重组         │
├─────────────────────────────────────────┤
│    帧层 (Framing Layer)                   │
│    以太网帧封装、ARP、NDP                  │
└─────────────────────────────────────────┘
```

**关键设计**：
- **双 IP 层架构**：IPv4 和 IPv6 共享传输层和帧层实现
- **对上**：提供内核 Winsock 接口（WSK）和 TDI（兼容层）
- **对下**：实现为 NDIS 协议驱动，与 Miniport Driver 交互
- **内嵌 WFP**：每层都嵌入 Windows Filtering Platform 接口，支持流量拦截和修改

### 4.2 Tcpip.sys 作为 NDIS 协议驱动

Tcpip.sys 通过 `NdisRegisterProtocol()` 向 NDIS 框架注册为协议驱动，注册的回调函数包括：
- `ProtocolBindAdapterHandler` — 绑定网卡
- `ProtocolReceiveNetBufferLists` — 接收数据包
- `ProtocolSendNetBufferListsComplete` — 发送完成通知

**数据接收路径**：
```
网卡硬件中断
  ↓
Miniport Driver 处理 DMA 接收
  ↓
NDIS 将 NET_BUFFER_LIST 链表递交给 tcpip.sys
  ↓
IP 层校验 → TCP 层解析连接状态
  ↓
若为新建连接：创建 TCB (Transmission Control Block)
  ↓
触发三次握手响应 → 通过 AFD 通知应用程序
```

**来源**：CSDN 文库 — Windows 网络协议栈内部实现原理深度解析  
**URL**：https://wenku.csdn.net/doc/x2za1cp4mx  
**可信度**：★★★★☆（基于 TechNet 广播课程，技术细节与微软文档一致）

### 4.3 NDIS 协议特征注册

协议驱动在 DriverEntry 中向 NDIS 注册协议特征结构：

```c
typedef struct _NDIS40_PROTOCOL_CHARACTERISTICS {
    UCHAR MajorNdisVersion;
    UCHAR MinorNdisVersion;
    OPEN_ADAPTER_COMPLETE_HANDLER OpenAdapterCompleteHandler;  // 绑定完成
    CLOSE_ADAPTER_COMPLETE_HANDLER CloseAdapterCompleteHandler;
    SEND_COMPLETE_HANDLER SendCompleteHandler;                // 发送完成
    RECEIVE_HANDLER ReceiveHandler;                           // 接收函数
    RECEIVE_COMPLETE_HANDLER ReceiveCompleteHandler;          // 接收完成
    BIND_HANDLER BindAdapterHandler;                          // 绑定通知
    UNBIND_HANDLER UnbindAdapterHandler;                      // 解除绑定
    PNP_EVENT_HANDLER PnPEventHandler;                        // PnP 事件
    UNLOAD_PROTOCOL_HANDLER UnloadHandler;                    // 卸载例程
    // ...
} NDIS40_PROTOCOL_CHARACTERISTICS;
```

**来源**：博客园 — Windows 内核情景分析：网络通信（含 NDIS 源码分析）  
**URL**：https://www.cnblogs.com/jadeshu/p/10663601.html  
**可信度**：★★★★☆（基于 ReactOS 源码和逆向分析，技术细节可靠）

---

## 五、NDIS（Network Driver Interface Specification）驱动模型

### 5.1 NDIS 驱动栈架构

NDIS 是微软为统一网卡驱动与上层协议栈交互而制定的标准框架。NDIS 驱动栈包含三种类型的驱动：

```
┌──────────────────────────────────────┐
│  协议驱动 (Protocol Drivers)          │
│  如 tcpip.sys, NetBt.sys              │
│  位于驱动栈顶部，绑定到 Miniport       │
├──────────────────────────────────────┤
│  过滤驱动 (Filter Drivers)            │
│  如 NDIS Filter, WFP Callout          │
│  可堆叠任意数量的过滤模块              │
├──────────────────────────────────────┤
│  中间驱动 (Intermediate Drivers)       │
│  同时具有协议边缘和虚拟 Miniport       │
│  如 WLAN 驱动、LBFO 驱动              │
├──────────────────────────────────────┤
│  微端口驱动 (Miniport Drivers)         │
│  直接控制网卡硬件                      │
│  如 e1e6532.sys, rtl8168.sys          │
├──────────────────────────────────────┤
│  网卡硬件 (NIC Hardware)               │
└──────────────────────────────────────┘
```

### 5.2 NDIS 6.0 驱动栈

NDIS 6.0（Windows Vista+）的驱动栈结构：
- **Miniport Adapter** — 底层，直接与硬件交互
- **Filter Modules** — 可堆叠任意数量的过滤模块，对协议驱动透明
- **Protocol Driver** — 顶部，绑定到 Miniport Adapter
- **Intermediate Driver** — 本质上是两个栈的组合：上半部是虚拟 Miniport，下半部是协议绑定

**关键特性**：
- 协议驱动绑定到 Miniport Adapter，底层过滤模块对协议驱动透明
- 多个协议驱动可以绑定到同一个 Miniport Adapter
- NDIS 负责路由请求到正确的协议驱动

**来源**：Microsoft Learn — NDIS Driver Stack  
**URL**：https://learn.microsoft.com/en-us/windows-hardware/drivers/network/ndis-driver-stack  
**可信度**：★★★★★（微软官方驱动开发文档）

### 5.3 NDIS 框架 (ndis.sys)

ndis.sys 是 NDIS 框架的实现，提供：
- 协议驱动、中间驱动、小端口驱动三者之间的交互框架
- 注册 API：`NdisRegisterProtocol()`、`NdisRegisterMiniport()`
- 数据包管理：NET_BUFFER_LIST、NET_BUFFER 结构
- 状态通知、PnP 事件处理
- NDIS OID（Object Identifier）查询/设置接口

### 5.4 NDIS Filter vs WFP

| 特性 | NDIS Filter | WFP (Windows Filtering Platform) |
|------|-------------|-----------------------------------|
| **位置** | NDIS 中间驱动层，更底层 | tcpip.sys 内部，更高层 |
| **过滤粒度** | 原始网络帧 | 应用层连接、传输层、网络层等多层 |
| **可见性** | 看到所有协议的数据包 | 只看到 TCP/IP 协议栈的数据包 |
| **用途** | 包过滤、NAT、流量整形 | 防火墙、安全监控、应用层过滤 |
| **接口** | NDIS Filter Driver API | WFP API (Fwpuclnt.sys) |

**来源**：CSDN 博客 — WIN7 系统内核网络堆栈实现简述  
**URL**：https://blog.csdn.net/wzsy/article/details/50954596  
**可信度**：★★★★☆（技术分析准确，与微软文档一致）

---

## 六、DNS Client Service（Dnscache）内部机制

### 6.1 服务架构

- **服务名称**：Dnscache（DNS Client）
- **宿主进程**：svchost.exe -k NetworkService
- **依赖服务**：Tcpip, NSI (Network Store Interface Service)
- **注册表路径**：`HKLM\SYSTEM\CurrentControlSet\Services\Dnscache\Parameters`

**核心职责**：
- 维护 DNS 缓存（正向/反向/负缓存）
- 管理每个网卡的 DNS 服务器列表
- 实现 NRPT（Name Resolution Policy Table）规则匹配
- 控制查询超时和重试逻辑
- 处理 DNS 后缀追加和 Devolution

### 6.2 DNS 解析流水线

```
应用程序调用 getaddrinfo() / DnsQuery()
    ↓
① 检查 DNS 缓存（含 Hosts 文件）
    ↓ 缓存未命中
② 检查 NRPT 规则 — 域名是否匹配？
    ├── 匹配 → 使用 NRPT 指定的 DNS 服务器
    └── 不匹配 → 继续
③ 选择 Preferred Adapter（最低 Interface Metric）
④ DNS 后缀追加（如果是 short name）
⑤ 按超时逻辑查询 DNS 服务器
    ↓
返回结果（写入缓存）
```

### 6.3 DNS 超时与重试逻辑

**2 个 DNS 服务器配置时**：
| 累计时间 | 动作 |
|---------|------|
| 0s | 向第 1 个 DNS 发查询 |
| 1s | 无应答 → 向第 2 个 DNS 发查询 |
| 2s | 无应答 → 再次向第 2 个 DNS 发查询 |
| 4s | 无应答 → 同时向所有 DNS 发查询 |
| 8s | 无应答 → 再次同时向所有 DNS 发查询 |
| 10s | 超时，解析失败 |

**关键规则**：
1. **否定应答（NXDOMAIN）会立即终止查询** — DNS 客户端不会再尝试下一个 DNS 服务器
2. 只有 DNS 服务器不可达（无应答）时才会尝试下一个
3. 前 3 个 DNS 是逐个尝试的，第 4 个及以后只在 4 秒后的"同时发送"阶段才被使用

### 6.4 多网卡 DNS 解析（Smart Multi-Homed Name Resolution）

Windows 8/Server 2012 引入了 Smart Multi-Homed Name Resolution：
- **传统模式**：先查 Preferred Adapter，超时后查其他
- **Smart Multi-Homed**（默认启用）：可能同时向所有适配器的 DNS 发查询

**VPN 场景的常见问题**：没有 NRPT 规则时，VPN 适配器的 Metric 通常较高（优先级低），所有 DNS 查询都先发给物理网卡的公网 DNS 服务器，公网 DNS 返回 NXDOMAIN 后直接终止，永远不会查到 VPN 的内部 DNS。

**解决方案**：配置 NRPT 规则，或降低 VPN 适配器的 Interface Metric。

**来源**：irishemin.github.io — Deep Dive: Windows DNS 客户端解析逻辑  
**URL**：https://irishemin.github.io/knowledge_base/knowledge/networking/2026/03/27/deep-dive-dns-client-resolution-multi-nic-vpn/  
**可信度**：★★★★☆（技术分析深入，与微软文档和实际测试一致）

---

## 七、DHCP Client 服务机制

### 7.1 服务架构

- **服务名称**：Dhcp（DHCP Client）
- **宿主进程**：svchost.exe
- **注册表路径**：`HKLM\SYSTEM\CurrentControlSet\Services\Dhcp`
- **依赖服务**：Tcpip, NSI, Afd
- **协议端口**：UDP 67（服务器）/ UDP 68（客户端）

### 7.2 DHCP 租约过程

```
客户端 → 广播 DHCP Discover (UDP 67/68)
    ↓
DHCP 服务器 → DHCP Offer（提供 IP 地址）
    ↓
客户端 → 广播 DHCP Request（接受 offer）
    ↓
DHCP 服务器 → DHCP ACK（确认租约）
    ↓
客户端配置 IP、子网掩码、网关、DNS
```

### 7.3 Svchost 服务拆分机制

从 Windows 10 1703 开始，微软引入 Svchost 服务拆分机制：
- 在内存大于 3.5GB 的客户端系统中，将共享服务拆分到独立的 svchost 实例
- 提高安全隔离性（一个服务崩溃不影响其他服务）
- DHCP Client、DNS Client 等网络服务可能在同一 svchost 实例中运行

**来源**：CSDN 博客 — Windows Internals 10.2.26：Svchost 服务拆分机制详解  
**URL**：https://blog.csdn.net/weixin_47431459/article/details/160602470  
**可信度**：★★★★☆（基于 Windows Internals 内容分析）

---

## 八、SMB/CIFS 协议栈

### 8.1 SMB 协议架构

SMB（Server Message Block）是 Windows 文件共享的核心协议。客户端和服务器端分别由不同的内核驱动实现：

**客户端**：
- **MRxSmb.sys** — SMB 客户端小端口驱动，负责 SMB 协商、认证加密/解密
- **RDBSS.sys** — 重定向驱动缓冲子系统（Redirected Drive Buffering SubSystem）
- **LanmanWorkstation 服务** — 用户态服务组件

**服务器端**：
- **srv.sys** — SMB 服务器驱动，处理来自客户端的 SMB 请求
- **srv2.sys** — SMB2/3 服务器驱动
- **LanmanServer 服务** — 用户态服务组件

### 8.2 SMB 版本演进

| 版本 | 引入版本 | 关键特性 |
|------|---------|---------|
| SMBv1 | Windows NT 3.1 | 基础文件共享，明文传输 |
| SMBv2 | Windows Vista / Server 2008 | 性能优化，减少往返次数 |
| SMBv3 | Windows 8 / Server 2012 | 多通道、加密、压缩 |
| SMBv3.1.1 | Windows 10 / Server 2016 | AES-128-GCM 加密、预认证完整性 |

### 8.3 SMB 认证与签名

SMB 会话建立流程：
1. **SMB 协商** — 客户端和服务器协商协议版本和安全能力
2. **会话设置** — 使用 Kerberos 或 NTLM 进行身份验证
3. **树连接** — 连接到共享资源

**签名机制**：
- `RequireSecuritySignature=1` 在 SRV.SYS 中触发 `Srv2SignSmb3Message`
- 在 MRxSmb.sys 中还需检查 `SmbClientConfiguration.SigningRequired`
- 两者不一致将导致签名协商失败却不报错 — 这是生产环境 SMB 连接问题的常见根源

**来源**：CSDN 文库 — 揭秘"安全策略阻止未经身份验证的来宾访问"：SMB 协议演进史  
**URL**：https://wenku.csdn.net/column/5iexva8qvapc  
**可信度**：★★★★☆（技术分析深入，结合实际生产环境问题）

### 8.4 EternalBlue 漏洞与 srv.sys

EternalBlue（MS17-010）是 2017 年影响 SMBv1 的严重远程代码执行漏洞，攻击者向 srv.sys 发送特制 SMB 消息即可触发。该漏洞导致 WannaCry 勒索软件全球爆发，凸显了 SMB 服务器驱动在安全方面的关键地位。

**来源**：Dell Support — Windows EternalBlue 漏洞导致的停止代码 0x50 srv.sys  
**URL**：https://www.dell.com/support/kbdoc/zh-cn/000141143/  
**可信度**：★★★★★（厂商官方支持文档）

---

## 九、IPv4 自动配置（APIPA）的内部实现

### 9.1 APIPA 概述

APIPA（Automatic Private IP Addressing）是 Windows 的 DHCP 故障转移机制。当设备配置为 DHCP 客户端但无法从 DHCP 服务器获取 IP 地址时，系统自动从 169.254.0.0/16 范围分配地址。

### 9.2 实现机制

**地址分配流程**：
1. 系统检测到 DHCP 服务超时（60-120 秒）
2. 从 169.254.0.0/16 范围随机选择一个候选地址
3. 发送 **ARP Probe**（3 次，目标 IP=候选 IP，源 IP=0.0.0.0）验证地址唯一性
4. 等待 1 秒确认无冲突响应
5. 配置 255.255.0.0 子网掩码
6. 启用临时网络接口

**持续行为**：
- 系统每 5 分钟重新尝试 DHCP 请求
- 收到 DHCPOFFER 后，平滑切换到 DHCP 分配的地址

**注册表控制**：
- `HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters`
- `IPAutoconfigurationEnabled` — 启用/禁用 APIPA

### 9.3 内核实现

APIPA 的核心逻辑在 tcpip.sys 中实现。当 DHCP 客户端（Dhcp 服务）报告无法获取地址时，tcpip.sys 的自动配置模块接管：
- 生成随机地址候选
- 执行 ARP 探测
- 管理地址生命周期

**来源**：51CTO 博客 — 169.254.x.x 地址技术深度解析  
**URL**：https://blog.51cto.com/u_14940497/14094397  
**可信度**：★★★★☆（技术分析准确，与 RFC 3927 规范一致）

---

## 十、Windows Filtering Platform (WFP)

### 10.1 WFP 架构

WFP 是 Windows Vista+ 引入的网络过滤框架，取代了旧的防火墙钩子和 TDI 过滤机制。

**架构层次**：
```
用户态: Fwpuclnt.sys (WFP 用户态 API)
    ↓
内核态: WFP Engine (tcpip.sys 内部)
    ↓
14 个预定义筛选层:
  ├── ALE_AUTH_CONNECT (应用层连接认证)
  ├── ALE_FLOW_ESTABLISHED (流建立)
  ├── INET_LAYER_IPv4 (IPv4 网络层)
  ├── TRANSPORT_LAYER_UDP (UDP 传输层)
  ├── OUTBOUND_TRANSPORT / INBOUND_TRANSPORT
  ├── OUTBOUND_NETWORK / INBOUND_NETWORK
  └── ... 更多层
```

**WFP 的关键优势**：
- 在 TCP/IP 协议栈的所有层提供过滤能力
- 支持 Callout Driver 进行深度包检测和修改
- 比旧的防火墙钩子更安全、更灵活
- 与 Windows Firewall with Advanced Security 集成

**来源**：CSDN 文库 — 基于 Windows 筛选平台 WFP 的网络流量监控系统实现  
**URL**：https://wenku.csdn.net/doc/7seqrjk6xt  
**可信度**：★★★★☆（技术分析与微软文档一致）

---

## 十一、各组件在网络数据包路径中的位置

### 11.1 发送路径（Outbound）

```
应用程序 send()
    ↓
WS2_32.dll (Winsock API)
    ↓ Socket IRP
AFD.sys (缓冲、异步 I/O)
    ↓ TDI/WSK 请求
Tcpip.sys — 传输层 (TCP/UDP 封装)
    ↓ [WFP OUTBOUND_TRANSPORT 过滤]
Tcpip.sys — 网络层 (IP 封装、路由)
    ↓ [WFP OUTBOUND_NETWORK 过滤]
Tcpip.sys — 帧层 (以太网帧封装)
    ↓ NDIS Protocol Driver 回调
NDIS Filter Drivers (可选过滤)
    ↓
NDIS Miniport Driver
    ↓ DMA / 中断
网卡硬件
```

### 11.2 接收路径（Inbound）

```
网卡硬件 → 中断
    ↓
Miniport Driver (DMA 接收，构建 NET_BUFFER_LIST)
    ↓
NDIS (将 NET_BUFFER_LIST 递交给协议驱动)
    ↓
NDIS Filter Drivers (可选过滤)
    ↓
Tcpip.sys — 帧层 (解析以太网帧)
    ↓
Tcpip.sys — 网络层 (IP 校验、路由决策)
    ↓ [WFP INBOUND_NETWORK 过滤]
Tcpip.sys — 传输层 (TCP 状态机、UDP 分发)
    ↓ [WFP INBOUND_TRANSPORT 过滤]
AFD.sys (缓冲、事件通知)
    ↓ IOCP 完成通知
应用程序 recv()
```

---

## 十二、常见网络故障的层次分析

### 12.1 故障层次对照表

| 故障类型 | 发生层次 | 相关组件 | 诊断工具 |
|---------|---------|---------|---------|
| 网卡不工作 | NDIS Miniport 层 | Miniport Driver, 网卡硬件 | Device Manager, netsh |
| 无法获取 IP | DHCP Client 层 | Dhcp 服务, tcpip.sys | ipconfig, eventvwr |
| APIPA 地址 (169.254.x.x) | DHCP + tcpip.sys | Dhcp 服务, APIPA 模块 | ipconfig /all |
| DNS 解析失败 | DNS Client 层 | Dnscache 服务 | nslookup, ipconfig /displaydns |
| 连接超时 | 传输层 | tcpip.sys (TCP) | ping, tracert, netstat |
| 端口不可达 | 传输层 | tcpip.sys (UDP/ICMP) | telnet, Test-NetConnection |
| SMB 连接失败 | 应用层 | MRxSmb.sys, srv.sys | net use, Get-SmbConnection |
| 防火墙阻止 | WFP 层 | WFP Engine, Windows Firewall | netsh advfirewall, wf.msc |
| VPN DNS 泄露 | DNS Client 层 | Dnscache, NRPT | nslookup, Wireshark |
| 网络驱动蓝屏 | NDIS 层 | ndis.sys, Miniport Driver | WinDbg, !analyze -v |

### 12.2 Russinovich 的内核视角网络问题诊断方法

Mark Russinovich 通过 Sysinternals 工具集提供了从内核视角诊断网络问题的方法论：

**Process Monitor (ProcMon)**：
- 通过内核驱动 ProcMon.sys 拦截文件系统、注册表、网络、进程/线程事件
- 可以观察到 socket 操作的完整 IRP 路径
- 支持堆栈跟踪（Stack Trace），可定位到具体的驱动和函数

**TCPView**：
- 实时显示所有 TCP/UDP 端点的详细信息
- 本地/远程 IP 与端口、连接状态（ESTABLISHED/SYN_SENT/FIN_WAIT 等）
- 所属进程名与 PID
- 支持直接终止连接

**网络诊断思路**：
1. **先看物理层**：网卡是否正常、驱动是否加载
2. **看 NDIS 层**：`netsh interface show interface` 检查适配器状态
3. **看 IP 层**：`ipconfig /all` 检查 IP 配置、`route print` 检查路由表
4. **看传输层**：`netstat -anob` 检查端口监听和连接状态
5. **看应用层**：ProcMon 抓取具体应用的网络操作
6. **看 DNS 层**：`ipconfig /displaydns` 检查缓存、`nslookup` 测试解析

**来源**：Sysinternals — TCPView  
**URL**：https://learn.microsoft.com/zh-tw/sysinternals/downloads/tcpview  
**可信度**：★★★★★（Russinovich 开发的官方工具）

**来源**：掘金 — Sysinternals 工具概述  
**URL**：https://juejin.cn/post/7633328194578055178  
**可信度**：★★★★☆（社区高质量技术文章）

---

## 十三、网络相关 Windows 内部组件和驱动完整列表

### 13.1 用户态组件

| 组件 | 可执行文件/DLL | 功能 |
|------|---------------|------|
| Winsock 库 | ws2_32.dll | Socket API 实现 |
| WinHTTP | winhttp.dll | HTTP 客户端 API |
| WinINET | wininet.dll | Internet API（IE 使用） |
| DNS Client | svchost.exe (Dnscache) | DNS 缓存和解析 |
| DHCP Client | svchost.exe (Dhcp) | DHCP 地址获取 |
| Workstation 服务 | svchost.exe (LanmanWorkstation) | SMB 客户端 |
| Server 服务 | svchost.exe (LanmanServer) | SMB 服务器 |
| IP Helper | svchost.exe (iphlpsvc) | IPv6 转换、端口代理 |
| NLA | svchost.exe (NlaSvc) | 网络位置感知 |
| WLAN AutoConfig | svchost.exe (WlanSvc) | 无线网络管理 |

### 13.2 内核态驱动

| 驱动 | 文件名 | 功能 |
|------|--------|------|
| TCP/IP 协议驱动 | tcpip.sys | IPv4/IPv6 + TCP/UDP 实现 |
| NDIS 框架 | ndis.sys | 网络驱动接口规范框架 |
| AFD 驱动 | afd.sys | Winsock 内核态辅助驱动 |
| TDI 驱动 | tdi.sys | 传输驱动接口（已废弃） |
| NetBIOS over TCP | netbt.sys | NetBIOS 协议实现 |
| SMB 重定向器 | mrxsmb.sys | SMB 客户端驱动 |
| SMB 服务器 | srv.sys / srv2.sys | SMB 服务器驱动 |
| RDBSS | rdbss.sys | 重定向驱动缓冲子系统 |
| WFP 引擎 | fwpkclnt.sys / wfplwfs.sys | Windows 过滤平台 |
| NSI | nsiproxy.sys | 网络存储接口代理 |
| NDIS 过滤器 | ndiswan.sys, ndisppp.sys | WAN 和 PPP 过滤器 |
| HTTP 协议驱动 | http.sys | HTTP 服务器端内核态驱动 |

---

## 十四、Russinovich 的内核视角网络理解

### 14.1 核心方法论

Mark Russinovich 从内核视角理解网络问题的核心方法论：

1. **分层思维**：网络问题必须从正确的层次开始排查，不能跳层
2. **数据包追踪**：理解数据包从应用层到硬件的完整路径
3. **状态机理解**：TCP 连接状态机（LISTEN, SYN_SENT, ESTABLISHED, FIN_WAIT 等）
4. **驱动交互**：理解 NDIS 框架下协议驱动、过滤驱动、小端口驱动的交互
5. **工具驱动**：使用 Sysinternals 工具从内核层面观察网络行为

### 14.2 从 Sysinternals 工具到内核洞察

Russinovich 开发的工具不仅用于诊断，更体现了他对 Windows 网络栈的深度理解：

- **Process Monitor** 通过内核驱动拦截 IRP，展示了 Winsock → AFD → TDI → Tcpip 的完整调用链
- **TCPView** 通过查询 tcpip.sys 的内部数据结构，展示所有活跃的 TCP/UDP 端点
- **Tcpvcon** (命令行版 TCPView) 可用于自动化网络状态监控

### 14.3 实际诊断案例思路

**案例：应用程序无法连接远程服务器**

Russinovich 式诊断流程：
1. **TCPView** — 检查是否有 SYN_SENT 状态的连接（连接建立中）
2. **Process Monitor** — 过滤网络操作，查看应用的 socket 调用序列
3. **ping / tracert** — 验证 IP 层连通性
4. **netstat -anob** — 检查端口监听状态和防火墙规则
5. **Event Viewer** — 检查系统日志中的网络相关错误事件
6. **netsh winsock reset** — 重置 Winsock 目录（修复 LSP 损坏）
7. **netsh int ip reset** — 重置 TCP/IP 协议栈

---

## 十五、信息源汇总

| # | 来源 | URL | 可信度 | 说明 |
|---|------|-----|--------|------|
| 1 | Microsoft Learn — NDIS Driver Stack | https://learn.microsoft.com/en-us/windows-hardware/drivers/network/ndis-driver-stack | ★★★★★ | 微软官方驱动文档 |
| 2 | Microsoft Learn — Roadmap for NDIS Drivers | https://learn.microsoft.com/en-us/windows-hardware/drivers/network/roadmap-for-developing-ndis-drivers | ★★★★★ | 微软官方驱动文档 |
| 3 | Microsoft Learn — Windows Network Architecture and OSI Model | https://learn.microsoft.com/en-us/windows-hardware/drivers/network/windows-network-architecture-and-the-osi-model | ★★★★★ | 微软官方架构文档 |
| 4 | Microsoft Learn — TCPView | https://learn.microsoft.com/zh-tw/sysinternals/downloads/tcpview | ★★★★★ | Russinovich 官方工具 |
| 5 | Microsoft Learn — Diagnose Packet Loss | https://learn.microsoft.com/zh-cn/troubleshoot/windows-client/networking/diagnose-packet-loss | ★★★★★ | 微软官方故障排查 |
| 6 | Microsoft Learn — SMB Protocol Overview | https://learn.microsoft.com/zh-cn/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview | ★★★★★ | 微软官方协议文档 |
| 7 | Microsoft Learn — Windows Internals Book | https://learn.microsoft.com/ka-ge/sysinternals/resources/windows-internals | ★★★★★ | Russinovich 官方著作 |
| 8 | 《Windows Internals, 7th Edition, Part 2》 | 书籍 | ★★★★★ | 第 13 章 Network I/O |
| 9 | 博客园 — Windows 内核情景分析：网络通信 | https://www.cnblogs.com/jadeshu/p/10663601.html | ★★★★☆ | 高质量内核分析 |
| 10 | CSDN 文库 — Windows 网络协议栈内部实现原理 | https://wenku.csdn.net/doc/x2za1cp4mx | ★★★★☆ | TechNet 课程内容 |
| 11 | CSDN 博客 — WIN7 系统内核网络堆栈实现简述 | https://blog.csdn.net/wzsy/article/details/50954596 | ★★★★☆ | NDIS/WFP/TDI 分析 |
| 12 | CSDN 博客 — 内核网络组件 AFD 与 Kernel Socket | https://blog.csdn.net/zhangyihu321/article/details/157906143 | ★★★★☆ | AFD 架构分析 |
| 13 | irishemin.github.io — Deep Dive: Windows DNS 客户端 | https://irishemin.github.io/knowledge_base/knowledge/networking/2026/03/27/deep-dive-dns-client-resolution-multi-nic-vpn/ | ★★★★☆ | DNS 客户端深度分析 |
| 14 | CSDN 文库 — SMB 协议演进史 | https://wenku.csdn.net/column/5iexva8qvapc | ★★★★☆ | SMB 安全与签名分析 |
| 15 | Dell Support — EternalBlue srv.sys | https://www.dell.com/support/kbdoc/zh-cn/000141143/ | ★★★★★ | 厂商官方文档 |
| 16 | 51CTO 博客 — 169.254.x.x 地址技术深度解析 | https://blog.51cto.com/u_14940497/14094397 | ★★★★☆ | APIPA 技术分析 |
| 17 | 技术栈 — Windows Socket API 与 LSP | https://jishuzhan.net/article/2021019608774410241 | ★★★★☆ | Winsock/LSP 分析 |
| 18 | CSDN 文库 — WFP 网络流量监控系统 | https://wenku.csdn.net/doc/7seqrjk6xt | ★★★★☆ | WFP 架构分析 |
| 19 | 掘金 — Sysinternals 工具概述 | https://juejin.cn/post/7633328194578055178 | ★★★★☆ | Sysinternals 工具分析 |
| 20 | CSDN 博客 — Windows Internals Svchost 拆分机制 | https://blog.csdn.net/weixin_47431459/article/details/160602470 | ★★★★☆ | Svchost 架构分析 |

---

> **免责声明**：本调研文档中的技术信息基于公开可用的资料，包括微软官方文档、《Windows Internals》书籍和社区技术文章。部分内容涉及 Windows 内部实现细节，可能随 Windows 版本更新而变化。
