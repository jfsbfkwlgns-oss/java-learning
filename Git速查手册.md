# Git 速查手册

> 创建于 2026-10-05，第一次学 Git 的当天。
> 这份文件本身就是一次真实的 Git 提交练习 —— 建议把它放进你的 `java-learning` 仓库。

## 修订记录

| 版本 | 日期 | 修改人 | 说明 |
|---|---|---|---|
| v1.0 | 2026-10-05 | 罗贤华 | 首次创建。覆盖核心模型、日常五连、撤销操作、当天踩过的坑 |

---

## 一、核心模型：四个区域

```
工作区 ──git add──> 暂存区 ──git commit──> 本地仓库 ──git push──> 远程仓库
（你改的文件）      （待提交清单）        （你的历史）          （GitHub）
        ^                                                              │
        └──────────────────── git clone ───────────────────────────────┘
```

**一句话理解**：`add` 是"把东西放进购物车"，`commit` 是"结账打包"，`push` 是"寄回家"。

**三条铁律**：

1. 每次动手前先敲 `git status`，它会告诉你当前处在哪个区域。
2. `commit` 是**存快照**，不是"保存文件"—— 每次 commit 都是一个可以随时回到的时间点。
3. `push` 是**同步差异**，不是"上传文件"。本地和远程是**两份独立的仓库**。

---

## 二、日常五连（每天用这五条就够）

| 顺序 | 命令 | 作用 | 什么时候用 |
|---|---|---|---|
| 1 | `git status` | 看当前状态 | 开始干活前、提交前 |
| 2 | `git add .` | 所有改动放进暂存区 | 改完文件后 |
| 3 | `git commit -m "说明"` | 存成一个快照 | 一个完整改动做完后 |
| 4 | `git log --oneline` | 看提交历史 | 想确认提交成功时 |
| 5 | `git push` | 同步到 GitHub | 一天结束时 |

**判读 `git status` 的三种输出**：

| 你看到 | 含义 | 下一步 |
|---|---|---|
| `nothing to commit, working tree clean` | 干净，没有未提交的改动 | 不用做什么 |
| `Changes not staged for commit`（**红色**） | 改了但没进暂存区 | `git add` |
| `Changes to be committed`（**绿色**） | 已进暂存区，等着提交 | `git commit` |

---

## 三、撤销操作：保命三招

**按"东西走到哪一步"来选，走错会丢代码：**

| 情况 | 命令 | 会不会丢东西 |
|---|---|---|
| 改了文件，还没 `add` | `git restore <文件>` | ⚠️ **会丢**，工作区改动直接没了 |
| 已经 `add`，还没 `commit` | `git restore --staged <文件>` | ✅ 安全，只退回暂存区，改动还在 |
| 已经 `commit`，还没 `push` | `git reset --soft HEAD~1` | ✅ 安全，撤销提交但改动留在暂存区 |
| 已经 `push` 出去了 | ❌ **别自己动** | 改历史会影响别人，先问 |

`HEAD~1` 读作"往回退 1 个提交"。

---

## 四、常用命令速查

### 查看类

| 命令 | 作用 |
|---|---|
| `git status` | 当前状态（最常用，没有之一） |
| `git log --oneline` | 简洁的提交历史 |
| `git log --pretty=format:"%h \| %an <%ae> \| %s"` | 带署名和作者的历史 |
| `git diff` | 看**还没 add** 的改动内容 |
| `git diff --staged` | 看**已经 add** 的改动内容 |
| `git show <hash>` | 看某一次提交具体改了什么 |

### 提交类

| 命令 | 作用 |
|---|---|
| `git add <文件>` / `git add .` | 加入暂存区（`.` 表示当前目录全部） |
| `git commit -m "说明"` | 提交 |
| `git commit --amend --no-edit` | 修改**最新一条**提交（不新建） |

### 远程类

