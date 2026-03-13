# TAPD Skill

本目录为 **TAPD 的 Agent Skill**，可在不依赖 MCP 服务的情况下，通过 AI（如 Claude、Cursor、OpenClaw）直接调用 TAPD 开放 API，完成需求、任务、缺陷、评论、迭代、测试用例、Wiki、工时、发布计划等操作，或发送企业微信通知。

## 使用方式

- **在 Claude / Cursor / OpenClaw 等中**：将本 Skill 目录（或仓库）配置为可加载的 Skill 来源，使 AI 在用户提到 TAPD 相关操作时自动遵循 [SKILL.md](./SKILL.md) 中的说明。
- **无需安装 MCP server**：本 Skill 仅提供文档与可选的标准库 Python 脚本，由 AI 根据文档构造 HTTP 请求或运行脚本。

## 环境变量与认证

使用前请配置以下环境变量（由用户或运行脚本的环境提供）；OpenClaw 等运行时可从 SKILL.md 的 metadata 读取 `primaryEnv`、`requires.env`：

| 变量名 | 必填 | 说明 |
|--------|------|------|
| TAPD_ACCESS_TOKEN | 二选一 | 个人访问令牌（推荐）；建议使用最小权限、短期有效的令牌 |
| TAPD_API_USER | 二选一 | API 账号 |
| TAPD_API_PASSWORD | 二选一 | API 密钥 |
| TAPD_API_BASE_URL | 可选 | API 根地址，默认 `https://api.tapd.cn`；自定义时请确保指向可信端点 |
| TAPD_BASE_URL | 可选 | 前端地址，用于生成可点击链接，默认 `https://www.tapd.cn` |
| BOT_URL | 可选 | 企业微信机器人 webhook（仅发送群消息时需要）；请确保指向可信 webhook 地址 |
| CURRENT_USER_NICK | 可选 | 当前用户昵称（参与项目、待办、工时等查询的默认 nick） |

认证二选一：**TAPD_ACCESS_TOKEN** 或 **TAPD_API_USER + TAPD_API_PASSWORD**。凭据在 TAPD 后台「个人设置」→「开放平台」中创建。

### 安全说明

- **TAPD_ACCESS_TOKEN**：建议使用**最小权限、短期有效**的令牌。在 TAPD 开放平台中为该技能单独创建令牌，仅授予所需项目/接口权限，并设置较短过期时间。
- **BOT_URL 与 TAPD_API_BASE_URL**：使用前请确认 `BOT_URL`（企业微信 webhook）和自定义的 `TAPD_API_BASE_URL` 均指向**可信端点**（如官方 `https://api.tapd.cn` 或贵司自建 TAPD、企业微信官方 webhook），避免指向不可信第三方。
- **可选加固**：若需更强安全，可在沙盒环境中运行 Python 客户端，并限制网络仅允许访问 TAPD API 与 BOT_URL，以确认出站请求仅发往预期端点。

## 命令行快速调用

脚本仅使用 Python 标准库，配置好环境变量后用 **python3** 运行即可，输出 JSON 到 stdout：

```bash
python3 scripts/tapd_client_stdlib.py projects [--nick 用户昵称]
python3 scripts/tapd_client_stdlib.py workspace --workspace-id <项目ID>
python3 scripts/tapd_client_stdlib.py stories --workspace-id <项目ID> [--limit 10] [--entity-type stories|tasks]
python3 scripts/tapd_client_stdlib.py bugs --workspace-id <项目ID> [--limit 10]
python3 scripts/tapd_client_stdlib.py get --endpoint "stories/count" -p workspace_id=<ID> -p entity_type=stories
python3 scripts/tapd_client_stdlib.py post --endpoint "stories" -b '{"workspace_id":123,"name":"需求标题"}'
```

更多子命令与参数见 [SKILL.md](./SKILL.md) 中的「命令行调用方式」一节，或运行 `python3 scripts/tapd_client_stdlib.py --help`。

## 目录结构

- **SKILL.md**：Skill 说明、操作清单与**命令行调用方式**，AI 主入口；配置（metadata、openclaw.requires/primaryEnv）在 frontmatter 中。
- **pyproject.toml**：Python 项目配置，无第三方依赖。
- **reference/api_reference.md**：API 端点与参数速查。
- **scripts/tapd_client_stdlib.py**：仅用 Python 标准库的客户端，支持**命令行子命令**与 Python import。

## 依赖

- **Python 3**：脚本仅用 Python 3 标准库，无第三方包依赖；不依赖 MCP。直接使用 `python3` 运行即可。

## 发布到 OpenClaw / ClawHub

本 Skill 已按 [OpenClaw Skills](https://docs.openclaw.ai/skills) 与 [ClawHub](https://docs.openclaw.ai/tools/clawhub) 规范编写，可直接在 OpenClaw 工作区使用或发布到 ClawHub 技能市场。

### 在 OpenClaw 中使用

1. 将本目录复制到 OpenClaw 技能目录，例如：
   - 工作区技能：`<你的工作区>/skills/tapd`
   - 本地技能：`~/.openclaw/skills/tapd`
2. 在 `~/.openclaw/openclaw.json` 中配置环境变量（可选，也可在系统环境里配置）：
   ```json5
   {
     skills: {
       entries: {
         tapd: {
           enabled: true,
           env: {
             TAPD_ACCESS_TOKEN: "你的令牌",
             // TAPD_API_BASE_URL、TAPD_BASE_URL 有默认值，仅自建环境需覆盖
           },
         },
       },
     },
   }
   ```
3. 重启 OpenClaw 或刷新技能后即可使用。

### 发布到 ClawHub

1. 安装 ClawHub CLI：`npm i -g clawhub`
2. 登录：`clawhub login`
3. 在**仓库根目录**执行（`skills/tapd` 为当前技能目录）：
   ```bash
   clawhub publish skills/tapd --slug tapd --name "TAPD" --version 1.0.0 --tags latest --changelog "初始发布"
   ```
4. 之后更新版本可改用 `clawhub sync` 或再次 `clawhub publish` 并指定新版本号。
