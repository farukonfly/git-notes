# 🌟 Git Restore 用法指南

`git restore` 命令是在 Git 2.23 版本中引入的，主要用于分离原先 `git checkout` 承担的“恢复文件”功能，使其语义更加清晰。它可以用来撤销工作区（Working Directory）或暂存区（Staging Area）的文件修改。

## 📝 核心使用场景

### 1. 放弃工作区的修改 (撤销未暂存的更改)
如果你在本地修改了文件，但还没有执行 `git add`，你想放弃这些修改并让文件恢复到最后一次提交（或暂存）的状态。

```bash
# 恢复单个文件，撤销工作区的修改
git restore <file>

# 恢复当前目录及其子目录下的所有修改
git restore .
```
- **解释：** 默认情况下，`git restore` 将使用暂存区（Index）中的状态来覆盖工作区的文件。如果暂存区没有内容，则使用最后一次 commit（HEAD）的状态。

### 2. 将文件从暂存区移出 (取消暂存 / Unstage)
如果你已经执行了 `git add <file>`，但发现加错了，想把文件退回到工作区（保留修改，但不包含在下次提交中）。

> **💡 简单来说**：它相当于 **“撤销 git add”** 操作。

```bash
# 将指定文件从暂存区撤出，但保留你在文件里的修改
git restore --staged <file>

# 将所有暂存的文件撤出
git restore --staged .
```
- **参数说明：** `--staged` (简写 `-S`) 表明操作目标是暂存区，而不是默认的工作区。这相当于将暂存区的内容恢复成 HEAD 的状态。

### 3. 同时丢弃暂存区和工作区的修改
如果你既 `add` 了文件，又希望彻底丢弃这些修改，让文件恢复到最后一次 commit 的原始状态。

```bash
# --staged 加上 --worktree，或者分布执行
git restore --staged --worktree <file>
```
- **参数说明：** `--worktree` (简写 `-W`) 是默认行为。当与 `--staged` 一起使用时，会同时重置暂存区和工作区。

### 4. 从特定的 Commit 恢复文件
你想查看或恢复到历史某个提交版本中的某个文件状态。

```bash
# 从指定的 commit 中恢复文件到你的工作区
git restore --source=<commit_hash> <file>

# 从 HEAD 的前一次提交 (HEAD~1) 恢复文件
git restore --source=HEAD~1 <file>
```
- **参数说明：** `--source` (简写 `-s`) 指定恢复内容的来源（通常是一个 commit 哈希值或分支名）。如果不加这个参数，默认行为是从 `Index` (如果使用了 `--staged` 则是从 `HEAD`) 恢复。

## ⚖️ 关键辨析：带 `--staged` vs 不带参数

这是 `git restore` 最容易混淆的地方，请务必区分清楚！

| 命令 | 作用 | 结果 | 危险程度 |
| :--- | :--- | :--- | :--- |
| `git restore --staged <file>` | **撤销 git add** | 文件修改**保留**在工作区 | 🛡️ **安全** |
| `git restore <file>` | **撤销文件修改** | 文件修改被**丢弃**（变回上次暂存/提交的样子） | ⚠️ **危险** (数据丢失) |

> **🛡️ 为什么 `--staged` 是安全的？**
> 因为它**绝对不会修改你的文件内容**，只是改变文件的 Git 状态（从“已暂存”变为“未暂存”）。原本你写的东西都还在。

### 📚 记忆口诀
- `git restore --staged` = **撤销 add**（回到 add 之前）
- `git restore` = **撤销修改**（丢弃工作成果，慎用！）

## ⚙️ 总结对比表

| 目标 | 需要使用的命令 | 影响的区域 |
|---|---|---|
| 放弃当前修改（未 `add` 时） | `git restore <file>` | 仅工作区 |
| 取消暂存（已 `add` 时） | `git restore --staged <file>` | 仅暂存区 |
| 彻底抛弃包括已暂存和未暂存的修改 | `git restore --staged --worktree <file>` | 暂存区 + 工作区 |
| 从特定版本提取文件 | `git restore --source=<commit> <file>` | 工作区（默认） |
