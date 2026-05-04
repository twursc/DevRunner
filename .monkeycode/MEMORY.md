# 用户指令记忆

本文件记录了用户的指令、偏好和教导，用于在未来的交互中提供参考。

## 格式

### 用户指令条目
用户指令条目应遵循以下格式：

[用户指令摘要]
- Date: [YYYY-MM-DD]
- Context: [提及的场景或时间]
- Instructions:
  - [用户教导或指示的内容，逐行描述]

### 项目知识条目
Agent 在任务执行过程中发现的条目应遵循以下格式：

[项目知识摘要]
- Date: [YYYY-MM-DD]
- Context: Agent 在执行 [具体任务描述] 时发现
- Category: [代码结构|代码模式|代码生成|构建方法|测试方法|依赖关系|环境配置]
- Instructions:
  - [具体的知识点，逐行描述]

## 去重策略
- 添加新条目前，检查是否存在相似或相同的指令
- 若发现重复，跳过新条目或与已有条目合并
- 合并时，更新上下文或日期信息
- 这有助于避免冗余条目，保持记忆文件整洁

## 条目

[依赖来源偏好]
- Date: 2026-04-10
- Context: 用户在新增 Java 开发镜像任务中强调依赖来源要求
- Instructions:
  - 所有语言运行时和工具链都应优先从官方渠道下载或安装。
  - 禁止使用用户认为不够正式或不够稳定的第三方直链发布地址。
  - 当用户要求 Java 使用 OpenJDK 时，优先使用 OpenJDK 官方直链下载，而不是第三方发行版仓库。

[镜像仓库结构与构建方式]
- Date: 2026-04-10
- Context: Agent 在执行新增 Java 开发镜像任务时发现
- Category: 代码结构
- Instructions:
  - 镜像目录遵循 `docker/{stack}/{version}/Dockerfile` 结构。
  - `base` 镜像位于 `docker/base/bookworm/Dockerfile`，其他语言栈基于 `ghcr.io/chaitin/monkeycode-runner/base:bookworm` 扩展。

[本地构建与 CI 约定]
- Date: 2026-04-10
- Context: Agent 在执行新增 Java 开发镜像任务时发现
- Category: 构建方法
- Instructions:
  - 本地通过 `STACK=<stack> VERSION=<version> ./scripts/build.sh` 构建镜像。
  - GitHub Actions 在 `.github/workflows/ci.yaml` 的 `build-stacks` matrix 中维护需要构建的语言栈。
  - README 需要同步维护每个镜像的构建、推送和运行示例。
