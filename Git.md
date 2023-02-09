# Git

开源的分布式版本控制系统。

## 资料来源

> [Git 教程 | 菜鸟教程 (runoob.com)](https://www.runoob.com/git/git-tutorial.html)

## 常用命令速查

> [git查看当前账号名以及修改,更换源_HqL丶1024的博客-CSDN博客_查看git账号](https://blog.csdn.net/hql1024/article/details/107762587#)

![](src\a361811740034054bd3a6d52e640ff1c.jpeg)

## 与SVN的区别

|              | Git                                                   | SVN  |
| ------------ | ----------------------------------------------------- | ---- |
| 分布式       | 是                                                    | 否   |
| 内容存储方式 | 元数据                                                | 文件 |
| 全局版本号   | 无                                                    | 有   |
| 完整性       | 使用SHA-1算法，遇到磁盘或网络故障时降低对版本库的破坏 |      |

## 工作流程

1. 克隆Git资源作为工作目录。
2. 在克隆的资源上添加或修改文件。
3. 如果其他人修改了，可以更新资源。
4. 在提交之前查看修改。
5. 提交修改。
6. 修改完成后，如果发现错误，可以撤回提交并修改后再次提交。

## 基本概念

### 工作区

​	电脑中能看见的目录

### 暂存区

​	英文叫stage或index,一般存放在***.git***目录下的index文件，所以暂存区有时也叫做索引。

### 版本库

​	工作区有一个隐藏目录 ***.git***，这个不算工作区，而是Git的版本库。

## 使用

### 创建仓库

```powershell
git init
```

​	执行完 ***git init*** 命令后，Git 仓库会生成一个 ***.git*** 目录，该目录包含了资源的所有源数据。

​	Git 会默认创建master分支。

### 将文件添加到暂存区

````powershell
git add *.java
````

​	以上命令将以*** .java*** 结尾的文件添加到版本控制，添加到版本控制的文件改动会被保存在暂存区。

### 将暂存区内容提交到本地仓库

```powershell
git commit -m '初始化版本'
```

​	以上命令将改动后的已经添加到版本控制的文件提交至仓库。

### 从现有仓库拷贝项目

```powershell
git clone <repo> <directory>
```

​	将项目拷贝至本地目录

* repo：Git 仓库。
* directory：本地目录，可省略。

### 配置Git

​	展示当前git信息。

```powershell
git config --list
```

​	编辑配置文件。

```powershell
git config <-e> <--global> <item> <value>
```

* -e：针对当前项目。
* --global：针对所有git仓库。

### 查看仓库状态

```powershell
git status
```

​	查看仓库当前的状态，显示有变更的文件。

### 比较文件的不同

```powershell
git diff
```

​	查看暂存区和工作区文件的差异

### 回退版本

```powershell
git reset
```

### 删除工作区文件

```powershell
git rm
```

### 移动或重命名工作区文件

```powershell
git mv
```

### 日志

#### 查看历史提交记录

```powershell
git log
```

#### 查看指定文件的历史修改记录

```powershell
git blame <file>
```

### 远程操作

#### 添加一个远程仓库

```powershell
git remote add [alias] [url]
```

#### 查看当前远程仓库

```powershell
git remote
```

#### 获取代码库

```powershell
git fetch
```

#### 获取资源并合并

```powershell
git pull
```

#### 上传资源并合并

```powershell
git push <-u> [alias] [branch]
```

* -u：可选参数

#### 删除远程仓库

```powershell
git remote rm [alias]
```

### 分支

​	一个分支代表一个独立的项目版本路线。使用分支可以从主版本路线分离，其上的版本更迭不会影响主项目。

​	Git 分支实际上是指向更改快照的指针。

#### 创建分支

```powershell
git branch <-b> <branchName>
```

* -b：可选参数，创建分支完成后会自动切换到这个分支

#### 切换分支

```powershell
git checkout <branchName>
```

​	切换分支后，Git 会用该分支的最后版本快照替换工作目录的内容。

#### 合并分支

```powershell
git merge
```

#### 列出分支

```powe
git branch
```

#### 删除分支

```powershell
git branch -d <branchName>
```

### 标签

​	当项目到达一个重要阶段，并希望永远记住这个特别的提交快照，可以使用 ***git tag*** 打上标签。 

```powershell
git tag <-a> 'context'
```

* -a：可选参数，标志这个标签何时创建，何人创建。

## GitHub作为远程仓库

### 配置验证信息

​	本地Git仓库和GitHub仓库之间的传输是通过SSH加密的，所以需要配置验证信息

> SSH - 安全外壳协议（Secure Shell）
>
> ​	较可靠，专为远程登录会话和其他网络服务提供安全性的协议。利用SSH协议可以有效防止远程管理过程中的信息泄露问题。
>
> ​	建立在应用层基础上的安全协议。
>
> ​	提供两种级别的安全验证：基于口令和基于密钥

1. 生成 SSH Key

```powershell
ssh-keygen -t rsa -C "1033879106@qq.com"
```

2. 将生成的密钥在GitHub中配置
3. 验证 SSH 是否可以连接成功

```powershell
ssh -T git@github.com
```

