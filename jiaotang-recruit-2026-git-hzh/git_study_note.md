# 主题：Git 学习

## 创建版本库

1. 创建新文件夹，在文件夹空白处右键点击Open git bash here
2. 通过`git init`命令把这个目录变成Git可以管理的仓库:`$ git init`

## 把文件添加到版本库

1. 用命令`git add`告诉Git，把文件添加到仓库：  

   `$ git add readme.txt`

   `git add -A` # 可一次性把所有文件提交到缓存区

   **注意**：要先ctrl+s保存后才能提交！

2. 用命令`git commit`告诉Git，把文件提交到仓库：  

   `$ git commit -m "wrote a readme file"`

## 时光机穿梭

### 基础查看

1. 查看工作区状态（能看到文件是否被修改）：`git status`  

   `$ git status`

2. 查看文件具体修改了什么内容：`git diff`  

   `$ git diff readme.txt`

### 版本回退

1. 用`git log`查看历史记录  

   `$ git log`  

   如果嫌输出信息太多，看得眼花缭乱的，可以试试加上`--pretty=oneline`参数：  

   `$ git log --pretty=oneline`

2. 版本回退，用`git reset`.  

   在Git中，用HEAD表示当前版本，上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，然往上100个版本写成`HEAD~100`  

   `$ git reset --hard HEAD^`

   `--hard`会回退到上个版本的已提交状态，而`--soft`会回退到上个版本的未提交状态，`--mixed`会回退到上个版本已添加但未提交的状态  
   退回后，新版本消失[^消失]  

3. 用版本号指定回到新版本[^回到新版本]

   `$ git reset --hard 1094a`  
  
4. 查看文件内容`cat`  

   `$ cat readme.txt`

5. 找不到版本的commit id,用git reflog用来记录你的每一次命令：

   `$ git reflog`

- **总结**：
  - HEAD指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令`git reset --hard commit_id`。
  - 穿梭前，用`git log`可以查看提交历史，以便确定要回退到哪个版本。
  - 要重返未来，用`git reflog`查看命令历史，以便确定要回到未来的哪个版本。



[^消失]: 好比你从21世纪坐时光穿梭机来到了19世纪，想再回去已经回不去了  
[^回到新版本]: 指定回到21世纪

### 工作区和缓存区

### 管理修改

每次修改，如果不用git add到暂存区，那就不会加入到commit中

### 撤销修改

- 场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git restore <file>`。

- 场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命`git restore --staged <file>`，就回到了场景1，第二步按场景1操作。

- 场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程。

### 删除文件

- 1. 删除文件并把删除放入缓存区：`git rm 文件名`

     `git rm test.txt`

  2. 把删除结果提交： `git commit -m "remove test file"`

  - **注意**：提交删除 ！= 从历史记录中消失  ，可以从历史记录中恢复该文件

  - 想撤销删除：同 “插销修改”


## 远程仓库  

### 添加远程库

1. 要关联一个远程库，使用命令`git remote add origin https://github.com/zh5737752/learn_git.git`（http协议） 或 `$ git remote add origin git@github.com:zh5737752/learngit.git `  (ssh协议)


2. 关联一个远程库时必须给远程库指定一个名字，origin是默认习惯命名；

3. 关联后，使用命令`git push -u origin main`第一次推送main分支的所有内容；

4. 此后，每次本地提交后，就可以使用命令`git push origin main`推送最新修改；  
   若分支与远程已经绑定，可用`git push`

- 删除远程库  
  如果添加的时候地址写错了，或者就是想删除远程库，可以用`git remote rm <name>`命令。使用前，建议先用`git remote -v`查看远程库信息  

### 从远程库克隆

1. 法1：在本地输入 `$ git clone https://github.com/zh5737752/gitskills` (后面那一串网址是仓库上方的网址)   （使用https协议下载）  
   法2：在本地输入：`$ git clone git@github.com:zh5737752/gitskills.git`（账户名/文件名）   （使用ssh协议，免密、安全、不被打断）

### 为git配置代理

- 查看：`git config --global --get http.proxy`
- 配置代理：`git config --global http.proxy http://127.0.0.1:7890`（假设代理端口是 7890，要替换为实际端口）（--global 表示全局配置，执行一次后对当前用户的所有仓库生效，之后无需重复输入）
- 更改代理端口 先清除记录 `git config --global --unset http.proxy`  再配置代理`git config --global http.proxy http://127.0.0.1:7890`


---

## 分支管理

### 创建与合并分支

Git鼓励使用分支完成某个任务，合并后再删除分支，这样既安全又不影响他人工作。

- 查看所有分支：`git branch`
- 创建分支：`git branch <name>`
- 切换分支：`git checkout <name>`
- 创建并切换分支：`git checkout -b <name>`（等价于 `git branch dev` + `git checkout dev`）

