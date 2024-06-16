
# guochaolang.github.com

### 个人主页，包含个人介绍，整理写技术文章
使用Jekyll Gitbook Theme 作为个人主页主题

### Git使用流程记录
* 下载远程仓库 git clone https://xxxxxx.com.git
	
* 添加修改文件 git add .  [点表示所有也可写指定文件]

* 配置用户名和邮箱 global全局 local为本次
git config --global user.email "you@example.com"
git config --global user.name "Your Name"

* 提交修改 添加提交注释 git commit -m 'change'

* 推送提交到远程仓库   git push -u orign master >>*master 主分支，可以变更为其他分支* 

### Git使用流程记录 其他功能
1. 查看分支 git branch -a 
切换git checkout -b v1 origin/v1
切换回master
git checkout master
2. 查看本地修改，和远程仓库 git status
3. 查看修改内容 git diff

4. 查看是否连接远程 git remote -v //这里是查看，我们的是否链接到了远端
5.初始化 git init //初始化git
6. 连接远程 git remote add origin xxxxxx //链接到远程分支xxxxxx（git上面的仓库地址）