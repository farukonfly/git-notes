# Git 试验：覆盖已暂存的更改 (Overwriting Staged Changes)

## 试验目标 (Goal)
了解当一个文件被修改并添加到 Staging Area (暂存区) 后，在 commit 之前又在 Working Directory (工作区) 被修改时会发生什么。目标是学习如何确保最终 commit 的是 Working Directory 中最新版本的文件。

## 执行步骤与观察 (Execution Steps & Observations)

### 1. 初始状态设置 (Initial State Setup)
```bash
git init
echo "version 1" > test.txt
git add test.txt
git commit -m "Initial commit"
```
*创建包含单个文件的基础仓库。*

### 2. 暂存第一次修改 (Stage First Modification)
```bash
echo "version 2 (staged)" > test.txt
git add test.txt
git status
```
*观察到的输出：* 文件出现在 `Changes to be committed` 下。这意味着 "version 2" 的快照现在已保存在 Staging Area (暂存区) 中。

### 3. 再次修改文件（不暂存） (Modify File Again Without Staging)
```bash
echo "version 3 (latest workspace content)" > test.txt
git status
```
*观察到的输出：* `test.txt` 同时出现在 **两个地方**：`Changes to be committed` 和 `Changes not staged for commit`。
*原因：* Git 分别记录同一文件路径在 Staging Area (保存了 "version 2") 和 Working Directory (保存了 "version 3") 中的不同状态。

### 4. 用最新的工作区快照覆盖暂存区 (Overwrite Staging Area with Latest Workspace Snapshot)
```bash
git add test.txt
git status
git commit -m "Commit the final version 3"
```
*(执行完毕：旧的暂存快照被最新工作区快照覆盖，并成功 commit)*

## 核心要点总结 (Key Takeaways)
- **Staging Area 保存的是快照 (snapshots)，而不是文件的链接。** 当你运行 `git add` 时，Git 保存的是该文件在那一确切时刻的精确内容。
- **一个文件可以同时具有两种不同的修改状态：** 一个是为下一次 commit 准备好的快照 (已被 staged)，另一个是本地文件夹中较新的未提交更改 (unstaged)。
- **如果要 commit 本地最新的更改**，你必须再次运行 `git add`。这会告诉 Git 丢弃 Staging Area 中旧的快照，并用 Working Directory 中最新的快照进行替换。