```bash
# 创建并切换到dev分支
$ git checkout -b dev

# 在dev分支上正常提交
$ git add readme.txt
$ git commit -m "branch test"

# 切回master分支，合并dev
$ git checkout master
$ git merge dev

# 合并完成后，删除dev分支
$ git branch -d dev
```

**原理**：`HEAD`指向当前分支，分支指针（如`master`、`dev`）指向提交。创建分支就是新建一个指针指向当前提交，切换分支就是让`HEAD`指向该指针。

### 解决冲突

当两个分支对同一文件的同一位置做了不同修改时，`git merge`会报冲突。

```bash
$ git merge feature1
Auto-merging readme.txt
CONFLICT (content): Merge conflict in readme.txt
Automatic merge failed; fix conflicts and then commit the result.
```

冲突文件会被Git用标记标出：

```
Creating a new branch is quick AND simple.
```

手动编辑文件，保留正确内容后，重新提交即可：

```bash
$ git add readme.txt
$ git commit -m "conflict fixed"
```

用 `git log --graph --pretty=oneline --abbrev-commit` 可以查看分支合并图。

### 分支管理策略

通常合并分支时，Git采用 **Fast forward** 模式，删除分支后会丢掉分支信息。如果强制禁用Fast forward，Git会在merge时生成一个新的commit，从分支历史上就能看出合并痕迹。

```bash
$ git merge --no-ff -m "merge with no-ff" dev
```

- `--no-ff` 表示禁用Fast forward，`-m` 写合并commit信息。
- 实际开发中，`master`分支应保持非常稳定，仅用于发布新版本；日常开发在`dev`分支上进行。

### Bug分支（git stash）

修复bug时，当前工作尚未完成，不想提交又想切换分支，可以用`git stash`把工作现场“储藏”起来。

```bash
# 储藏当前工作现场
$ git stash

# 从master创建临时分支修复bug
$ git checkout master
$ git checkout -b issue-101
# ...修复并提交...
$ git checkout master
$ git merge --no-ff -m "merged bug fix 101" issue-101
$ git branch -d issue-101

# 回到dev，恢复工作现场
$ git checkout dev
$ git stash list          # 查看储藏列表
$ git stash pop           # 恢复并删除stash
# 或
$ git stash apply         # 恢复但保留stash
$ git stash drop          # 手动删除stash
```

### Feature分支

每添加一个新功能，最好新建一个`feature`分支，完成后合并再删除。如果要丢弃一个**没有被合并过**的分支，需要用大写的 `-D` 强行删除。

```bash
# 创建并开发feature分支
$ git switch -c feature-vulcan
# ...开发并提交...

# 如果功能取消，强行删除未合并的分支
$ git branch -D feature-vulcan
```


## 标签管理

发布版本时，通常在版本库中打一个标签（tag），唯一确定打标签时刻的版本，标签本质是指向某个commit的指针，与分支类似但不能移动。

### 创建标签

```bash
# 切换到需要打标签的分支
$ git checkout master

# 在当前最新提交上打标签
$ git tag v1.0

# 查看所有标签
$ git tag

# 在指定commit id上打标签
$ git tag v0.9 faaaaa6

# 创建带说明的标签（-a指定标签名，-m指定说明）
$ git tag -a v0.8 -m "version 0.8 released" 7c4d427

# 查看标签详情
$ git show v0.8
```

### 操作标签

```bash
# 删除本地标签
$ git tag -d v0.9

# 推送单个标签到远程
$ git push origin v1.0

# 一次性推送全部未推送的标签
$ git push origin --tags

# 删除远程标签（先删本地，再push）
$ git tag -d v0.9
$ git push origin :refs/tags/v0.9
```


## 忽略特殊文件

有些文件必须放在工作目录但不需要提交（如数据库密码配置文件、编译产物等）。在仓库根目录创建 `.gitignore` 文件，把要忽略的文件名填进去即可。

```
# Windows
Thumbs.db
Desktop.ini

# Python
*.py[cod]
*.so
dist/
build/

# 自定义配置
db.ini
deploy_key_rsa
```

- `.gitignore` 本身应该提交到Git，确保所有人使用相同规则。
- 如果某个被忽略的文件确实需要添加，用 `-f` 强制添加：`git add -f App.class`。
- 检查哪条规则忽略了文件：`git check-ignore -v App.class`。
- 添加例外规则（不排除某文件）：`!.gitignore`、`!App.class`。


## 配置别名

通过 `alias` 简化常用命令，`--global` 参数表示对当前用户全局生效。

```bash
$ git config --global alias.st status
$ git config --global alias.co checkout
$ git config --global alias.ci commit
$ git config --global alias.br branch
$ git config --global alias.unstage 'reset HEAD'
$ git config --global alias.last 'log -1'
```

配置后 `git st` 即代表 `git status`，`git co` 即代表 `git checkout`。

配置文件位置：
- 当前仓库：`.git/config`（别名在 `[alias]` 段中，删掉对应行即可删除别名）。
- 当前用户： `C:\Users\用户名`