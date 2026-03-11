# AI Bridge PHP

单文件 PHP 版 AI API 反向代理，专为解决中国大陆及香港地区无法直接访问 OpenAI、Claude、Google Gemini 等海外 AI 服务而设计。无需 Go 环境或 Docker，上传一个文件即可运行。

## 项目背景

由于网络环境和政策限制，中国大陆和香港的服务器无法稳定访问 OpenAI、Anthropic (Claude)、Google Gemini 等海外 AI 服务的 API。AI Bridge 通过在海外部署一台代理网关来解决这一问题 —— 国内服务器将 AI 请求发送到你的海外网关，再由网关转发至 AI 服务商。

本仓库是 AI Bridge 的 PHP 轻量版后端，适用于没有 Docker 环境的共享主机或虚拟主机。如果你的服务器支持 Docker，推荐使用性能更好的 [Go 版本](https://github.com/gentpan/ai-bridge-go)。

> **⚠️ 部署要求：** 本程序必须部署在能够正常访问海外 AI 服务 API 的服务器上，如美国、日本、新加坡等地区的主机。**请勿部署在中国大陆或香港服务器上**，否则仍然无法访问目标 API。

**安全保障：** API Key 仅在你自己的服务器上流转，不经过任何第三方平台，杜绝密钥泄露风险。

> **相关仓库**
>
> - WordPress 插件：[gentpan/global-ai-bridge](https://github.com/gentpan/global-ai-bridge)
> - Go 后端（推荐）：[gentpan/ai-bridge-go](https://github.com/gentpan/ai-bridge-go)

## 功能特性

- 单文件部署，无依赖
- 支持 OpenAI、Claude、Google Gemini、DeepSeek
- 自托管模式，直接使用 AI 服务商 API Key
- 兼容共享主机、虚拟主机环境

## 部署要求

- PHP 7.4+
- curl 扩展
- 允许出站 HTTPS 连接

## 快速开始

### 1. 下载

```bash
wget https://github.com/gentpan/ai-bridge-php/releases/latest/download/bridge.php
```

或从 [Releases](https://github.com/gentpan/ai-bridge-php/releases) 页面手动下载。

### 2. 上传到服务器

将 `bridge.php` 上传到 PHP 服务器的 Web 目录：

```
https://your-domain.com/ai-bridge/bridge.php
```

### 3. 验证部署

```bash
curl https://your-domain.com/ai-bridge/bridge.php/healthz
# 返回 {"ok":true, "mode":"self-hosted", ...}
```

### 4. 配置 WordPress 插件

1. 安装 [AI Bridge 插件](https://github.com/gentpan/global-ai-bridge)
2. 进入 WordPress 后台 → 工具 → AI Bridge
3. 连接方式选择「自定义地址」
4. 填入地址：`https://your-domain.com/ai-bridge/bridge.php/v1/chat/completions`
5. **AI Bridge 访问令牌**：留空（自托管不需要）
6. **模型 API Token**：填入你的 OpenAI / Claude / Gemini 等 API Key
7. 保存后点击「测速当前节点」验证

## 请求示例

```bash
curl -X POST https://your-domain.com/ai-bridge/bridge.php/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "X-AIBRIDGE-PROVIDER-TOKEN: sk-your-openai-key" \
  -d '{
    "provider": "openai",
    "model": "gpt-4.1-mini",
    "messages": [{"role": "user", "content": "Hello"}]
  }'
```

## 支持的提供商

| 提供商        | provider 值          | 需要的 API Key    |
| ------------- | -------------------- | ----------------- |
| OpenAI        | `openai`             | OpenAI API Key    |
| Claude        | `claude`             | Anthropic API Key |
| Google Gemini | `google` 或 `gemini` | Google AI API Key |
| DeepSeek      | `deepseek`           | DeepSeek API Key  |

## 自定义配置

编辑 `bridge.php` 顶部的 `$CONFIG` 数组：

```php
$CONFIG = [
    'debug' => false,
    'allowed_origins' => ['*'],
    'providers' => [
        'openai' => [
            'base_url' => 'https://api.openai.com/v1',
            'default_model' => 'gpt-4.1-mini',
        ],
        'claude' => [
            'base_url' => 'https://api.anthropic.com/v1',
        ],
        'google' => [
            'base_url' => 'https://generativelanguage.googleapis.com/v1beta',
        ],
        'deepseek' => [
            'base_url' => 'https://api.deepseek.com/v1',
        ],
    ],
];
```

## Nginx 配置（可选）

如果想隐藏 `.php` 后缀：

```nginx
location /ai-bridge/ {
    try_files $uri $uri/ /ai-bridge/bridge.php?$query_string;
}
```

配置后访问地址变为：`https://your-domain.com/ai-bridge/v1/chat/completions`

## 与 Go 版本的区别

| 特性           | PHP 版本   | Go 版本         |
| -------------- | ---------- | --------------- |
| 部署方式       | 单文件上传 | Docker / 二进制 |
| 部署要求       | PHP 7.4+   | 无依赖          |
| 性能           | 中等       | 高              |
| 内存占用       | 较高       | 低              |
| 托管模式       | ❌         | ✅              |
| Token 管理     | ❌         | ✅              |
| 使用量统计     | ❌         | ✅              |
| 邮件通知       | ❌         | ✅              |
| 速率限制       | ❌         | ✅              |
| Connector 代理 | ❌         | ✅              |

## 适用场景

- 共享主机 / 虚拟主机环境
- 已有 PHP 服务器，不想额外运行 Go 进程
- 快速测试 AI Bridge 功能
- 低流量个人站点

> 生产环境建议使用 [Go 版本](https://github.com/gentpan/ai-bridge)，性能更好、功能更完整。

## License

MIT
