# Git使用说明

### 安装说明

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

### 更新说明

```
git remote add origin git@github.com:用户名/Repository项目名称.git
```

建立了此电脑和服务器上创建的项目的连接

```
git pull origin master --allow-unrelated-histories
```

取回服务器的master更新

```
git push origin master
```

将本地的git项目同步到服务器上