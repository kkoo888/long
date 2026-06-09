# Sandbox（Docker 沙箱）技术指南

> 供 Peter Steinberger 技术顾问模式使用。当用户问「安全隔离」「沙箱」「Docker 执行」时，Agent 按需 read 本文件。

## 核心概念

OpenClaw 可以在 Docker 容器内执行工具（exec、read、write 等），降低 blast radius。Gateway 本身在宿主机运行，工具执行在沙箱内隔离。

**Peter 式解读**：模型5（信任自动化 > 权限控制）的技术实现——不锁权限，用容器隔离。出了事，容器没了，宿主机还在。💥

## 三种模式

| mode | 说明 | 适用场景 |
|------|------|---------|
| `"off"` | 不沙箱 | 开发环境、完全信任 |
| `"non-main"` | 仅非主会话沙箱 | 日常聊天在宿主机，sub-agent 在沙箱 |
| `"all"` | 所有会话沙箱 | 生产环境、安全敏感 |

## 三种 scope

| scope | 说明 | 适用场景 |
|-------|------|---------|
| `"session"` | 每个会话一个容器 | 最强隔离，默认值 |
| `"agent"` | 每个 Agent 一个容器 | 资源节约 |
| `"shared"` | 所有沙箱会话共享一个容器 | 最省资源 |

## 配置示例

```json5
{
  agents: {
    defaults: {
      sandbox: {
        mode: "non-main",       // 仅非主会话沙箱
        scope: "session",       // 每会话一个容器
        workspaceAccess: "full", // 沙箱可访问 workspace
        browser: {
          autoStart: true,      // 自动启动沙箱浏览器
          network: "openclaw-sandbox-browser"  // 独立 Docker 网络
        }
      }
    }
  }
}
```

## 沙箱边界

**被沙箱的**：
- 工具执行（exec、read、write、edit、apply_patch、process）
- 可选：浏览器（sandbox.browser）

**不被沙箱的**：
- Gateway 进程本身
- 显式允许在宿主机执行的工具（`tools.elevated`）

## Elevated Mode

`tools.elevated` 让特定命令绕过沙箱，在宿主机执行。
- 沙箱关闭时：elevated 无区别（已在宿主机）
- 沙箱开启时：elevated 命令在宿主机执行，非 elevated 在容器内

## 失败分支

| 场景 | 处理 |
|------|------|
| Docker 未安装 | `docker --version` 检查，安装 Docker |
| 容器启动失败 | `docker ps -a` 查看，检查镜像和资源 |
| 沙箱内网络不通 | 检查 Docker network 配置 |
| 沙箱浏览器 CDP 不可达 | 检查 `sandbox.browser.cdpSourceRange` |
| 文件权限问题 | 检查 `workspaceAccess` 配置 |