| 命令 | 作用 |
|---|---|
| `git remote -v` | 查看远程仓库地址 |
| `git remote set-url origin <新地址>` | 修改远程地址（不是 `add`，会报 already exists） |
| `git push` | 推送到远程 |
| `git push -u origin main` | 首次推送并建立跟踪关系（之后可直接 `git push`） |
| `git pull` | 从远程拉取更新（**远程上有别人改的内容时必须先拉**） |
| `git ls-remote --heads origin` | 只看远程有哪些分支（免认证，适合排查） |

### 配置类

| 命令 | 作用 |
|---|---|
| `git config --global --list` | 查看全局配置 |
| `git config --show-origin --get-all user.name` | **查找某个配置到底由哪一级提供**（排查"配了不生效"的神器） |
| `git config --local --unset user.email` | 删掉仓库级配置，回退到全局 |

**配置优先级**：本地（仓库）> 全局（用户）> 系统。

---

## 五、2026-10-05 当天踩过的五个坑

| 现象 | 根因 | 记住这一句 |
|---|---|---|
| 终端里敲 `git` 报"不是内部或外部命令" | 只有宿主程序内置的便携版 Git | 工具要装**自己独立的** |
| 改全局身份不生效，`--reset-author` 也没用 | 仓库级 `.git/config` 里旧值**优先级更高** | 排查用 `--show-origin` |
| 提交署名是 `replace-me@example.com` | 同上 | 署名邮箱决定 GitHub **贡献图归谁** |
| 追加中文后文件乱码 | PowerShell 5.1 的 `Add-Content` 默认用 **GBK** 编码 | 写文件必须加 `-Encoding UTF8` |
| `git push` 卡住 / 报 `Connection was reset` | Git **不读**系统代理；认证需要交互界面 | 配 `http.<url>.proxy`；push 必须在**自己终端**跑 |

---

## 六、本机环境备忘（这台电脑专用）

| 项目 | 值 |
|---|---|
| Git 位置 | `C:\Program Files\Git\cmd\git.exe` |
| Git 版本 | 2.55.0.windows.5 |
| 提交署名 | `Xianhua Luo <jfsbfkwlgns@gmail.com>` |
| 默认分支 | `main` |
| GitHub 地址 | `https://github.com/jfsbfkwlgns-oss/java-learning` |
| 代理配置 | `http.https://github.com.proxy = http://127.0.0.1:3067` |
| 认证模式 | `device`（不弹浏览器，终端给验证码，自己去 `github.com/login/device` 输入） |

**代理端口变了怎么办**（换代理软件时会发生）：

```powershell
git remote -v                                                      # 确认地址
git config --global http.https://github.com.proxy http://127.0.0.1:新端口
```

**想切回弹浏览器模式**：

```powershell
git config --global --unset credential.gitHubAuthModes
```

---

## 七、下一步学习顺序

| 阶段 | 学什么 | 为什么 |
|---|---|---|
| 现在 | 把五连练成肌肉记忆 | 基础不熟，后面全是坑 |
| 接下来 | `git diff` + `git restore` | 防手滑的安全网 |
| 然后 | 读懂 `.gitignore` | 决定哪些文件永远不进 Git |
| 再然后 | 分支：`git switch -c`、`git merge` | Git 真正强大的地方，团队协作前提 |
| 最后 | PR（Pull Request）流程 | 实习/工作第一周就要用 |

---

## 附录：一句话原理速记

| 概念 | 一句话 |
|---|---|
| 为什么要有暂存区 | 让你能**挑选**这次提交包含哪些改动，而不是一次全提交 |
| commit hash 是什么 | 每次提交的身份证号（如 `9c6b784`），全局唯一，改一个字节就变 |
| `main` 是什么 | 默认分支名。老仓库叫 `master`，两者只是名字不同 |
| 为什么改动历史要谨慎 | hash 会变，所有人基于旧 hash 的工作都会错乱。**没推过的随便改，推过的别碰** |
| `.git` 目录是什么 | 你的仓库本体。删了它就只剩普通文件，历史全没（除非有远程备份） |
