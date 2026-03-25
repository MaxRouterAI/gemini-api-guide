# Gemini CLI 完整教程：从安装到实战（国内开发者指南）

> Gemini CLI 是 Google 推出的开源终端 AI 编程工具，GitHub 96K+ Star，基于 Gemini 模型，拥有百万级上下文窗口。这篇文章记录了我在国内从安装到日常使用的完整过程。

---

## 什么是 Gemini CLI

Gemini CLI 是 Google 在 2025 年开源的命令行 AI 编程工具，Apache 2.0 协议，Node.js 编写。它是继 Claude Code、Codex CLI 之后的第三个主流终端 AI Agent。

核心能力：

- 基于 Gemini 模型（2.5 Pro / 3 Pro），支持百万级上下文窗口
- 多模态 — 可以处理文本、图片、视频、音频
- 阅读和理解整个代码库，自主编写和修改文件
- 执行终端命令，管理项目
- 支持 MCP（Model Context Protocol）扩展
- 完全开源，社区活跃

跟 Claude Code 和 Codex CLI 相比，Gemini CLI 最大的优势是超长上下文（100 万 token）和多模态能力。处理大型项目时，它可以一次性读取整个代码库而不用担心上下文溢出。

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

验证安装：

```bash
gemini --version
```

> npm 慢的话先换镜像：`npm config set registry https://registry.npmmirror.com`

## 官方使用方式

Gemini CLI 官方支持两种认证方式：

### 方式一：Google 账号登录（免费）

首次启动时会引导你登录 Google 账号，有每日免费额度：

```bash
gemini
# 按提示在浏览器中登录 Google 账号
```

免费额度对轻度使用够了，但重度编程会很快用完。

### 方式二：API Key（按量付费）

