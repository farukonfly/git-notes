# 🌟 Git Submodule (子模块) 的提交机制

在 Git 中使用子模块（Submodule）时，主仓库和子模块是**两个完全独立的 Git 仓库**，但主仓库需要一种方式来“记住”子模块当前到底处在什么状态。

## 🧩 为什么会出现把文件夹当成文件提交的情况？

当你在 VS Code 的变更列表中看到类似于这样的差异（Diff）：
```diff
diff --git a/submodules/git-notes b/submodules/git-notes
--- a/submodules/git-notes
+++ b/submodules/git-notes
@@ -1 +1 @@
-Subproject commit 929316b077ed43dfd0004170e03f8ac88b374a88
+Subproject commit 9c07e5ab822820578fa84a0a5549677ba035fb6e
```

**你会感到困惑：我明明没有修改一个叫 `submodules/git-notes` 的文本文件，为什么它出现在了主仓库的待提交列表里？**

### 🤔 核心原理解释：Git 的“指针”机制
- **这不是一个实体文件**：在主仓库（`chieperce-cmse-notes`）眼里，`submodules/git-notes` 并不是一个普通的文件夹。它是一个**特殊的指针（称为 Gitlink）**。
- **只记录 Commit ID**：主仓库根本不关心子模块里具体的 Markdown 或代码是怎么写的，主仓库**只关心并记录一条信息：** 子模块当前停留在哪一个 Commit（提交号）上。
- **发生了什么？** 
  - 之前，主仓库记住你的子模块版本是 `929316b...`。
  - 现在，你在子模块里写了新笔记并提交了（比如新加了 `git_fetch_vs_pull.md`）。子模块自己向前走了一步，诞生了一个新的 Commit 号 `9c07e5a...`。
  - 主仓库探测到了这种变化，于是它说：“**嘿！我需要更新我记在小本本上的那个子模块的版本号了！**”
  - 因此，你看到的这一行待提交代码，**其实是主仓库在更新它对子模块版本的‘书签’**。

### 🔍 进阶：这个“指针”记录在主仓库的哪里？
很多初学者会误以为这个 Commit ID 记录在 `.gitmodules` 文件里。其实不是！
- **`.gitmodules` 文件**：只记录子模块的**路径**（`path`）和**克隆地址**（`url`），它是一个人眼可读的普通文本文件。
- **真正的指针载体 (`Git Tree Object`)**：这个包含具体 `9c07e5a...` Commit ID 的指针实体，**并没有作为一个单独的、你可以直接用文本编辑器打开的文件存在于你的工作目录中**。它被当做一棵“目录树（Tree Object）”中的一个特殊条目（模式为 `160000`），深度嵌在了主仓库隐藏的 `.git` 文件夹内部的数据库里。
  - *(扩展提示：有些人可能会看到主目录 `.git/modules/submodules/git-notes/FETCH_HEAD` 或者 `HEAD` 里记录了这一串哈希值。但请注意，那个只属于**子模块本身自己**的 Git 缓存（存放它自己去远端 fetch 的记录），真正让**主仓库**“认识”这个版本的凭证，是刻在主仓库的 Tree Object 里的！我们可以通过命令 `git ls-tree HEAD submodules/git-notes` 来审查它。)*
- 所以当它发生变动时，Git 的 `diff` 会虚拟出一个名字叫 `submodules/git-notes` 的“文件”给你看改动，但这纯粹是 Git 为了向你展示差异而使用的一种**表现形式**。

## ✅ 标准提交流程

当子模块发生内容修改后，一套正确且完整的提交流程应该是 **“由内而外”** 的：

### 第一步：在子模块内部提交并推送到远端
必须先让子模块自己完成保存动作。
```bash
# 1. 进入子模块文件夹
cd submodules/git-notes

# 2. 将修改存入暂存区并提交
git add .
git commit -m "docs: 新增关于 fetch 和 remote 的 Git 笔记"

# 3. 将子模块的新版本推送到它自己的云端仓库
git push origin main
```

### 第二步：回到主仓库，提交指针号的更新
主仓库需要知道子模块已经升级了。
```bash
# 1. 退出子模块，退回到主项目根目录
cd ../..

# 2. 我们会看到变更列表里有 submodules/git-notes 的纯 commit ID 改变
git add submodules/git-notes

# 3. 在主仓库进行提交
git commit -m "chore: 更新子模块 git-notes 至最新版本"

# 4. 把主项目推送到云端
git push origin main
```

---
💡 **重要提示**：一定要确保**先 Push 子模块，再 Push 主仓库**。
如果只 Push 了主仓库（更新了指针），但忘记 Push 子模块的新文件。你的同事拉取主仓库代码后，会发现 Git 提示“找不到对应的子模块文件内容”，因为他拿到了新的指针号码，却在云端找不到配对的实际文件。
