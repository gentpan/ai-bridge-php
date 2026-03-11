# AI Bridge PHP

单文件 PHP 版 AI API 反向代理，专为解决中国大陆及香港地区无法直接访问 OpenAI、Claude、Google Gemini 等海外 AI 服务而设计。无需 Go 环境或 Docker，上传一个文件即可运行。

## 项目背景

由于网络环境和政策限制，中国大陆和香港的服务器无法稳定访问 OpenAI、Anthropic (Claude)、Google Gemini 等海外 AI 服务的 API。虽然本地开发时可以借助代理工具，但对于线上运行的 WordPress 站点或其他服务端应用，直接从国内/港区服务器调用这些 AI API 几乎不可行。

AI Bridge 通过在海外部署一台轻量级代理网关来解决这一问题 —— 国内服务器将 AI 请求发送到你的海外网关，再由网关转发至 AI 服务商，实现稳定、安全的 AI API 访问。

本仓库是 AI Bridge 的 PHP 轻量版后端，适用于没有 Docker 环境的共享主机或虚拟主机。如果你的服务器支持 Docker，推荐使用性能更好的 [Go 版本](https://github.com/gentpan/ai-bridge-go)。

> **⚠️ 部署要求：** 本程序必须部署在能够正常访问海外 AI 服务 API 的服务器上，如美国、日本、新加坡等地区的主机。**请勿部署在中国大陆或香港服务器上**，否则仍然无法访问目标 API。

**安全保障：** API Key 仅在你自己的服务器上流转，不经过任何第三方平台，杜绝密钥泄露风险。

> **相关仓库**
>
> - WordPress 插件：[gentpan/global-ai-bridge](https://github.com/gentpan/global-ai-bridge)
> - Go 后端（推荐）：[gentpan/ai-bridge-go](https://github.com/gentpan/ai-bridge-go)

## 工作原理

本程序是一个纯粹的 **反向代理**，不存储任何 API Key，不缓存任何对话内容：

```
┌─────────────┐         ┌─────────────────┐         ┌─────────────────┐
│  WordPress  │  POST   │   bridge.php    │  POST   │   AI 服务商      │
│  (国内服务器) │ ──────→ │  (海外 PHP 主机) │ ──────→ │  (OpenAI 等)    │
│             │         │                 │         │                 │
│  插件发送    │         │  1. 读取 provider│         │                 │
│  AI 请求    │         │  2. 查内置地址表  │         │                 │
│             │         │  3. 用你的 API   │         │                 │
│             │ ←────── │     Key 转发请求 │ ←────── │  返回 AI 响应    │
│  收到响应    │  JSON   │  4. 原样回传结果  │  JSON   │                 │
└─────────────┘         └─────────────────┘         └─────────────────┘
```

## 功能特性

- 单文件部署，无依赖
- 支持 OpenAI、Claude、Google Gemini、DeepSeek
- 无需鉴权，直接使用 AI 服务商 API Key
- 兼容共享主机、虚拟主机环境
- 要求 PHP 7.4+，curl 扩展

## 快速开始

将 `bridge.php` 上传到海外 PHP 服务器的 Web 目录：

```
https://your-domain.com/ai-bridge/bridge.php
```

### 验证

```bash
curl https://your-domain.com/ai-bridge/bridge.php/healthz
# {"ok":true, "mode":"Self-Hosted", ...}
```

## WordPress 插件配置

1. 安装 [AI Bridge 插件](https://github.com/gentpan/global-ai-bridge)
2. 进入 WordPress 后台 → 工具 → AI Bridge
3. 连接方式选择「使用自己的服务器（自托管）」
4. 填入后端地址：`https://your-domain.com/ai-bridge/bridge.php/v1/chat/completions`
5. **AI Bridge 访问令牌**：留空
6. **模型 API Token**：填入你的 OpenAI / Claude 等 API Key
7. 保存后点击「测速当前节点」验证

## 更多信息

- [Go 后端（推荐）](https://github.com/gentpan/ai-bridge-go)
- [WordPress 插件](https://github.com/gentpan/global-ai-bridge)

## License

MIT
