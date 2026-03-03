# tapd-skills

本仓库通过 **MCP（Model Context Protocol）** 与 **Agent Skill** 两种方式提供 TAPD（腾讯敏捷研发管理平台）能力，可按使用场景任选其一或同时使用。

## 1. 通过 MCP 使用（推荐支持 MCP 的 IDE）

**目录**：[mcp-server-tapd/](mcp-server-tapd/)

以 MCP 服务形式暴露 TAPD 能力，供支持 MCP 的 IDE（如 Cursor、Claude Desktop、CodeBuddy）通过工具调用，完成需求、任务、缺陷、评论、迭代、测试用例、Wiki、工时、发布计划等操作。

- 安装与配置见 [mcp-server-tapd/README.md](mcp-server-tapd/README.md)。

## 2. TAPD Agent Skill（独立使用，不依赖 MCP）

**目录**：[skills/tapd/](skills/tapd/)

可在 Claude、Cursor、OpenClaw 等中作为 Agent Skill 加载，通过 TAPD 开放 API 完成上述操作，或发送企业微信通知。**不依赖 MCP 服务**，仅使用 Python 标准库或由 AI 按文档直接发 HTTP 请求。

- 使用方式与环境变量说明见 [skills/tapd/README.md](skills/tapd/README.md)。
- Skill 主文档：[skills/tapd/SKILL.md](skills/tapd/SKILL.md)。

---

同一套 TAPD 凭据（个人令牌或 API 账号密码）可同时用于 MCP Server 与 Agent Skill。
