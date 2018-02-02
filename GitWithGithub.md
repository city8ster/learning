# Git 和 Github
git clone [url]

git config --global user.name
git config --global user.email
git config --list

git status
git add
git commit -m 'xxx'
git commit -a -m 'xxx'

git diff
git diff --cached
git diff --staged
git diff master

git reset HEAD [fileName]
git checkout -- [fileName]
git commit -amend

git rm [fileName]
git rm -f [fileName]
git rm --cached [fileName]

git checkout [commitID] [fileName]
git reset --hard [commitID]
git reset --hard HEAD^
git reset --hard HEAD~[num]
git reflog

git remote -v
git push origin master

git fetch
git diff master origin/master
git merge origin/master
git pull

git branch -d [branchName]
git branch --merged
git branch --no-merged
git checkout -b [branchName]
git merge

git push
git tag

fork
pull request

三个区:
  工作区
  暂存区
    - 作为过渡层
    - 避免误操作
    - 保护工作区和版本区
    - 分支处理
  版本区
  
创建组织：Github 上创建
创建博客：Github 上创建，https://pages.github.com/

总结：
  - 如何深入？
  - 技巧
  - 资源
    http://git.oschina.net/progit/
    http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000

