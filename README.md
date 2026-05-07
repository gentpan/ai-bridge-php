<div align="center">

# AI Bridge PHP

**单文件 PHP AI API 反向代理 · 上传即用，无需 Docker**

<p>
  <img src="https://img.shields.io/badge/PHP-7.4%2B-777BB4?style=for-the-badge&logo=php&logoColor=white" alt="PHP">
  <img src="https://img.shields.io/badge/license-MIT-brightgreen?style=for-the-badge" alt="License">
  <img src="https://img.shields.io/badge/deploy-single--file-2ea44f?style=for-the-badge" alt="Single File">
  <img src="https://img.shields.io/github/v/release/gentpan/ai-bridge-php?style=for-the-badge" alt="Release">
  <img src="https://img.shields.io/github/stars/gentpan/ai-bridge-php?style=for-the-badge" alt="Stars">
</p>

<p>
  <a href="https://github.com/gentpan/ai-bridge-go">Go 版（推荐）</a> ·
  <a href="https://github.com/gentpan/ai-bridge">WordPress 插件</a>
</p>

</div>

---

## 📖 概述

AI Bridge PHP 是一个**单文件** PHP AI API 反向代理，专为解决中国大陆及香港地区无法直接访问 OpenAI、Claude、Google Gemini 等海外 AI 服务而设计。

**无需 Go 环境，无需 Docker，上传一个文件即可运行。** 适合共享主机、虚拟主机等轻量环境。

> ⚠️ **部署要求：** 必须部署在能正常访问海外 AI 服务 API 的服务器上（美国、日本、新加坡等地区）。**请勿部署在中国大陆或香港服务器上。**

---

## 🔧 工作原理

本程序是一个纯粹的**反向代理**，不存储任何 API Key，不缓存任何对话内容：

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

---

## ✨ 特性

- 📄 **单文件部署** — 一个 `bridge.php`，零依赖
- 🤖 **多平台支持** — OpenAI、Claude、Google Gemini、DeepSeek
- 🔑 **绝对安全** — API Key 仅在你服务器上流转
- 🏠 **共享主机友好** — 无需 SSH root 权限，虚拟主机也能跑
- ⚡ **极低开销** — PHP 7.4+，仅需 curl 扩展

---

## 🚀 快速开始

### 1. 部署

将 `bridge.php` 上传到海外 PHP 服务器的 Web 目录：

```
https://your-domain.com/ai-bridge/bridge.php
```

### 2. 验证

```bash
curl https://your-domain.com/ai-bridge/bridge.php/healthz
# {"ok":true, "mode":"Self-Hosted", ...}
```

### 3. WordPress 插件配置

1. 安装 [AI Bridge 插件](https://github.com/gentpan/ai-bridge)
2. 进入 WordPress 后台 → 工具 → AI Bridge
3. 连接方式选择「使用自己的服务器（自托管）」
4. 填入后端地址：`https://your-domain.com/ai-bridge/bridge.php/v1/chat/completions`
5. **AI Bridge 访问令牌**：留空
6. **模型 API Token**：填入你的 OpenAI / Claude 等 API Key
7. 保存后点击「测速当前节点」验证

---

## 🔗 关联项目

| 仓库 | 说明 |
|------|------|
| [ai-bridge](https://github.com/gentpan/ai-bridge) | WordPress 插件 + Go 后端组合包 |
| [ai-bridge-go](https://github.com/gentpan/ai-bridge-go) | Go 版后端代理（高性能推荐，Docker 部署） |

---

## 📄 License

MIT
