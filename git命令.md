> 常用git 命令

##### 1. 修改最后一次提交
```shell
# 可以修改最后一次提交message，不会新增commit记录
git commit --amend
```

##### 2. 合并多条提交

```shell
git reset --soft [commitID]
```

如果你想合并最近几条提交信息的话，那么就需要使用上面的命令来操作，指定要撤销的ccommitId,该命令会保留当前改动并撤销指定提交后的所有commit记录，如果不指定ID的话可以使用HEAD~`{num}` 来选择最近`{num}`条提交

```shell
git reset --soft HEAD~2 #合并最近两条提交，带 --soft 参数的区别在于把改动内容添加到暂存区 相当于执行了git add .
git commit -m 'feat: add new feat' # 撤销commit记录后，将改动重新add，然后commit
```

**还有一种办法**

```shell
git rebase -i HEAD~3 #修改最近3条提交记录
p,pick = 使用提交
r,reword = 使用提交，但修改提交说明
e,edit = 使用提交，退出后使用git commit --amend 修改
s,squash = 使用提交，合并前一个提交
f,fixup = 和squash相同，但丢弃提交说明日志
d,drop = 删除提交，丢弃提交记录
#最好是保留前面消息，后面的可以丢掉，不要丢前面留后面

git rebase --abort # 会放弃合并，回到rebase操作之前的状态，之前的提交不会丢弃
```

#### 同步到远程仓库

```shell
git push -f # 强制推送，慎用
#或
git push --force
```

##### 3. 查看历史

```shell
--oneline # 参数，一行记录，最简显示
git log # 本地记录
git log origin/master #远程分支记录
```

##### 4. 设置简写（别名）

```shell
git config --global alias.st status #添加status别名，只能一行一行设置
git config --global -e #打开git基本配置并编辑
[user]
        name = xieyuhao
        email = xieyuhao@fscut.com
[core]
        autocrlf = true
        quotepath = false
[credential]
        helper = store
[gui]
        encoding = utf-8
[i18n "commit"]
        encoding = utf-8
[i18n]
        logoutputencoding = utf-8
[alias]
        st = status
        pl = pull
        lg = "log --online"
# 也可以使用该命令直接在这里编辑配置，编辑多行设置
```