从 [Google AI Studio](https://aistudio.google.com) 获取 API Key：

```bash
export GEMINI_API_KEY=your-google-api-key
gemini
```

按 token 用量计费，没有月费上限。

## 国内使用的现实问题

Google 的服务在国内面临的问题大家都懂：

**网络层面：**
- Google AI Studio 和 Gemini API 在国内无法直接访问
- Google 账号登录需要代理
- API 请求被拦截，即使有 Key 也用不了

**账号层面：**
- Google AI Studio 获取 API Key 需要科学上网
- 部分地区 IP 被 Google 限制
- Google 账号本身在国内注册和使用都不方便

**跟 Claude Code 和 Codex CLI 遇到的问题本质一样** — 海外 AI 服务在国内都有网络障碍。详细分析可以看 [Claude Code 国内使用教程](https://github.com/MaxRouterAI/claude-code-guide#国内使用的现实问题)。

## 我的方案：通过 OpenAI 兼容接口中转

Gemini CLI 支持自定义 API 端点，可以通过 OpenAI 兼容接口使用。配合中转服务，国内直连就能用。

我用的是 [MaxRouter](https://www.maxrouter.ai)，之前用 Claude Code 和 Codex CLI 时就在用这个平台。同一个 Key 可以调用所有模型 — Claude、GPT、Gemini、DeepSeek 等 80+ 模型，不用分别注册不同厂商的账号。

## 配置步骤

### 1. 注册并获取 API Key

如果你之前已经注册过 MaxRouter（比如跟着 [Claude Code 教程](https://github.com/MaxRouterAI/claude-code-guide) 或 [Codex CLI 教程](https://github.com/MaxRouterAI/openai-api-guide)），直接用同一个 Key。

没注册的话，访问 [www.maxrouter.ai](https://www.maxrouter.ai) 注册（送 ¥10 体验金），进入控制台 → 密钥管理 → 创建密钥。

### 2. 配置 Gemini CLI

编辑 Gemini CLI 的配置文件 `~/.gemini/settings.json`：

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

或者通过环境变量（更简单）：

```bash
# ~/.zshrc 或 ~/.bashrc
export OPENAI_API_KEY=sk-your-maxrouter-key
export OPENAI_BASE_URL=https://api.maxrouter.ai/v1
```

### 3. 启动

```bash
gemini
```

配置正确就能正常使用了。

## 支持的 Gemini 模型

| 模型 | 上下文 | 说明 |
|------|--------|------|
| `gemini-2.5-pro` | 1M tokens | 最强版本，百万级上下文 |
| `gemini-2.5-flash` | 1M tokens | 快速版本，性价比高 |
| `gemini-2.0-flash` | 1M tokens | 上一代快速版本 |

Gemini CLI 默认会自动选择最合适的模型，也可以手动指定：

```bash
gemini --model gemini-2.5-pro
```

或者在交互模式中切换：

```
/model gemini-2.5-pro
```

## 日常使用

### 基本操作

Gemini CLI 的交互方式跟 Claude Code 类似，用自然语言描述需求：

```
> 帮我分析这个项目的架构，画一个模块依赖图

> 这个函数有性能问题，帮我优化

> 把这个 Python 脚本改写成 Go，保持功能一致

> 帮我写一套完整的 API 集成测试
```

### Gemini CLI 的独特优势

**超长上下文：**

Gemini 2.5 Pro 支持 100 万 token 上下文，这意味着它可以一次性读取一个大型项目的所有代码。对比 Claude Code（200K）和 Codex CLI（128K），在处理大型代码库时 Gemini CLI 有明显优势：

```
> 阅读整个项目（包括所有子模块），给我一份完整的架构分析报告
```

这种任务在其他工具上可能会因为上下文不够而需要分批处理，Gemini CLI 可以一次搞定。

**多模态能力：**

Gemini 的多模态能力很强，你可以传入截图让它分析：

```
> 看看这个 UI 截图，帮我用 React 实现一样的界面

> 这个报错截图是什么问题，帮我修复
```

**MCP 扩展：**

Gemini CLI 支持 Model Context Protocol，可以连接外部工具和数据源，扩展能力边界。

### 实际场景

**大型项目分析：**

```
> 这个项目有 500 多个文件，帮我理清核心模块之间的调用关系，找出可以优化的地方
```

**跨语言迁移：**

```
> 把这个 Java Spring Boot 项目的核心逻辑迁移到 Go + Gin，保持 API 接口不变
```

**文档生成：**

```
> 阅读所有源码，生成一份完整的 API 文档，包含请求参数、响应格式和示例
```

**代码审查：**

```
> 审查最近一周的所有 commit，找出潜在的 bug 和安全问题
```

## 三大终端 AI 工具对比

我三个工具都在用，各有适合的场景：

| 特性 | Claude Code | Codex CLI | Gemini CLI |
|------|------------|-----------|------------|
| 厂商 | Anthropic | OpenAI | Google |
| 底层模型 | Claude 4 系列 | GPT-5 / o3 | Gemini 2.5/3 |
| 开源 | ❌ | ✅ MIT | ✅ Apache 2.0 |
| 上下文 | 200K | 128K | **1M** |
| 多模态 | 有限 | 支持 | **最强** |
| 代码理解 | **最强** | 强 | 强 |
| 安全沙箱 | 有 | **OS 级** | 有 |
| 推理能力 | Opus 4 很强 | o3 很强 | 强 |

**我的使用习惯：**
- 日常编程、代码审查 → Claude Code（Sonnet 4）
- 需要安全隔离执行 → Codex CLI（full-auto 模式）
- 大型项目分析、多模态任务 → Gemini CLI（超长上下文）

通过 MaxRouter 可以用同一个 Key 在三个工具间自由切换，不用管理多个账号。

## 进阶技巧

### GEMINI.md 文件

跟 Claude Code 的 `CLAUDE.md` 和 Codex CLI 的 `AGENTS.md` 类似，在项目根目录放一个说明文件，Gemini CLI 启动时会自动读取：

```markdown
# GEMINI.md

## 项目简介
微服务电商系统，Go + gRPC + Kubernetes

## 架构
- gateway/ — API 网关
- user-service/ — 用户服务
- order-service/ — 订单服务
- payment-service/ — 支付服务

## 编码规范
- 错误处理用 pkg/errors
- 日志用 zap
- 配置用 viper

## 常用命令
- make build — 构建所有服务
- make test — 跑测试
- make deploy-dev — 部署到开发环境
```

### 利用超长上下文

Gemini CLI 的 100 万 token 上下文是它最大的武器。充分利用这个优势：

```
> 我要重构整个项目的错误处理机制，先阅读所有文件，理解当前的错误处理方式，然后给我一个统一的方案，最后逐个文件修改
```

其他工具可能需要你手动指定文件范围，Gemini CLI 可以直接处理整个项目。

## 配合其他工具

同一个 MaxRouter Key 还能用在：

**Cursor：**
- Override OpenAI Base URL: `https://api.maxrouter.ai/v1`
- 模型选 `gemini-2.5-pro`

**Python 项目集成：**

```python
from openai import OpenAI

client = OpenAI(
    api_key="sk-your-key",
    base_url="https://api.maxrouter.ai/v1"
)

response = client.chat.completions.create(
    model="gemini-2.5-pro",
    messages=[{"role": "user", "content": "分析这段代码的时间复杂度"}]
)
print(response.choices[0].message.content)
```

**Node.js 项目集成：**

```javascript
import OpenAI from 'openai';

const client = new OpenAI({
    apiKey: 'sk-your-key',
    baseURL: 'https://api.maxrouter.ai/v1'
});

const response = await client.chat.completions.create({
    model: 'gemini-2.5-flash',
    messages: [{ role: 'user', content: '帮我优化这个 SQL 查询' }]
});
console.log(response.choices[0].message.content);
```

## 常见问题

**Q: Gemini CLI 跟 Claude Code 比怎么样？**
代码理解和重构 Claude Code 更强，但 Gemini 的超长上下文（100 万 token）在处理大型项目时有明显优势。多模态能力 Gemini 也更强。建议根据场景选择。

**Q: 免费额度够用吗？**
Google 账号的免费额度适合轻度试用。日常编程建议按量付费，通过 MaxRouter 中转更方便。

**Q: 报错连接超时？**
配置了 MaxRouter 中转就不会有这个问题，国内直连。

**Q: 支持流式输出吗？**
支持，体验流畅。

**Q: 数据安全？**
MaxRouter 不存储对话内容和代码，请求直接转发到官方 API。

**Q: 一个 Key 能同时用 Claude Code、Codex CLI 和 Gemini CLI 吗？**
可以。MaxRouter 的 Key 支持所有模型，三个工具可以同时使用，各自配置对应的环境变量就行。

## 相关链接

- [Gemini CLI GitHub](https://github.com/google-gemini/gemini-cli)
- [Google AI Studio](https://aistudio.google.com)
- [Gemini 官方文档](https://ai.google.dev/docs)
- [MaxRouter](https://www.maxrouter.ai) — 我用的 API 平台
- [MaxRouter 文档](https://www.maxrouter.ai/docs)
- [Claude Code 国内教程](https://github.com/MaxRouterAI/claude-code-guide)
- [Codex CLI 国内教程](https://github.com/MaxRouterAI/openai-api-guide)

---

如果这篇教程帮到了你，给个 ⭐ Star 让更多人看到。有问题欢迎提 Issue。
