# Consistency Guard

AI 改代码最危险的地方，往往不是某一行写错，而是只改了一层。

数据库字段改了，DTO 没改；接口返回变了，前端类型还旧；权限规则动了，测试和文档没跟上；迁移脚本看似能跑，但回滚、缓存、SDK、兼容版本全没人检查。`consistency-guard` 就是为这些问题准备的 AI Skill。

它让 Codex 或其他支持 Skills 的 AI Agent 在修改、审查全栈项目时，先建立“哪些东西互相依赖”的一致性视图，再决定能不能改、该改哪里、怎么验证、怎么回滚。

## 它有什么用

- 防止 AI 只改局部，漏掉 schema、API、类型、表单、缓存、测试、文档等关联层。
- 帮 AI 找到真正的 source of truth，而不是误把生成文件、SDK、快照或文档当成权威。
- 在权限、支付、钱包、审计、上传、限流等高风险领域要求更严格的证据和人工决策。
- 要求每次修改都有风险等级、证据、验证结果和回滚计划。
- 在信息不足时让 AI 停下来说明缺什么，而不是编业务规则。

## 这是给谁用的

这是一个 AI Skills 技能，不是普通 npm 包或命令行工具。

把它安装到 Codex 的 skills 目录后，当你让 AI 做这些事时，它会按本 skill 的流程工作：

- 修改数据库 schema、迁移、ORM model、repository、service、DTO、validator。
- 修改 REST、GraphQL、OpenAPI、gRPC、SDK 或前后端共享类型。
- 审查涉及业务规则、权限、缓存、兼容版本、交易链路、财务流的改动。
- 做全栈一致性 review，确认一个改动有没有漏改上下游。
- 给复杂修复生成验证清单、风险说明和回滚方案。

## 安装

将本仓库放到 Codex skills 目录，例如：

```powershell
git clone https://github.com/ixbaicn/consistency-guard.git $env:USERPROFILE\.codex\skills\consistency-guard
```

或者放到你的 `$CODEX_HOME/skills/consistency-guard` 目录。

## 使用

在 Codex 里直接要求使用这个 skill：

```text
Use $consistency-guard to review this change for schema/API/type drift and missing verification.
```

也可以用中文：

```text
使用 consistency-guard 检查这次改动有没有数据库、接口、前端类型、缓存和文档不一致的问题。
```

## 它会要求 AI 输出什么

典型输出会包含：

- 当前 AI 能读、能改、能执行、能验证什么。
- 项目 profile、adapter、权威契约和非目标范围。
- 受影响的 schema、API、类型、缓存、测试、文档节点。
- 漂移问题列表，按严重程度和证据排序。
- 修复队列、风险等级、是否需要人工决策。
- 验证命令、未能验证的项目、手动检查清单。
- 回滚计划和最终退出条件。

## 目录

```text
consistency-guard/
  SKILL.md
  agents/openai.yaml
  references/
    artifact-discovery.md
    consistency-graph.md
    drift-rules.md
    execution-protocol.md
    project-adapters.md
    repair-strategy.md
    verification-checklists.md
    adapters-and-extension.md
    engineer-lenses.md
```

`SKILL.md` 是主协议；`references/` 里是按需加载的细分规则，避免 AI 一次性吞太多上下文。

## 适合的提示词

```text
Use $consistency-guard before editing this API contract.
```

```text
用 consistency-guard 审查这个 PR，重点看 schema、DTO、前端类型和缓存有没有漂移。
```

```text
这个变更涉及支付和权限，请用 consistency-guard 给出风险、验证和回滚计划。
```

## 设计原则

- 证据优先：没有证据就不报高置信度。
- 权威优先：先找 source of truth，再判断谁该跟谁改。
- 图优先：从依赖图推导影响范围，不靠猜。
- 风险优先：高风险领域默认需要更强验证或人工决策。
- 退出条件优先：不能验证就不能声称一致。
