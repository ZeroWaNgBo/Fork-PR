

# 开源协作流程（Fork-PR 标准规范）

![思维导图](https://github.com/ZeroWaNgBo/Fork-PR/blob/main/%E6%80%9D%E7%BB%B4%E5%AF%BC%E5%9B%BE.png?raw=true)

## 一、前期准备（首次参与项目）

### 1. Fork 原仓库

在 GitHub 网页端打开目标项目仓库（如 `原作者/awesome-project`），点击右上角 **Fork** 按钮，将仓库复刻到个人账号下（生成私人副本：`你的账号/awesome-project`）。

* **一次性**：Fork 只在点击的那一刻执行一次复制，之后原仓库的任何更新（比如其他开发者提交的代码），都**不会自动同步**到你的私人仓库；
* **独立性**：你的私人仓库和原仓库是两个完全独立的远程仓库，二者之间没有天然的 “自动同步” 关联。

### 2. Clone 私人副本到本地

打开终端，执行以下命令，将个人账号下的仓库克隆到本地：

```Bash
# 克隆私人副本（替换为个人账号）
git clone git@github.com:你的账号/awesome-project.git
# 进入项目目录
cd awesome-project
```

### 3. 配置远程仓库（关联原仓库）

添加原仓库为**上游仓库**（用于同步原仓库最新代码），仅需执行一次：

```Bash
# 添加原仓库为上游（替换为原作者仓库的Git地址）
git remote add upstream 原作者/awesome-project的Git地址

# 验证远程仓库配置（应显示 origin（私人副本）和 upstream（原仓库））
git remote -v
```

### 4. 创建本地功能分支

从本地 `main` 分支创建独立功能分支（**分支名需遵循规范**：`类型/功能描述`，如 `feature/新增红色按钮`）：

```Bash
# 确保本地main分支与原仓库同步（首次克隆可跳过）
git checkout main
git fetch upstream
git merge upstream/main

# 创建并切换到功能分支
git checkout -b feature/新增红色按钮
```

## 二、本地开发流程

### 1. 开发并提交代码

在功能分支中完成开发后，提交代码到本地仓库（**提交信息需遵循规范**）：

```Bash
# 暂存修改的文件
git add src/Button.tsx

# 提交代码
git commit -m "feat: 新增红色按钮组件，支持size属性（large/small）"
```

### 2. 推送功能分支到私人仓库

将本地功能分支推送到个人 GitHub 仓库（`origin` 为私人仓库的远程别名）：

```Bash
git push origin feature/新增红色按钮
```

执行后，个人 GitHub 仓库中会新增 `feature/新增红色按钮` 分支。

## 三、发起 Pull Request（PR）

### 1. 提交 PR

1. 打开个人账号下的仓库（`你的账号/awesome-project`），页面会提示「最近推送了新分支」，点击 **Compare & pull request**；

2. 配置 PR 目标：

    - **基础仓库**：`原作者/awesome-project` → `main`（原仓库的目标分支）；

    - **头仓库**：`你的账号/awesome-project` → `feature/新增红色按钮`（你的功能分支）；

3. 填写 PR 信息：

    - **标题**：与 commit 规范一致（如 `feat: 新增红色按钮组件`）；

    - **描述**：说明功能背景、实现逻辑、测试情况（如「解决#123需求，已验证不同size的显示效果」）；

4. 点击 **Create pull request** 完成提交。

### 2. 处理评审意见

若原仓库维护者提出修改建议：

1. 在本地功能分支中调整代码；

2. 重复「提交代码 → 推送功能分支」步骤，PR 会自动同步新提交。

### 3. 冲突处理（PR 提示冲突时）

**若 PR 显示「存在冲突」，需在本地同步原仓库代码并解决冲突：**

#### ## 推荐：使用 rebase 模式解决冲突（公司优先方案）

```bash
# 1. 切换到功能分支
git checkout feature/新增红色按钮

# 2. 拉取原仓库最新代码
git fetch upstream

# 3. 变基到原仓库main分支（逐提交解决冲突）
git rebase upstream/main

# 4. 若出现冲突：
# - 打开冲突文件，删除<<<<<<<、=======、>>>>>>>等标记，修改为最终代码
# - 解决后执行：
git add 冲突文件路径
git rebase --continue  # 继续完成变基（若有多个冲突需重复此步骤）

# 5. 推送解决冲突后的分支（rebase后需加-f强制推送，仅限个人功能分支）
git push -f origin feature/新增红色按钮
```

#### ## 备选：使用 merge 模式解决冲突

```bash
# 1. 切换到功能分支
git checkout feature/新增红色按钮

# 2. 拉取原仓库最新代码
git fetch upstream

# 3. 合并原仓库main分支到当前功能分支
git merge upstream/main

# 4. 手动解决冲突：
# - 打开冲突文件，删除<<<<<<<、=======、>>>>>>>等标记，修改为最终代码
git add 冲突文件路径

# 5. 提交冲突解决记录
git commit -m "fix: 解决与原仓库main分支的代码冲突"

# 6. 推送解决冲突后的分支
git push origin feature/新增红色按钮
```

## 四、PR/MR 合并后的后续操作

### 1. 同步原仓库代码

PR 被原仓库维护者合并后，更新本地 `main` 分支至原仓库最新版本：

```Bash
# 切换到本地main分支
git checkout main

# 同步原仓库最新代码 upstream--原仓库  origin--私人仓库
git fetch upstream  # 把原仓库（upstream）的所有最新代码拉取到本地的缓存区
git merge upstream/main # 把缓存区里原仓库的main分支代码，合并到你的本地main分支

# 推送更新后的main分支到私人仓库
git push origin main
```

### 2. 清理分支（可选）

```Bash
# 删除本地功能分支
git branch -d feature/新增红色按钮

# 删除私人仓库中的功能分支
git push origin --delete feature/新增红色按钮
```

## 五、冲突管理与模式选择

### 1.冲突产生本质与原因：

**同一文件的同一位置出现了互斥的修改**

* 开发功能的过程中，原仓库的`main`分支有了新的提交
* 多个开发者向原仓库提交 PR，且修改了同一文件
* 多次修改功能分支，且原仓库频繁更新

### 2. rebase变基模式 与 merge合并模式 对比

| 维度                   | rebase 模式                                                  | merge 模式                                                   |
| ---------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **操作方式**           | 1. 基于公共分支（如 latest）创建功能分支开发<br />2. 有冲突时通过`rebase`解决后再提 MR<br />3. 合并后无额外 merge 提交 | 1. 在 fork 仓库任意开发（可不用单独分支）<br />2. 直接提 MR 合并，自动生成 merge 提交<br />3. 冲突在合并时一次性解决 |
| **提交历史**           | 线性、干净（无多余 merge 提交）                              | 网状、杂乱（包含大量 merge 提交，如 Linux 内核的历史图）     |
| **Buildbot（BB）表现** | 仅触发功能分支的差异提交，部署正常                           | 会触发主分支所有差异提交，导致生产服务被重复部署（BB “炸了”） |
| **提交信息**           | 原始提交信息（如时间）会被修改，提交 ID 会改写               | 保留所有原始提交信息，不修改历史                             |
| **冲突处理**           | 需在`rebase`过程中主动解决冲突才能完成操作，冲突解决更前置   | 合并时一次性处理冲突，可能积累更多冲突点                     |

### 3. 公司 rebase 优先的原因

- **适配 Buildbot（BB）CI/CD 工具**：BB 对 rebase 模式的部署触发更友好，避免 merge 模式导致的重复部署问题；
  * Buildbot（BB）是负责**自动化触发部署、执行 CI/CD 流程**的工具
    - 当代码提交到 Git 仓库（如 GitLab）后，Buildbot 会监听仓库的提交事件，自动拉取最新代码，执行构建、测试、部署等操作；
      * **面对`merge`模式**：Buildbot 会误触发主分支（如 latest）上的**所有差异提交**，导致生产环境的服务被重复部署；
      * **面对`rebase`模式**：Buildbot 只会触发功能分支的**差异提交**，部署行为正常，符合预期
- **保证线性历史**：提交历史整洁易追溯，便于代码审计和问题定位；
- **冲突前置解决**：在 rebase 过程中提前解决冲突，减少合并时的风险。

## 六、优势

* **分支隔离**：本地 `main` 分支仅用于同步原仓库更新，所有开发必须在功能分支中进行；
* **PR/MR **：一个功能对应一个分支、一个 PR/MR，禁止混入无关修改；
  * 二者是同一功能的不同平台命名（GitHub 叫 PR，GitLab 叫 MR），核心作用是**代码评审与合并管控**。
* **命名规范**：分支名、提交信息需遵循团队统一规范，便于协作与追溯。