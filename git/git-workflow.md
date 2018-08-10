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
**[bg]:** 有一个较大的需求,需要前后端多次代码提交更新,这时候就需要一个开发分支来支持  
git co -b dev  
git push origin dev:feature/dev #将本地的dev分支推到远端,相当于在远端建立feature/dev分支  
**[action]:** 在本地dev分支上进行feature开发  
git push origin HEAD:feature/dev #代码提交到feature/dev分支上  
**[action]:** 开发结束后,代码都在远端feature/dev上,接下来将提交合并到master  
git pull -r #在本地dev分支上将代码拉到最新  
git rebase origin/master  
git push origin HEAD:master  


## 7.I don't want add this file  
**[bg]:** 新建的本地配置文件不入git库  
vim  .git/info/exclude  #添加文件名  
or  
vim .gitignore   
**[bg]:** 修改的配置文件不入git库  
git update-index --assume-unchanged PATH  


## 8.I want to push to github besides companty gitlab  
**[bg]:** 本地添加git配置,可以push到公司的gitlab,也可以push到github  
ssh-keygen -t rsa -C "yourmail@gmail.com"   
**[action]:** 注意保存公钥和私钥的path  
eval $(ssh-agent -s)   
ssh-add ~/.ssh/id_rsa_github  
**[action]:** 在～/.ssh目录下创建名为config的文件  
```
    ...
    Host github.com
    HostName github.com
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa_github
```
chmod 644 ~/.ssh/config  
**[action]** 在github上添加公钥  
**[end]:** 可以愉快的push到github了

