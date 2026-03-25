# Gemini CLI 国内使用完整教程（2026 最新）

> Gemini CLI 是 Google 推出的开源终端 AI 编程工具，基于 Gemini 模型，GitHub 上已有 96K+ Star。本文记录了国内开发者从安装到使用的完整过程。

---

## 什么是 Gemini CLI

Gemini CLI 是 Google 在 2025 年开源的终端 AI 编程工具，Apache 2.0 协议，Node.js 编写。它是 Claude Code、Codex CLI 之外的第三个主流终端 AI Agent，特点是：

- 基于 Gemini 模型（2.5 Pro / 3 Pro），百万级上下文窗口
- 多模态能力 — 可以处理文本、图片、视频、音频
- 完全开源，社区活跃（GitHub 96K+ Star）
- Google 账号免费使用（有额度限制）
- 支持 MCP（Model Context Protocol）扩展

跟 Claude Code 和 Codex CLI 一样，它直接在终端里运行，能理解你的代码库、写代码、执行命令。

## 安装 Gemini CLI

### 系统要求

- macOS、Linux、或 Windows
- Node.js 18+

### 安装命令

```bash
npm install -g @anthropic-ai/gemini-cli
```

或者直接用 npx 免安装运行：

```bash
npx @anthropic-ai/gemini-cli
```

> ⚠️ npm 慢的话：`npm config set registry https://registry.npmmirror.com`

### 验证安装

```bash
gemini --version
```

## 官方使用方式

Gemini CLI 官方支持两种认证方式：

1. **Google 账号登录** — 免费，有每日额度限制
2. **API Key** — 从 Google AI Studio 获取，按量付费

```bash
# 方式一：Google 账号（首次启动会引导登录）
gemini

# 方式二：API Key
export GEMINI_API_KEY=your-google-api-key
gemini
```

## 国内的问题

Google 的服务在国内同样面临访问限制：

**网络问题：**
- Google AI Studio 和 Gemini API 在国内无法直接访问
- Google 账号登录需要代理
- API 请求被拦截

**账号问题：**
- Google AI Studio 的 API Key 需要科学上网才能获取
- 部分地区 IP 被 Google 限制

**不过 Gemini CLI 有个优势：** 它是开源的，支持自定义 API 端点，可以通过 OpenAI 兼容接口使用。

## 我的方案：通过 OpenAI 兼容接口中转

Gemini CLI 支持 OpenAI 兼容模式。配合 [MaxRouter](https://www.maxrouter.ai) 中转，可以在国内直连使用 Gemini 模型。

MaxRouter 支持所有 Gemini 模型，与官方同价，按量计费。同一个 Key 还能调用 Claude、GPT、DeepSeek 等 80+ 模型。

## 配置步骤

### 1. 注册 MaxRouter

访问 [www.maxrouter.ai](https://www.maxrouter.ai) 注册，送 ¥10 体验金。

进入控制台 → 密钥管理 → 创建密钥 → 复制 Key。

### 2. 配置 Gemini CLI

Gemini CLI 支持通过配置文件设置自定义 API 端点。编辑 `~/.gemini/settings.json`：

```json
{
  "models": {
    "default": {
      "provider": "openai",
      "model": "gemini-2.5-pro",
      "apiKey": "sk-your-maxrouter-key",
      "baseUrl": "https://api.maxrouter.ai/v1"
    }
  }
}
```

或者直接通过环境变量：

```bash
# ~/.zshrc 或 ~/.bashrc
export OPENAI_API_KEY=sk-your-maxrouter-key
export OPENAI_BASE_URL=https://api.maxrouter.ai/v1
```

### 3. 启动

```bash
gemini
```

## 支持的 Gemini 模型

| 模型 | 说明 |
|------|------|
| `gemini-2.5-pro` | 最强版本，百万级上下文 |
| `gemini-2.5-flash` | 快速版本，性价比高 |
| `gemini-2.0-flash` | 上一代快速版本 |

## 日常使用

Gemini CLI 的使用方式跟 Claude Code 类似：

```
> 帮我分析这个项目的架构，画一个依赖关系图

> 这个函数有性能问题，帮我优化

> 把这个 Python 脚本改写成 Go

> 帮我写一套完整的 API 测试用例
```

### Gemini CLI 的独特优势

- **超长上下文：** Gemini 2.5 Pro 支持 100 万 token 上下文，可以一次性读取整个大型项目
- **多模态：** 可以传入截图让它分析 UI 问题
- **免费额度：** 如果能直连 Google，每天有免费使用额度

## 三大终端 AI 工具对比

| 特性 | Claude Code | Codex CLI | Gemini CLI |
|------|------------|-----------|------------|
| 厂商 | Anthropic | OpenAI | Google |
| 底层模型 | Claude 4 系列 | GPT-5 / o3 | Gemini 2.5/3 |
| 开源 | ❌ | ✅ MIT | ✅ Apache 2.0 |
| 上下文 | 200K | 128K | 1M |
| 多模态 | 有限 | 支持 | 强 |
| 代码能力 | 最强 | 强 | 强 |
| 安全沙箱 | 有 | 有（OS 级） | 有 |

三个工具各有优势，建议都试试。通过 MaxRouter 可以用同一个 Key 在不同工具间切换。

## 配合其他工具

同一个 MaxRouter Key 还能用在：

**Cursor：**
- Override OpenAI Base URL: `https://api.maxrouter.ai/v1`
- 模型选 `gemini-2.5-pro`

**Python 项目：**

```python
from openai import OpenAI
client = OpenAI(
    api_key="sk-your-key",
    base_url="https://api.maxrouter.ai/v1"
)
response = client.chat.completions.create(
    model="gemini-2.5-pro",
    messages=[{"role": "user", "content": "你好"}]
)
```

## 常见问题

**Q: Gemini CLI 跟 Claude Code 比怎么样？**
代码能力上 Claude Code 目前更强一些，但 Gemini 的超长上下文（100 万 token）在处理大型项目时有优势。建议根据场景选择。

**Q: 免费额度够用吗？**
Google 账号的免费额度适合轻度使用。重度使用建议按量付费。

**Q: 支持流式输出吗？**
支持。

## 相关链接

- [Gemini CLI GitHub](https://github.com/google-gemini/gemini-cli)
- [Google AI Studio](https://aistudio.google.com)
- [MaxRouter](https://www.maxrouter.ai) — 我用的 API 平台
- [MaxRouter 文档](https://www.maxrouter.ai/docs)
- [Claude Code 国内教程](https://github.com/MaxRouterAI/claude-code-guide)
- [Codex CLI 国内教程](https://github.com/MaxRouterAI/openai-api-guide)

---

觉得有用就给个 ⭐ Star，有问题欢迎提 Issue。
