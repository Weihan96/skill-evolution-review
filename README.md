# Skill Evolution Review

When a skill repeats a mistake, identify the missing decision instead of adding another one-off rule. Review triggers, reasoning order and validation, and keep development checkouts separate from verified release installations.

技能反复犯同一种错时，找出缺失的判断，而不是不断叠加补丁；同时让开发仓库、发布包和全局安装各归其位。

[English](#english)

[中文](#中文)

## English

### Prerequisites

- [ ] **Codex with local skills support.** Install the Codex app from OpenAI's download page below, open a local project, and verify its bundled installer with `test -f ~/.codex/skills/.system/skill-installer/scripts/install-skill-from-github.py`. Substitute your configured `CODEX_HOME` below if different.
- [ ] **GitHub CLI and Python 3.** On macOS with Homebrew, run `brew install gh python`, then `gh auth login`; verify `gh auth status` and `python3 --version`. On Ubuntu/WSL, use `sudo apt install gh python3 python3-venv` and the same login command.
- [ ] **PyYAML for validation.** Run `python3 -m venv ~/.local/share/skill-validation-venv`, then `~/.local/share/skill-validation-venv/bin/python -m pip install PyYAML`. Verify with `~/.local/share/skill-validation-venv/bin/python -c 'import yaml; print(yaml.__version__)'`.
- [ ] **Optional: Bun for discovery.** On macOS, run `brew install oven-sh/bun/bun`; verify `bun --version`. Development also needs Git (`brew install git` or `sudo apt install git`; verify `git --version`). Reading the instructions needs neither tool.

[Codex download — OpenAI](https://chatgpt.com/codex)

[Homebrew installation](https://brew.sh)

### Install a pinned release

Commands target macOS/Linux/WSL shells. Native Windows users should run them in WSL and use that Codex instance's skills directory; do not mix Windows and WSL home paths. Authentication is needed for this `gh` workflow, not for the skill's reasoning.

1. Complete the prerequisites. Verify the validator with `test -f ~/.codex/skills/.system/skill-creator/scripts/quick_validate.py`; if missing, use the bundled skill-creator from your Codex installation before continuing.
2. Download into isolation, never straight into global skills:

   ```bash
   skill_release_dir=$(mktemp -d)
   gh release view v1.0.0 --repo Weihan96/skill-evolution-review
   gh release download v1.0.0 --repo Weihan96/skill-evolution-review --dir "$skill_release_dir" --pattern 'skill-evolution-review-v1.0.0.tar.gz' --pattern SHA256SUMS --pattern FILES.json
   (cd "$skill_release_dir" && shasum -a 256 -c SHA256SUMS)
   printf '%s\n' "$skill_release_dir"
   ```

   Every checksum must report `OK`; this checks integrity, while the next step checks provenance and safe extraction.
3. Give Codex the printed path and request: “Install release `v1.0.0` of `Weihan96/skill-evolution-review` from these downloaded assets. Before extraction, reject unsafe paths, duplicates, `.git`, links and special files. Verify the tag's full commit, file bytes/modes against that commit and `FILES.json`, and run quick_validate. Preserve any old installation outside skill discovery. Install only the verified downloaded release, check every installed file, record an external provenance receipt, and roll back on failure.” Check the reported commit, archive validation and backup path; detailed checks are linked at the end of this README.
4. Verify installation, then use the skill on your next Codex turn:

   ```bash
   test -f ~/.codex/skills/skill-evolution-review/SKILL.md
   ~/.local/share/skill-validation-venv/bin/python ~/.codex/skills/.system/skill-creator/scripts/quick_validate.py ~/.codex/skills/skill-evolution-review
   ```

   Ask Codex to actually re-read the installed `SKILL.md` and `references/release-verification.md`, report the receipt's version/commit/hash and inspect links for checkout ownership. quick_validate alone does not prove installation safety.

Optional discovery check (does not install): `bunx skills add Weihan96/skill-evolution-review --list`.

For independent source-download testing, invoke Codex's skill-installer script with `--repo Weihan96/skill-evolution-review --ref FULL_COMMIT --path . --name skill-evolution-review --method download --dest TEMP_DIRECTORY`. Replace the uppercase values with the verified full commit and a fresh temporary directory. This tests installer compatibility; the global installation must still come from the published release asset.

### Natural-language examples

- “This skill keeps patching the last example. Diagnose the missing decision and propose a reusable fix.”
- “Review why this skill triggers on unrelated requests. Show evidence before changing it.”
- “Prepare a release of this skill, compare global and development content, and protect my staged changes.”

### Risks and limits

This is an instruction skill, not an automatic migration tool. Edits, commits, publication and replacement require task authorization. Public releases expose included files and repository history. Preserve local changes and recovery copies; inspect packages before extraction. An audit does not authorize publishing or deleting. Official/system and managed skills retain their ownership boundaries. Codex usage follows your account plan; this repository charges no separate fee.

The default development root `/Users/jiaxinchen/Documents/Projects/Skills` is the author's macOS path. Specify your own root on another machine; missing roots require a decision, not automatic creation. Global skills directories hold installations, not development checkouts.

### Troubleshooting

| Symptom | Action |
|---|---|
| Missing `gh` or authentication failure | Install using the prerequisite command, run `gh auth login`, then `gh auth status`. |
| `ModuleNotFoundError: yaml` | Install PyYAML in the venv above and run the validator with that venv's Python. |
| Missing release, checksum mismatch or unsafe archive | Stop before replacement. Check `gh release view v1.0.0 --repo Weihan96/skill-evolution-review`, download into a new temporary directory and investigate; do not bypass checks. |
| Destination exists or global content differs | Keep it intact, compare actual content and preserve a complete backup. Do not delete it to bypass the installer. |
| Skill not visible | Start the next turn, check the active `CODEX_HOME`/skills path and read the installed file; do not link a checkout. |

### Source, credits and license

Maintained by Weihan96. Initial source is retained in `31e3d08`; the lifecycle workflow was added in `0343387`. No third-party implementation or vendored content is included. OpenAI's skill-creator and skill-installer, the publisher tool, GitHub CLI and Bun are external workflow tools, not bundled code. Distributed under MIT; see LICENSE. Source and tool credits are linked below.

## 中文

### 前置条件

- [ ] **支持本地技能的 Codex。** 从下方 OpenAI 下载页安装 Codex App，打开本地项目；用 `test -f ~/.codex/skills/.system/skill-installer/scripts/install-skill-from-github.py` 检查内置 installer。自定义 `CODEX_HOME` 时替换下文路径。
- [ ] **GitHub CLI 与 Python 3。** macOS 已安装 Homebrew 时运行 `brew install gh python`，再执行 `gh auth login`；用 `gh auth status` 和 `python3 --version` 验证。Ubuntu/WSL 使用 `sudo apt install gh python3 python3-venv`，登录命令相同。
- [ ] **验证用 PyYAML。** 执行 `python3 -m venv ~/.local/share/skill-validation-venv`，再执行 `~/.local/share/skill-validation-venv/bin/python -m pip install PyYAML`。用 `~/.local/share/skill-validation-venv/bin/python -c 'import yaml; print(yaml.__version__)'` 验证。
- [ ] **可选：发现验证需要 Bun。** macOS 执行 `brew install oven-sh/bun/bun`，用 `bun --version` 验证。开发另需 Git：`brew install git` 或 `sudo apt install git`，用 `git --version` 验证。阅读技能本身不需要这两项。

[Codex 下载 — OpenAI](https://chatgpt.com/codex)

[Homebrew 安装](https://brew.sh)

### 安装明确版本

以下命令适用于 macOS/Linux/WSL。Windows 用户应在 WSL 中运行并使用该环境中 Codex 的技能目录，不要混用系统的用户目录。GitHub 登录用于这里的 `gh` 下载流程，技能分析本身不依赖 GitHub 账号。

1. 完成前置条件。运行 `test -f ~/.codex/skills/.system/skill-creator/scripts/quick_validate.py` 检查验证器；缺失时先使用当前 Codex 安装提供的 skill-creator。
2. 在临时目录下载发布包，不直接写全局技能目录：

   ```bash
   skill_release_dir=$(mktemp -d)
   gh release view v1.0.0 --repo Weihan96/skill-evolution-review
   gh release download v1.0.0 --repo Weihan96/skill-evolution-review --dir "$skill_release_dir" --pattern 'skill-evolution-review-v1.0.0.tar.gz' --pattern SHA256SUMS --pattern FILES.json
   (cd "$skill_release_dir" && shasum -a 256 -c SHA256SUMS)
   printf '%s\n' "$skill_release_dir"
   ```

   每项必须显示 `OK`。这一步验证下载完整性，下一步继续检查来源 commit 和安全解压。
3. 将打印的路径交给 Codex：“从这些下载文件安装 `Weihan96/skill-evolution-review` 的 `v1.0.0`。解压前拒绝危险路径、重复路径、`.git`、链接和特殊文件；核对 tag 完整 commit、逐文件 bytes/modes 与该 commit 及 `FILES.json` 一致，运行 quick_validate。完整保全旧安装到技能发现目录外，只用下载验证后的发布包替换；复核每个安装文件并记录外部 receipt，失败回滚。”验收时检查其报告的 commit、包检查和备份位置。详细方法见文末 reference。
4. 验证安装，并在下一轮 Codex 对话中使用：

   ```bash
   test -f ~/.codex/skills/skill-evolution-review/SKILL.md
   ~/.local/share/skill-validation-venv/bin/python ~/.codex/skills/.system/skill-creator/scripts/quick_validate.py ~/.codex/skills/skill-evolution-review
   ```

   请 Codex 实际重读安装后的 `SKILL.md` 与 `references/release-verification.md`，报告 receipt 的版本/commit/hash 和链接、Git 归属检查。quick_validate 不能单独证明安装安全。

可选的发现验证（不安装）：`bunx skills add Weihan96/skill-evolution-review --list`。

独立源码下载测试使用内置 skill-installer 脚本，参数为 `--repo Weihan96/skill-evolution-review --ref FULL_COMMIT --path . --name skill-evolution-review --method download --dest TEMP_DIRECTORY`。将大写值替换为已验证完整 commit 和全新临时目录。它检查 installer 兼容性；全局安装仍取自正式发布资产。

### 自然语言例子

- “这个技能总在给上次的例子打补丁，找出缺失的判断，提出能复用的改法。”
- “审核它为什么在无关请求里触发，先给证据再修改。”
- “准备发布这个技能，比较全局和开发内容，并保护我的暂存改动。”

### 风险与限制

本技能提供操作指引，不是自动迁移程序。修改、提交、发布和替换须在任务授权内；公开发布会公开包含的文件与仓库历史。保留本地改动和恢复副本，先检查包再解压。审核不等于授权发布或删除；官方/system 与托管技能仍遵守原有权限边界。Codex 用量按账号方案计费，本仓库不另收费。

默认开发根 `/Users/jiaxinchen/Documents/Projects/Skills` 是作者的 macOS 路径。其他机器应指定自己的开发根；目录缺失须询问，不自动创建。全局技能目录只作安装目录，不放开发 checkout。

### 常见问题

| 问题 | 处理方法 |
|---|---|
| 找不到 `gh` 或登录失败 | 按前置命令安装，执行 `gh auth login`，再用 `gh auth status` 验证。 |
| `ModuleNotFoundError: yaml` | 在上述 venv 中安装 PyYAML，使用该 venv 的 Python 运行验证器。 |
| 发布不存在、hash 不符或包不安全 | 替换前停止；用 `gh release view v1.0.0 --repo Weihan96/skill-evolution-review` 确认版本，下载到新临时目录并调查，不绕过检查。 |
| 安装目录已存在或两边内容不同 | 保持原目录，比较实际文件并完整备份，不为绕过报错而删除旧版。 |
| Codex 尚未发现技能 | 在下一轮使用并核对实际 `CODEX_HOME`/skills 路径；读取安装文件，不建立 checkout 链接。 |

### 来源、致谢与许可

由 Weihan96 维护。初始内容保留于 `31e3d08`，生命周期流程加入于 `0343387`。仓库没有第三方实现或 vendored 内容。OpenAI 的 skill-creator、skill-installer，以及 publisher 工具、GitHub CLI、Bun 用于外部工作流，未打包进本技能。采用 MIT 许可，完整条款见 LICENSE。来源与工具致谢链接如下。

[Weihan96 / source repository / 源仓库](https://github.com/Weihan96/skill-evolution-review)

[OpenAI / skills](https://github.com/openai/skills)

[GitHub / CLI](https://github.com/cli/cli)

[Bun contributors / Bun 贡献者](https://github.com/oven-sh/bun)

## Maintainer verification / 维护验证

Run in the dedicated development task / 在真实挂载开发仓库的专用 task 中运行：

```bash
python3 ~/.codex/skills/.system/skill-creator/scripts/quick_validate.py .
git diff --check
git diff --cached --check
```

The one-time initial migration installs and re-reads this skill before any separately authorized `git-graph-browser` migration. This is not a permanent dependency or permission to migrate another skill.

本次首次迁移先完成本技能安装并重读，再处理另行授权的 git-graph-browser 迁移；该顺序不构成永久依赖，也不授权迁移其他技能。

[Main instructions / 主指引](SKILL.md)

[Release verification / 发布验证方法](references/release-verification.md)
