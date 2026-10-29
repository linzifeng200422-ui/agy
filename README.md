# AGY 协作 Skill

供 Codex 等支持 `SKILL.md` 的本地代理使用的 AGY（Antigravity CLI）协作封装。提供通用执行、研究、复审、实现、简短问答五种角色，以及后台任务的等待、结果收集、取消和续接。

本项目基于 [keli-wen/agy-staff](https://github.com/keli-wen/agy-staff) `0.7.3` 改造，固定上游提交与文件指纹见 [provenance.json](provenance.json)。这是个人适配版本，不是 Google、OpenAI 或上游项目的官方发行版。

## 依赖与安装

- 已安装的 Node.js（建议使用仍受支持的 LTS 版本），封装本身仅使用 Node.js 标准库，无需 `npm install`。
- 兼容的 Antigravity CLI `agy`，由使用者自行安装、登录并确认账户与服务使用权限；本仓库不提供该 CLI、账号或凭据。
- `agy` 位于 `PATH`，或通过 `AGY_BIN` 指定其已有可执行文件路径。
- Git 用于工作区状态检查；无 Git 仓库时以当前工作目录保存任务状态。
- 抖音下载工具与 VidBee 是可选外部工具，不随本仓库发布，见 [下载说明](references/downloads.md)。

POSIX shell 下可安装到 Codex 的默认 Skill 目录；如果目标目录已存在，先检查并保留已有修改，不覆盖：

```sh
git clone https://github.com/linzifeng200422-ui/agy.git "${CODEX_HOME:-$HOME/.codex}/skills/agy"
```

其他支持 Skill 的宿主可将仓库放到其 Skill 目录，并按实际安装位置调用 `companion/agy-companion.mjs`。Windows 用户使用自己环境中的绝对路径与环境变量语法；上面的 shell 示例不直接适用于 PowerShell。

## 使用

在目标项目目录启动。通过宿主调用 `$agy`，或直接运行：

```sh
node "${CODEX_HOME:-$HOME/.codex}/skills/agy/companion/agy-companion.mjs" ask --prompt "解释这段材料，不调用工具：……"
node "${CODEX_HOME:-$HOME/.codex}/skills/agy/companion/agy-companion.mjs" research --prompt-file "/绝对路径/work/任务.txt"
node "${CODEX_HOME:-$HOME/.codex}/skills/agy/companion/agy-companion.mjs" wait JOB_ID --timeout 50s
```

`ask` 同步返回；其他四种角色启动后台任务，必须保存返回的任务 ID，并在相同工作目录继续收集结果。等待返回退出码 `2` 表示任务仍在运行，不要重复提交。详细说明见 [SKILL.md](SKILL.md)、[CLI](references/cli.md) 和 [任务管理](references/jobs.md)。

默认模型固定为 `gemini-3.8-flash-high`；实际可用模型受所安装 CLI 和账号影响，必要时显式传入 `--model`，不会自动降级。安装本 Skill 不自动安装或升级任何依赖。

## 权限与数据边界

`staffer`、`research`、`review`、`implement` 默认向底层 CLI 传入 `--dangerously-skip-permissions`；需要保留 CLI 权限检查时显式使用 `--restricted`。`ask` 使用 restricted，并由提示词要求不调用工具，但这不是操作系统沙箱。

工具可用不等于获得发布、付款、上传私人材料、扩大凭据使用或修改系统权限的授权。调用者应在任务中明确范围、允许动作、责任文件和验证标准，并自行核验结果；退出码或模型的成功声明不能替代实际验收。

任务说明、结果、日志与会话元数据保存在目标工作区的 `.agy-staff/` 中，可能含敏感内容，不应提交公开仓库。仓库提供相应忽略规则，但使用者仍应在提交前检查实际暂存内容。不要向复审模型提供密钥或无关私人材料。

## 许可证

采用 MIT 许可证，保留上游版权与许可原文：[LICENSE](LICENSE)。[LICENSE.agy-staff](LICENSE.agy-staff) 保留同一份上游许可。外部 AGY CLI、下载器及其服务各自的许可、条款与授权不由本仓库替代。
