# AGY 统一执行入口

执行程序位于本 Skill 的 `companion/agy-companion.mjs`。默认安装目录为 `${CODEX_HOME:-$HOME/.codex}/skills/agy`；安装在其他位置时使用其实际路径。
底层 AGY 默认从 `PATH` 查找 `agy`，也可用环境变量 `AGY_BIN` 指定已有可执行文件。使用已有 Node.js；不自动安装或升级。

在任务的绝对工作目录运行。以下示例适用于 POSIX shell；`AGY_BIN` 只是执行程序定位，不配置新凭据。

```sh
node "${CODEX_HOME:-$HOME/.codex}/skills/agy/companion/agy-companion.mjs" staffer --prompt-file "/绝对路径/work/任务.txt"
```

将 staffer 换为 research、review、implement 或 ask 即可。researcher/reviewer/implementer 是角色名，命令分别为 research/review/implement。

`--prompt`、`--prompt-file`、`--stdin` 三选一。长文本使用 UTF-8 文件或参数数组，禁止拼接用户文本为 shell 代码。任务中包含明确的输入、输出目录、责任文件、已授权动作和验收方式。程序向 AGY 传递 `--disable-slash-commands`，防止素材中的斜杠命令被展开。

## 模型、权限与时限

五个模式的默认模型都是 `gemini-3.8-flash-high`，通常省略模型参数。`--model` 是完整模型 ID；companion 的 `--effort high` 会选中同一个 high 后缀模型，不是额外一次推理档位设置。仅用户明确改选时才覆盖。

staffer/research/review/implement 默认 unrestricted；ask restricted 且要求无工具。前四者可显式传 `--restricted`，但可能有工具被非交互权限拒绝。遇到拒绝先核对原任务授权和实际配置；不得自动写全局 allowlist、绕过明确 deny，或擅自转图形界面。

工具模式默认后台运行，硬时限 60 分钟，`--timeout` 最长 120 分钟。ask 同步执行，默认 2 分钟，可按文本量设置正时限。继续已有会话继承原模式、模型和权限，显式参数优先。现有上游任务如果使用过其他模型，只有用户明确要恢复其旧模型时才沿用，否则明确传 `--model gemini-3.8-flash-high`。

## 工作目录与状态

状态存放在任务所在 Git worktree 根目录（无 Git 时为 cwd）的 `.agy-staff/`，沿用上游格式，以便管理和排障。程序会把该目录加入本地 `.git/info/exclude`，不修改被跟踪的 `.gitignore`。结果、日志、会话元数据保留在本地，可能含任务内容，不外传整份日志。

启动与 wait/result/cancel 必须使用同一个工作区和权限上下文。程序的 Git 状态对比只能辅助发现改动，不是文件隔离，也不能证明内容完全未变；严格只读复审另做候选文件哈希检查。

## 底层 CLI 兼容路径

只有后台执行层发生已确认故障、且相同授权下可用时才使用原始 CLI，并披露限制。原始 CLI 无 companion 的 jobs 管理；其会话 ID 不可冒充后台 job ID。

纯材料：`agy --disable-slash-commands --model gemini-3.8-flash-high --print-timeout 0 --output-format stream-json -p '完整材料和要求'`。
工具任务在同一命令加 `--dangerously-skip-permissions`，限已授权任务；不得当成只读开关。

登录、地区、配额、显式权限拒绝按真实原因报告。CLI 超时的空 SUCCESS 不算完成；已知 conversation 只用于有实际后续任务的续接，不能用再次发消息冒充读取状态。AGY 网络沿用当前授权配置，不通过更换出口规避服务地区拒绝。
