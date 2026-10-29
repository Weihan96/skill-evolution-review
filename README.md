# Skill Evolution Review

帮助诊断技能为何失效，并把改进放到合适的位置：主指引、reference、输出结构、校验或脚本。与 `skill-creator` 配合使用，也可单独审核现有技能。

主指引：

[SKILL.md](SKILL.md)

## 开发与安装

默认开发仓库为 `/Users/jiaxinchen/Documents/Projects/Skills/skill-evolution-review`；用户可指定其他开发根。`~/.codex/skills/skill-evolution-review` 仅用于发布安装内容。修改、测试、提交、发布在真实挂载开发仓库的专用 task 中完成。

使用、审核或准备修改全局技能时，先检测 Git 标记和指向 checkout 的链接。发现后先识别来源、比较实际内容、保全完整仓库；两边内容不同须由用户选择。具体授权和 Git 基线保护规则以主指引为准。

本次只准备本地规则和验证，不创建远端、不推送、不替换全局安装。发布目的地与公开/私有尚未确认；本地提交不代表已经发布或安装。首次须完成本技能的发布安装闭环，再读取新安装规则处理 `git-graph-browser`。

## 本地验证

在开发仓库运行（示例验证器路径对应本机 skill-creator）：

```bash
python3 /Users/jiaxinchen/.codex/skills/.system/skill-creator/scripts/quick_validate.py .
git diff --check
git diff --cached --check
```

还需检查规则覆盖、引用是否可读，以及缺失开发根、两边内容不同、Git 保护基线、挂载不符、远端未确认和安装失败等情景。`quick_validate` 仅覆盖基础技能结构，不能证明发布安装安全。

## 发布验证

确认目的地、可见性与本次发布授权后，以确定的 commit/tag 发布包并提供校验依据；从发布源重新下载该明确版本，在临时目录检查 hash、内容、安全路径和技能有效性。完整保全旧仓库后才替换全局，安装后复核一致性、版本追溯和无 checkout；失败回滚。开发目录副本或本地未发布压缩包不能当作安装源。

发布证据和验证方法：

[release-verification.md](references/release-verification.md)
