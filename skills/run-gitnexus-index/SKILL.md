---
name: run-gitnexus-index
description: 自动执行 gitnexus analyze --index-only，并基于 Git commit 记录智能跳过重复构建。
---

# 技能执行逻辑

当用户调用本技能时，请严格遵循以下工作流：

1. **获取最新代码版本**：
   使用 `run_command` 执行命令 `git rev-parse HEAD`，获取当前项目的最新 commit hash。

2. **读取历史记录**：
   检查当前目录下是否存在 `.last_gitnexus_commit` 文件。如果有，读取其内容。

3. **版本比对与判定**：
   - 如果读取到的 hash 与第 1 步获取的当前 hash **完全一致**，则向用户输出：“当前版本与上次构建索引的版本一致，跳过执行。”，随后**结束本技能流程**，不要执行后续步骤。
   - 如果文件不存在，或者两者 hash 不一致，则进入第 4 步。

4. **执行构建与状态更新**：
   - 使用 `run_command` 工具在当前目录下执行命令：`gitnexus analyze --index-only --skip-git`
   - 等待命令执行完成后，使用写入工具将第 1 步获取的 commit hash 覆盖写入到当前目录的 `.last_gitnexus_commit` 文件中。

5. **任务汇报**：
   向用户简要汇报“索引构建已完成，并已更新本地版本记录”。
