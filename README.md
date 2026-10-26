# Skills

用于软件开发的 Agent Skills，按 `skills/<skill-name>/SKILL.md` 组织。

## 可用技能

| 技能 | 用途 |
| --- | --- |
| [clean-code](skills/clean-code/SKILL.md) | 架构设计、实现方案、代码实现、代码审查、BUG 修复和渐进重构 |

## 安装

使用 Codex 内置 skill-installer 脚本（若自定义了 CODEX_HOME，请替换脚本所在路径）：

```sh
python3 ~/.codex/skills/.system/skill-installer/scripts/install-skill-from-github.py \
  --repo OnslaughtSnail/skills --path skills/clean-code
```

安装到 `~/.agents/skills`：

```sh
python3 ~/.codex/skills/.system/skill-installer/scripts/install-skill-from-github.py \
  --repo OnslaughtSnail/skills --path skills/clean-code --dest ~/.agents/skills
```

默认安装 main 分支；可用 `--ref <commit-or-tag>` 固定版本。安装器不会覆盖已存在的同名目录。
安装完成后可在下一轮使用 `$clean-code`，也可由支持自动选择的客户端按开发任务触发。

## 目录

```text
skills/
└── clean-code/
    ├── SKILL.md
    ├── agents/openai.yaml
    └── references/
```

`SKILL.md` 提供入口与工作流程，参考材料按需加载；`agents/openai.yaml` 提供 Codex 界面元数据。
