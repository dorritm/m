# Git使用说明

### 创建仓库

1.首先在git安装目录下找到并打开git-bash，设置基本信息：

```
git config --global user.name "You Name"
git config --global user.email yourmail@server.com
```

2.切换目录到你要提交的文件所在目录，输入git init初始化一个本地仓库。

```
git init
```

3.输入git add 要提交文件夹的名称 ，将文件添加到跟踪列表

```
git add [file1] [file2]
```

4.提交到暂存区后，那开发者怎么看到这个是否提交成功呢？

```
git status
```

显示git版本管理工具的控制范围git status,会对git管理范围内所有文件进行监听，监听这些文件处于什么状态

5.把缓存区的文件提交到本地主仓库内

```
git commit -m “备注信息（可以是任何）”
```

6.提交到主仓库后，在提交到gitHub服务器上

```
git push https://github.com/dorritm/m（网址） master
```

将本地主仓库的内部代码提交到GitHub远程服务器上

在提交的过程中会让输入GitHub的账号和密码，输入即可

### 更新内容

与安装基本相同

### 从git仓库中拷贝内容

repo:Git仓库    directory:本地目录

克隆仓库： 

```
git clone <repo>
```

克隆到指定的目录：

```
 git clone <repo> <directory> 
```

比如git clone git://github.com/dorritm/m.git mygrit 会在当前目录下创建一个叫mygrit的目录，存放拷贝内容。

### 配置git config

```
$ git config --global user.email "your email"
$ git config --global user.name "your name"
```

### 提交与修改

### 提交和修改

```
git add  [file]|[dir]|.        分别是添加文件到暂存区|添加指定目录到暂存区|添加当前目录下的所有文件到暂存区。
git status 						查看项目当前的状态
git log							提交日志查询 
git commit -m [message]			提交暂存到本地仓库
```

### 远程操作

```
git remove -v						显示所有远程仓库
git remote rm name  				删除远程仓库
git remote rename old_name new_name 修改仓库名
```

### Git工作区、暂存区和版本区

工作区：电脑能看到的目录

暂存区：英文叫stage或index。一般存放在.git目录下的index文件中，把暂存区也叫索引。

版本库：工作区的隐藏目录.git



