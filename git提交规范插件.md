> 现在接触的所有项目，使用的都是git进行版本控制，而在管理的过程中，commit的规范可以更加清楚，方便查找回溯内容

git作为一个开发人员必不可少的工具，代码提交也是日常一个频繁的操作，而commit的规范和约束则很有必要

**例子：**每次commit message 都包括三部分：header（必需的）、body和footer

```shell
<type>[optional scope]: <subject>
// 空一行
[optional body]
// 空一行
[optional footer(s)]
```

##### 规范说明

`type`提交的类别，必须是以下类型中的一个

```bash
feat: 新功能（feature）
fix：修补bug
docs：文档修改
style：样式(不影响代码运行的变动)，标点符号的更新
refactor：重构（既不是新增功能，也不是修改bug的代码变动）
perf：改进性能的代码
test：增加测试或更新已有的测试
chore：构建过程或辅助工具的变动（非src或test文件更新）
revert：回滚到上一个版本
merge：代码合并
sync：同步主线或分支bug
build：构建环境修改（maven、npm）
ci：ci配置，脚本文件等更新
```

`sope`可选，表示影响的范围、功能、模块

`subject`必填，简单说明，不超过50个字

`footer`选填，用于关联issus，或BREAK CHANGE

BREAKING CHANGE，必须是大写，表示引入了**破坏性API**，通常是一个大版本的改动，BREAKING CHANGE：之后必须提供描述，下面一个包含破坏性变更的提交示例

```bash
feat: allow provided config object to extend other configs

BREAKING CHANGE: `extends` key in config file is now used for extending other config files
```

规范有了，那么如何进行约束呢，目前有两种方式：

* 在idea中使用
* git bash中使用

## bash使用

Commitizen是一个nodejs工具，通过交互的方式，生成符合规范的git commit

#### 安装

```powershell
# 全局安装
npm install -g commitizen
# 安装适配器，使用Angular的commit message格式
npm install -g cz-conventional-changelog
```

#### 使用

```shell
# 添加修改到暂存区
git add .
# 使用工具
git cz
PS C:\Users\1\Desktop\SemiautoMachine> git cz
cz-cli@4.2.2, cz-conventional-changelog@3.3.0

? Select the type of change that you're committing: (Use arrow keys)
> feat:     A new feature
  fix:      A bug fix
  docs:     Documentation only changes
  style:    Changes that do not affect the meaning of the code (white-space, formatting, missing semi-colons, etc)
  refactor: A code change that neither fixes a bug nor adds a feature
  perf:     A code change that improves performance
  test:     Adding missing tests or correcting existing tests
#上下键选择，然后根据提示生成message
```



## idea插件（git commit template插件）

由于idea实在是太强了太强了，基本上很多好用的插件都会有，而git commit规范插件也不例外

推荐：git commit template

安装方法：在idea的plugins管理中，直接下载插件

![image-20201124105711609](https://xie-cloud-oss.oss-cn-shanghai.aliyuncs.com/images/image-20201124105711609.png)

### 使用方法

安装完成后，点击 git commit按钮

![image-20201124105849805](https://xie-cloud-oss.oss-cn-shanghai.aliyuncs.com/images/image-20201124105849805.png)

会有一点小变化，出现模板的按钮，点击就能进入该插件的规范选择

### 页面显示

![image-20201124110319144](https://xie-cloud-oss.oss-cn-shanghai.aliyuncs.com/images/image-20201124110319144.png)

##### 参数详解

