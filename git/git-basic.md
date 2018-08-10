- git，gerrit，jenkins  
    - git只是一个版本管理工具  
    - gerrit是一个代码审查工具，方便team人员在平台上进行代码review  
    - jenkins是一个持续集成工具
gerrit+jenkins代码杠杠滴  


- get rebase
```
git rebase -i HEAD^^ #可以更改最近两个提交的顺序
```

- git branch  
```
git branch test —新建分支
git checkout test ——切换分支
git co -b test  ———上面两条命令的合并效果
```

- git diff  
```
git diff —diff-filter=ACM  (A:添加，C:复制，M:修改)
Git diff  --name-only ---只显示文件名
Git diff HEAD^显示上次提交的更改
```

- rebase,fetch,pull的对比区别  
    - fetch：将远端分支拉到本地，之后可以进行merge或者rebase  
    - pull：等于fetch+merge可能会有conflict需要解决  
    - rebase：和pull有点像，区别是rebase看起来像是顺序开发，牛皮的rebase  -i

