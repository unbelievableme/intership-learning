## 0.tips:

**[bg]:** 准备使用git工具的背景  
**[end]:** 该阶段完成后需要等待的事情  
**[action]:** git以外的操作,例如修改代码

## 1.first online
**[bg]:**  first push code  
git diff xxx  #查看修改  
git add xxx  #添加到暂存区  
git commit -m xxx #添加到本地库存  
git pull -r #将目前远程的库和本地合并  
git push origin HEAD:refs/for/master #提到gerrit等待review 
**[end]:** waiting review 
  
分支开发：  
git clone #默认是本地master分支与origin/master对应
git branch branchname #创建分支
git checkout branchname #切换分支命令
git branch列出本地分支
git branch -a 列出本地/远程分支
git fetch origin branch1:branch2  #将远程branch1与本地branch2分支对应起来
git pull origin branch1:branch2 #将远程branch拉下来，再与branch2合并,相当于git fetch和git merge
##在checkout到其他分支之前，一定要git commit
##在git push origin test之前一定要git pull -r
git branch —set-upstream-to=origin/<branch>feature/investment_be
git commit --amend #重新提交

git代码不入库:
vim  .git/info/exclude—>添加文件信息.  
git update-index --assume-unchanged PATH
case : 前者是ignore untracked的文件后者是ignore已经入过库的文件

Git常用配置  
/etc/.gitconfig ——————对所有用户有用
~/.gitconfig———————对该用户有用
项目目录/.git/config———对该用户下的该项目有用
[alias]——缩写
[user]———配置用户名和密码
.gitignore配置同上原理


修改提交历史
git rebase -i HEAD^^ #真滴好用 


git配置多个ssh链接  
ssh-keygen -t rsa -C "yourmail@gmail.com"  
[action]Enter file in which to save the key (): ～/.ssh/id_rsa_github<剩下两个直接回车>  
eval $(ssh-agent -s)  
ssh-add ~/.ssh/id_rsa_github  
[action]在～/.ssh目录下创建名为config的文件
```
    ...
    Host github.com
    HostName github.com
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa_github
```
chmod 644 ~/.ssh/config  
[action]在github上添加公钥



gerrit冲突解决
git checkout master #选择分支
git fetch origin #
git rebase origin/master #
git add dev/controller/web/index.php #这只是一个举例，即要先add操作
git rebase —continue
git push origin HEAD:refs/for/master #OK了

push -f
git push -f origin HEAD:test

Revert 和reset


操作
Push abandon cherry-pick push！！！！close  

## 2.he gives me review-1
**[bg:]** code review-1  
**[action:]** 在gerrit平台上查看reviewer批注，本地修改代码  
git add . # 将所有改动添加到暂存区  
git commit --amend --no-edit #将本次改动合并到上次提交，并且commit msg不变  
git push origin HEAD:refs/for/master  
**[end]:** waiting second review

## 3.cannot merge
**[bg]:** code review+2 and gerrit show cannot merge  
git pull -r # 拉去新的代码  
[action]提示conflict,修改代码解决冲突  
git commit --amend --no-edit #合并代码  
git push origin HEAD:refs/for/master  
**[action]:** 快速code review+2,在gerrit平台上submit  
git pull -r # 拉取新的代码  
git log # 确保HEAD -> master和origin/master分支对齐  
**[end]:** 上线平台上部署代码，正式上线

## 4.I want to work quickly
```
...
git config --global alias.st status
...
```
or
```
vim ~/.gitconfig    
[alias]
    co = checkout
    ci = commit
    br = branch
    st = status
    cin = commit --amend --no-edit
[user]
    name = Your Name
    email = your@email.com
```
## 5.hint: ...review...closed
**[bg]:** 在gerrit平台上abandon某个提交,在本地又重新push了这个提交  
**[action]** 提示:hint： ...review...closed  
git ci --amend  
**[action]** 弹窗提示编辑commit msg,删除掉change_ID,保存退出  
git cin    
**[action]** 重新生成了change_id  
git push origin HEAD:refs/for/master  
**[end]**  push成功


## 6.a big demand is coming







