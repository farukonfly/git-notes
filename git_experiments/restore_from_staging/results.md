# Git 试验：使用暂存区覆盖工作区修改 (Restore Working Directory from Staging Area)

## 试验目标 (Goal)
学习当文件被放置到 Staging Area（暂存区）后，如何在发生错误修改时，从 Staging Area 中提取该快照覆盖 Working Directory（工作区）中的未提交更改。这可以让我们撤销那些不想提交的后续修改。

## 执行步骤与观察 (Execution Steps & Observations)

### 1. 初始状态设置 (Initial State Setup)
```bash
git init
echo "version 1 (init)" > test.txt
git add test.txt
git commit -m "Initial commit"
```
*创建包含单个文件的基础试验仓库。*

### 2. 模拟满意的状态并暂存 (Stage a Good State)
```bash
echo "version 2 (good state)" > test.txt
git add test.txt
```
*此时，包含 "version 2" 字符串的好快照已经保存在 Staging Area。*

### 3. 继续工作，但改乱了文件 (Make Messy Unstaged Changes)
```bash
echo "version 3 (messy changes)" > test.txt
git status
```
*观察到的输出：* `test.txt` 同时出现在 `Changes to be committed` (暂存区的版本 "version 2") 和 `Changes not staged for commit` (工作区错误的版本 "version 3")。这证明它们确实维持在分离的不同状态。

### 4. 从暂存区恢复文件 (Restore from Staging Area)
```bash
git restore test.txt
cat test.txt
git status
```
*执行完毕：由于执行了 git restore，本地工作区被改乱的内容被安全丢弃了，重新恢复到了已暂存且包含 `version 2` 字符串的无误快照状态。并且该文件又消失在了 `Changes not staged for commit` 列表中。*

## 核心要点总结 (Key Takeaways)
- **`git restore <file>` 的默认行为：** 它会放弃 Working Directory (工作区) 里未执行 `git add` 的更改，并**使用上级数据源（在此场景中是 Staging Area）中的快照直接覆盖当前本地的文件。**
- 它仅仅丢弃了你在 `git add` 之后所做的手滑修改，由于之前暂存的 `version 2` 快照还在，你可以放心继续执行 `git commit`。
