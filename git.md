##配置
1. 系统配置级别，文件路径 /etc/gitconfig，git config --system 会读写此文件。
2. 用户配置级别，文件路径 ~/.gitconfig、~/.config/git/config，git config --global 会读写此文件，windows系统一般为 C:\Users\$USER\.gitconfig
3. 当前仓库配置级别，.git/config
每一个级别的配置覆盖上一级别的配置。
####配置用户信息
```bash
git config --global user.name "dailei"
git config --global user.email 203328517@qq.com
```
####文本编辑器
```bash
git config --global core.editor emacs
```
####检查配置信息
```bash
git config --list
```
####获取帮助
```bash
git help <verb>
git <verb> --help
man git-<verb>

git help config
```
##仓库基本操作
```bash
#本地初始化
git init

#克隆
git clone https://github.com/xxx/aaa

#克隆重命名
git clone https://github.com/xxx/aaa bbb
```
####检查当前文件状态
git状态有
- 未跟踪
- 未修改
- 已修改
- 已暂存
新创建的文件处于 **未跟踪** 状态，使用 git add 后处于已 **跟踪状态** 并放入**暂存区**待提交
未修改的文件被修改后处于已修改状态，使用 git add 后会放入暂存区待提交
提交动作其实就是对当前暂存区做快照
```bash
#查看状态
git status

#查看简易状态
git status -s

# M   被修改但是未放入暂存区
#M    被修改并放入了暂存区
#MM   被修改并提交到暂存区后，又在工作区被修改了
#A    新添加到暂存区
#??   新添加未跟踪的文件
```
####忽略文件
```bash
*.[oa]      #忽略.o  .a 结尾的文件
*~          #忽略~结尾的文件
```

```bash
# no .a files
*.a
# 即使忽略了所有.a文件，但是除了lib.a
!lib.a
# 只忽略当前目录的 TODO 文件
/TODO
# 忽略build目录下的所有
build/
# 忽略 doc/notes.txt, 但是不忽略 doc/server/arch.txt
doc/*.txt
# 忽略所有的doc/目录下的 .pdf 文件
doc/**/*.pdf
```

有些文件无需纳入git管理，可以写到 .gitignore 文件
.gitignore规范如下
1. 空行或者#开头的忽略
2. 可以使用标准的glob模式匹配
3. 匹配模式可以使用 (/) 开头防止递归
4. 匹配模式可以使用 (/) 结尾指定目录
5. (!) 可以取反

####查看修改
git diff 默认比较的是当前文件与暂存区快照之间的差异，也就是修改之后还没暂存起来的变化内容
若要查看已暂存的这要加上 --staged 或者 --cached
```bash
git diff
git diff --staged
```

####提交更新
git commit 运行后，会跳出编辑窗口让你输入注释，也可以带上 -m 参数直接在命令行搞定
带上 -a 参数，会自动暂存已经跟踪的文件并提交，省去了 git add 操作
提交时记录的是放在暂存区的快照，任何还未暂存的仍然保持已修改状态

**提交其实就是对当前的暂存区做快照**
```bash
git commit
git commit -m ''
git commit -a -m '' #直接暂存已经跟踪的文件并提交
```

####移除文件
如果想删除已经修改但是未暂存的文件，需要带上 -f ，因为删除后不能恢复
```bash
rm xxx.txt      #删除文件，该文件未提交到暂存区
git rm xxx.txt  #删除文件并提交到暂存区
```
如果想从暂存区移除，但是保留当前文件在磁盘，则带上 --cached
此时当前文件变为未被跟踪状态
```bash
git rm --cached  xxx.txt

git rm log/\*.log   #删除log目录下所有.log结尾的文件
git rm \*~          #删除当前目录~结尾的文件
```

####移动文件
```bash
git mv aa.txt bb.txt
#类似于下面
mv aa.txt bb.txt
git rm aa.txt
git add bb.txt
```

####查看提交历史
git log 可以展示所有的提交的校验和、作者、电子邮件地址、提交时间以及提交说明
```bash
git log
-p      显示内容差异
-num    展示多少条历史
--stat  统计信息

#不同显示格式
--pretty=oneline/short/full/fuller
--pretty=format:""

#一般简易查看分支情况
git log --decorate --graph --all --oneline
```

####撤销提交
有时候提交后发现还有些文件没提交，可以使用如下命令将暂存区合并到上一次提交
```bash
git commit --amend -m 'xxx'
```

####撤销对于文件的修改
```bash
git restore --staged xx.txt         #从暂存区移除，变为未暂存状态
git restore --staged .              #从暂存区移除所有，变为未暂存状态
git restore xx.txt                  #从暂存区恢复当前文件，当前修改丢失

git checkout -- xx.txt             #从暂存区恢复当前文件，当前修改丢失
git checkout -- .                  #从暂存区恢复所有文件，所有修改丢失
```
####回退清除
```bash
#清除暂存区，变为未暂存状态，同 git restore --staged <file>
git reset HEAD <file>

#清除暂存区
git reset HEAD --

#HEAD表示当前版本，HEAD^ 上一个版本，HEAD^^ 上上个版本，HEAD~20 网上20个版本
#回退到上一个版本
git reset --hard HEAD^

#通过版本号跳到特定版本,版本号不必写全，写前面几个就可以，只要不重叠
git reset --hard commid
```



####远程仓库
```bash
git remote -v       #查看远程仓库的url和简写名称
git remote add <shortname> <url>    #添加一个新的远程git仓库
git fetch [remote-name]             #拉取不合并

git remote rename xx xxx        #重命名

git push [remote-name] [branch-name]    #推送到远程仓库
git pull                                #拉取远程分支并尝试合并

```

####打标签
即为某一个重要的提交打上标签，方便以后从暂存区快照中查找
```bash
git tag         #列出标签
git tag -l 'xx*'    #过滤列出标签

#轻量标签(没有注释，只是引用)，默认为最近一次提交
git tag [tag_name] [commit_id]

#附注标签(有注释，git数据库中完整的对象，包含打标签者名字、电子邮件地址、日期时间等)
git tag -a v1.4 -m 'comment' [commit_id]

#查看标签信息
git show v1.4

#推送标签到远程
git push origin v1.4

#推送所有标签
git push origin --tags

#检出标签到新建分支
git checkout -b xxx v1.4
```

####创建别名
```bash
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status

git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1'
```

####分支
```bash
git branch
git branch [branch_name]
git checkout [branch_name]
git checkout -b [branch_name]

git branch -d [branch_name]

#分支合并
git checkout master
git merge dev       #将dev合并到master

#查看合并的分支或者未合并的分支
git branch --merged
git branch --no-merged
```

####跟踪分支
使用 git pull 时候会自动识别去哪个服务器上抓取、合并到哪个分支
当克隆一个仓库，一般会自动创建一个跟踪origin/master的master分支
```bash
git checkout -b [branch] [remotename]/[branch]

#本地分支sf 跟踪远程的serverfix分支
git checkout -b sf origin/serverfix

git checkout serverfix
#默认如下
git checkout --trace origin/serverfix

##查看设置的所有跟踪分支
git branch -vv

#删除远程分支
git push origin --delete serverfix
```

####保存现场
```bash
#存储当前的工作坏境
git stash

#此时查看状态，当前工作环境是干净的，然后就可以去其他分支去改
#改完之后再回到这个分支
git status

#查看有哪些 stash，工作现场
#stash@{0}: WIP on dev: f52c633 add merge
git stash list

#恢复到指定的 stash
git stash apply stash@{0}
#删除stash
git stash drop stash@{0}

#恢复最近的 stash，并删除 stash
git stash pop
```