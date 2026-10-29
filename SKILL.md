---
name: agy
description: 调用本机 AGY（Antigravity CLI，简称 A）完成通用执行、调研、复审、实现和问答，管理后台任务；轻量执行及计划或执行逻辑已确定的任务默认委派 AGY；保留抖音下载、TK/VidBee、批量处理、文案协作和重要成果复审。用户说“让 A 做／交给 A／用 A 写”时适用；A-roll、选项 A 和变量 A 不触发。
---

# AGY：角色与后台任务

A = agy = 本机 Antigravity CLI。保留 `$agy` 统一入口，由任务选择角色；角色不是项目，也没有固定执行顺序。Codex 明确范围、委派、收取结果并核验交付。

## 选择角色

| 角色 | 执行命令 | 用途 | 执行方式 |
| --- | --- | --- | --- |
| staffer | staffer | 通用执行、下载、批量整理、文案起草/改写 | 后台 |
| researcher | research | 资料与代码调研、比较方案、引用证据 | 后台 |
| reviewer | review | 代码、文案、计划和决策复审 | 后台 |
| implementer | implement | 范围明确的代码修改与功能实现 | 后台 |
| ask | ask | 无需工具的简短问答、仅分析已附材料 | 同步 |

所有角色默认 `gemini-3.8-flash-high`。只有用户明确指定其他模型/档位时才覆盖；失败不自动降级。前四种角色默认 unrestricted（单次传入 `--dangerously-skip-permissions`）；ask 保持 restricted，并以提示词要求不使用工具，这不是操作系统沙箱。角色定义与调用规则见 [roles.md](references/roles.md)。

## 使用入口

首次启动任务读取 [cli.md](references/cli.md)。执行程序位于本 Skill 的 `companion/agy-companion.mjs`，依赖现有 Node.js 和 AGY CLI，无需安装插件或配置全局 allowlist。

后台任务返回 ID 后读取 [jobs.md](references/jobs.md)，跟进同一任务到结果。可以推进与其独立的工作；避免与 AGY 同时修改同一文件。后台运行不保证自动唤醒 Codex，宿主必须保持等待和结果收集。

## 默认分工

轻量执行任务，以及计划或执行逻辑已经确定的任务，默认交给 AGY：通用操作用 staffer，明确的代码实现用 implementer；无须用户逐次点名。Codex 负责目标、未解决的设计判断、任务范围和最终验收；普通原生子智能体保留 Sol/medium，复杂攻坚用 Astra/low 起步。取消 luna_worker 的轻量默认分工。AGY 缺少所需工具或不可用时，报告实际限制，再由 Codex 在原授权范围内接手，不声称 AGY 已执行。

用户明确指定其他工具或执行者时遵从其选择。无需实质执行的纯交谈、进度答复和需求澄清由 Codex 直接处理。委派范围与授权沿用当前任务，不因为步骤明确就扩大外部影响。

## 你的业务规则

- 抖音视频交给 staffer 使用本机抖音工具；TK 交给 staffer 使用 VidBee，先读 [downloads.md](references/downloads.md)。将平台工具要求、输出目录及适用约束写入任务，不只给 AGY 一个空泛的“下载”。
- 大量简单重复工作交给 staffer：给清单、统一规则、责任文件和验收方式，按真实规模分批，核对遗漏、重复及失败项。单个轻量执行任务也按上述默认分工交给 AGY。
- 实质性文案起草/改写交给 staffer。传入主题、受众、事实来源、已有稿件、语气、长度及必留内容；Codex 核验事实与口语表达。文本处理也按上述轻量任务分工；仅需所附材料且不用工具时可用 ask，需读写文件时用 staffer。
- 重要成果自动复审仍按 [review.md](references/review.md) 执行；执行过程只审最终成果，用户仅要计划时审该计划。纯交谈、澄清、进度、简单事实和低影响修改不触发。
- 用户指定角色时沿用；仅讨论或审计本 Skill 不触发业务任务。

## 共用约定

复用当前任务已有效的授权，不为角色选择、常规执行或自动复审重复确认。unrestricted 是工具权限配置，不扩大任务范围；对外发送、发布、支付、私人材料上传、凭据用途等仍依照用户授权。默认不运行 setup，也不修改 AGY 全局权限。

提示词交代输入、成果、工作目录、责任文件、验收及禁止事项；要求保护现有改动、禁止递归调用 AGY/子代理、禁止无关后台任务、不输出凭据。私人材料仅转交已授权用途所需的最少部分。

最终核验实际文件、工具结果和正文；任务 ID、exit 0、done 或 SUCCESS 均不能单独证明目标完成。超时、取消和恢复规则集中在 jobs.md。不能静默换服务后声称 AGY 已完成。

执行层来源及本地改动记录在 [provenance.json](provenance.json)，上游 MIT 许可保留于 LICENSE.agy-staff。更新时固定上游版本并重做本地适配与行为验证，不自动追踪或升级。
