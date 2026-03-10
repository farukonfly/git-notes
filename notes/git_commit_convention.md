# 📝 Git 规范化提交规范 (Angular Convention)

业界最广泛使用的 Git 团队协作提交规范是 **Angular 规范**。该规范不仅让提交历史（commit history）清晰易读，还能借助工具根据这些提交记录自动生成 Changelog（更新日志）。

## 🌟 提交格式结构

每条 Git 的提交信息（Commit Message）被要求包含三个部分：**Header（标题）**、**Body（正文）**、**Footer（页脚）**。

```text
<type>(<scope>): <subject>
// 空一行
<body>
// 空一行
<footer>
```

- **Header（必填）**：包括 `type`（类型）、`scope`（作用域）、`subject`（简短描述）。
- **Body（选填）**：对改动的详细描述。
- **Footer（选填）**：通常用于描述重大破坏性变更（Breaking Changes）或关联/关闭某个 Issue。

---

## ⚙️ 核心字段解析

### 1. 🏷️ `<type>` (必须项)
用于说明本次提交的目的类别，常见的 `type` 如下：
- `feat`: ✨ 新增功能 (Feature)
- `fix`: 🐛 修复 Bug
- `docs`: 📚 文档修改（仅仅修改了文档、注释比如 README.md）
- `style`: 💎 代码格式修改（不影响代码运行的变动，如空格、缩进、格式化等）
- `refactor`: ♻️ 代码重构（**既不是新增功能，也不是修改 bug 的代码变动**）
- `perf`: 🚀 性能优化（提高代码性能的改动）
- `test`: 🚨 测试补充（增加缺失的测试或修正已有测试）
- `chore`: 🔧 杂项、构建过程或辅助工具的变动（如更新依赖依赖包 `npm` 等）
- `revert`: ⏪️ 回滚/撤销之前的提交
- `build`: 📦 影响构建系统或外部依赖的更改

### 2. 🎯 `<scope>` (可选项)
用于说明本次 Commit 影响的范围。
- 比如可以是一个具体的模块：`auth`, `router`, `ui-components`, `database`。
- 如果修改影响了全全局，可以省略或是写 `global`（或者用 `*`）。

### 3. 💬 `<subject>` (必须项)
简短描述本次提交的目标内容。
- **动词开头**，第一人称现在时（如 `add`、`update` 而不是 `added`、`updates`）。
- **结尾不加句号（.）**。
- 字数控制（一般不超过 50 个字符）。

---

## 📖 Body(正文) 与 Footer(页脚)

### 1. 📝 Body (详细描述)
对 `subject` 的扩充。详细说明**为什么修改（Motivation）**以及**和以前行为有什么不同**。
- 也使用现在时态来进行描述。
- 中间需要与 Header 隔开一行。

### 2. ⚠️ Footer (尾注)
主要有两个使用场景：
1. **不兼容的变动 (Breaking Changes)**：如果改动会导致以前的代码报错/无法运行，必须在这里注明，以 `BREAKING CHANGE:` 开头，后面跟着具体的变动信息。
2. **关闭 Issue**：如果本次提交修复了某个 Issue，可以在 Footer 里关闭它。
   - 例如：`Closes #123`, `Fixes #456`

---

## 💡 命令行实践

如果你要在命令行中写出包含 Header 和 Body 的多行提交备注，可以多次使用 `-m` 参数：

```bash
# 例子：标准带详情的规范化提交
git commit -m "feat(auth): 新增企业微信扫码登录" -m "企业微信登录支持了内部 Oauth 认证，并处理了返回鉴权 Token 逻辑。"

# 例子：只写 Header 的最常见用法
git commit -m "docs: 重新整理并移动笔记目录结构"
git commit -m "fix(router): 修复非管理员越权访问后台页面的bug"
```

## 🛠️ 推荐辅助工具
如果你觉得每次手敲格式很麻烦，可以使用终端交互工具辅助：
* `Commitizen` (`cz-cli`): 在命令行通过选项问答（Q&A）的方式，自动帮你拼出标准格式的提交备注。
